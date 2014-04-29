---
layout: post

title: "Rails4 StrongParameters with Devise and multiple resources"
tags:
  - ruby
  - rails
  - protip

excerpt: "Rails 4 will use [StrongParameters](http://rubysource.com/rails-4-quick-look-strong-parameters/) by default. However, it doesn't seem that Devise is ready for this feature, even using its [rails4 branch](https://github.com/plataformatec/devise/tree/rails4). There's a [good example](https://gist.github.com/kazpsp/3350730) that explains how to solve this problem when dealing with only one resource (eg: ```User```). I forked that code to handle more resources (eg: ```User``` and ```Admin```) without repetitions."

---

First of all, we override the controllers where we need to explicitly authorize params, in this case ```PasswordsController``` and ```RegistrationsController``` should accept ```email```, ```password``` and ```password_confirmation``` by default:

{% highlight ruby %}
# app/controllers/custom_devise/password_controller.rb

class CustomDevise::PasswordsController < Devise::PasswordsController
  def resource_params
    params.require(resource_name).permit(:email, :password, :password_confirmation)
  end
  private :resource_params
end
{% endhighlight %}

{% highlight ruby %}
# app/controllers/custom_devise/registrations_controller.rb

class CustomDevise::RegistrationsController < Devise::RegistrationsController
  def resource_params
    params.require(resource_name).permit(:name, :email, :password, :password_confirmation)
  end
  private :resource_params
end
{% endhighlight %}

Then, we need to tell Devise to use these controllers instead of the default ones:

{% highlight ruby %}
# config/routes.rb

devise_for :users, controllers: {
  registrations: "custom_devise/registrations",
  passwords: "custom_devise/passwords"
}

devise_for :admins, controllers: {
  registrations: "custom_devise/registrations",
  passwords: "custom_devise/passwords"
}
{% endhighlight %}

enjoy :-)
