---
title: I18n.backend.store_translations のメモ
date: 2012-08-01 01:15:08 +0900
---

今日社内の Crafting Rails Applications 読書会でこういうコードがでてきた。


    test "model_name.human uses I18n" do
      begin
        I18n.backend.store_translations :en,
          :activemodel => { :models => { :sample_mail => "My Sample Mail" } }

        assert_equal "My Sample Mail", model.class.model_name.human
      ensure
        I18n.reload!
      end
    end


I18n.backend.store_translations を使うことで I18n のテストをしたい時にいちいち fixture とか yaml とかで I18n の文言を用意しなくてもテストができるらしい。
アプリケーションではなくライブラリなどで I18n のテストを書くときはこの方法がよさそうです。
begin...ensure で囲んであるのはこのテストが終わったときに、reload して格納したデータをリセットするためとのこと。

で今 [Enumerize](https://github.com/brainspec/enumerize) のコード見てたら I18n.backend.store_translations のラッパーを用意していて便利げだった。
テスト書いてて、begin...ensure でごちゃごちゃする時はこういう風に書いたりして
block で渡したりするとキレイになって他でも使えそう。

    module MiscHelpers
      def store_translations(locale, translations, &block)
        begin
          I18n.backend.store_translations locale, translations
          yield
        ensure
          I18n.reload!
        end
      end
    end

    class MiniTest::Spec
      include MiscHelpers
    end

    it 'returns nil as translation when value is nil' do
      store_translations(:en, :enumerize => {:foo => {:a => 'a text'}}) do
        klass.enumerize(:foo, :in => [:a, :b])
        object.foo_text.must_equal nil
      end
    end
