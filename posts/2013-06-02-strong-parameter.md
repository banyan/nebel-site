---
title: Strong Parameters の action_on_unpermitted_parameters の振る舞いについて
date: 2013-06-02 18:47:31 +0900
---

Rails4 から Rails のコアに取り込まれた Strong Parameters は、それまでの Model 側で mass assignments 対策を attr\_accessible / attr\_protected を使って行なっていたことに比べると、Controller 側で制御することは利用する側から言えば、とてもわかりやすくなったと思う。

Strong Parameters の詳細は [edgeguides.rubyonrails.org の Strong Parameters の箇所](http://edgeguides.rubyonrails.org/action_controller_overview.html#strong-parameters)を見るといいと思います。

```
$ rails g scaffold post title content:text published:boolean
```

Rails 4.0.0.rc1 で scaffold してみると

    class PostsController < ApplicationController
    # ... (略)
      def create
          @post = Post.new(post_params)
    # ... (略)
      private
    # ... (略)
        # Never trust parameters from the scary internet, only allow the white list through.
        def post_params
          params.require(:post).permit(:title, :content, :published)
        end

こんな感じで生成される。

require で指定されたキーがない場合は例外が起こる。

    > ActionController::Parameters.new(person: { name: 'Francesco' }).require(:person)
    # => {"name"=>"Francesco"}

    > ActionController::Parameters.new(person: nil).require(:person)
    # => ActionController::ParameterMissing: param not found: person

    > ActionController::Parameters.new(person: {}).require(:person)
    # => ActionController::ParameterMissing: param not found: person

一方 permit で指定したキー (カラム名に相当する) は configure option で設定しない限りログだけ吐かれて例外が起こらない。

    > ActionController::Parameters.new(person: { name: 'Francesco' }).require(:person).permit(:foo)
    Unpermitted parameters: name
    => {}

該当のソースは [actionpack/lib/action_controller/metal/strong_parameters.rb@3121904](https://github.com/rails/rails/blob/31219045ebde6acd26b0f023d006836fc2c10f88/actionpack/lib/action_controller/metal/strong_parameters.rb) で (SHA-1 は 2013/06/02 の HEAD)

    247     def permit(*filters)
    248       params = self.class.new
    249
    250       filters.flatten.each do |filter|
    251         case filter
    252         when Symbol, String
    253           permitted_scalar_filter(params, filter)
    254         when Hash then
    255           hash_filter(params, filter)
    256         end
    257       end
    258
    259       unpermitted_parameters!(params) if self.class.action_on_unpermitted_parameters
    260
    261       params.permit!
    262     end
    ...
    340       def unpermitted_parameters!(params)
    341         unpermitted_keys = unpermitted_keys(params)
    342         if unpermitted_keys.any?
    343           case self.class.action_on_unpermitted_parameters
    344           when :log
    345             name = "unpermitted_parameters.action_controller"
    346             ActiveSupport::Notifications.instrument(name, keys: unpermitted_keys)
    347           when :raise
    348             raise ActionController::UnpermittedParameters.new(unpermitted_keys)
    349           end
    350         end
    351       end

259行目に指定されている action\_on\_unpermitted\_parameters は [actionpack/lib/action_controller/railtie.rb@312190](https://github.com/rails/rails/blob/31219045ebde6acd26b0f023d006836fc2c10f88/actionpack/lib/action_controller/railtie.rb) でデフォルトの振る舞いが指定されている。

    22     initializer "action_controller.parameters_config" do |app|
    23       options = app.config.action_controller
    24
    25       ActionController::Parameters.permit_all_parameters = options.delete(:permit_all_parameters) { false }
    26       ActionController::Parameters.action_on_unpermitted_parameters = options.delete(:action_on_unpermitted_parameters) do
    27         (Rails.env.test? || Rails.env.development?) ? :log : false
    28       end
    29     end

27行目にあるように Rails.env が test か development の時は log に 'Unpermitted parameters:' というログが出るだけでその値は nil になる。一方で production でも nil になる。

結論から先に書くべきだけど、なんでこんな振る舞いになっているかと思った。例えば Rails3 から Rails4 に upgrade して Strong Parameters に対応させる時 permit 忘れのバグがたくさん出そうだし、テストコードでそういうものをすべてカバーできるほどのテストはなかなか書いてないと思うのでデフォルトは :raise でいいのではないかと思いました。(実際自分の遊びのアプリケーションでもバグが出た)

この振る舞い自体はこの [commit](https://github.com/rails/rails/commit/57126ee5e392a4dc2eed22963d25020a48a93413) で変更されていて、そしてこのことが議論されているのがこの [PR](https://github.com/rails/strong_parameters/pull/75) になる。rails/rails と rails/strong_parameters で議論が別れていて追いにくいけど多分以下のような流れになっていると思う。

1. unpermitted parameters がそのまま nil になってしまうのでなんとかできないかという [issue](https://github.com/rails/strong_parameters/issues/66) があがる
1. それを受けて、configure option として raise できるようになった PR が[これ](https://github.com/rails/strong_parameters/pull/75)。
1. config/environments 以下の development.rb や test.rb のテンプレートに ActionController::Parameters.action\_on\_unpermitted\_parameters の設定もできるようにしたほうがいいかどうかという話で DHH は「Because nobody is really ever want to change it」と[言って](https://github.com/rails/rails/pull/8752#issuecomment-11893845)、デフォルトのテンプレートの設定するところには出さないようにしている。


### まとめ

ということで非常に長くなってしまったけど、デフォルトで :raise したほうがいいかの議論はまだされてない気がする。これは PR したほうがいい気がするんだけどどうなんだろうか...

とりあえずは config/application.rb や config/environments/*.rb で以下のようにすれば permit に許可してない値が入ってきた時に例外が起こるように設定はできるので、そうしたほうが検知できていいと思う。

```
config.action_controller.action_on_unpermitted_parameters = :raise
```
