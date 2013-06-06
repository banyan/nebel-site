---
title: PORO の model に自動で ActiveModel::ForbiddenAttributesProtection を include する
date: 2013-06-06 19:45:05 +0900
---

Strong Parametes を使うには、各 model で ActiveModel::ForbiddenAttributesProtection を include してあげる必要があります。

ただ毎回手作業でやると忘れたりする可能性があるので config/initializers/strong_parameters.rb とかに

    ActiveRecord::Base.send(:include,  ActiveModel::ForbiddenAttributesProtection)

としてあげると自動で include されます。(ということを [railscasts で見ました](http://railscasts.com/episodes/371-strong-parameters))

今の自分のプロジェクトでは mongomapper を使っていて PORO (Plain Old Ruby Objects) の class なので

    def class_exists?(class_name)
      klass = Module.const_get(class_name)
      return klass.is_a?(Class)
    rescue NameError
      return false
    end

    Dir.foreach("#{Rails.root}/app/models") do |model_path|
      class_name = File.basename(model_path, ".rb").classify
      next unless class_exists?(class_name)
      klass = Object.const_get class_name
      klass.class_eval { include ActiveModel::ForbiddenAttributesProtection }
    end

こんな感じで include するようにしました。

    > p Post.included_modules
    => [ActiveModel::ForbiddenAttributesProtection,
    ...

include されていて、
前回[書いた設定](http://banyan.me/2013/06/02/1/)で ActionController::UnpermittedParameters が raise されることを確認できました。
