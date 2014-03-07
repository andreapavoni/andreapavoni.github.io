---
layout: post

title: "Store Rails views on database"
tags:
  - ruby
  - rails
  - templates
  - gem

excerpt: "When it comes to develop a CMS, one of the main problems concerns where or how to store and organize views (templates, partials, layouts) for particular needs like different layouts for specific pages, (sub)domains or multi-languages."

---

The ideal way is to store views on a database, indeed there are some CMSs that solve this problem with similar approaches, like [Radiant](http://radiantcms.org/) and [Locomotive](http://www.locomotivecms.com/).

However, I wanted something that let me to reuse this feature on several projects (because a custom CMS may have different features), and to store only certain views, according to specific content and needs.

So, I’ve developed [Panoramic](https://github.com/apeacox/panoramic), a simple gem inspired from some ideas found in José Valim’s excellent book [Crafting Rails Applications](http://pragprog.com/titles/jvrails/crafting-rails-applications). Simply put, once Panoramic is installed, you can store your views on database. It works through an implemetation of ```ActionView::Resolver``` class: Rails will lookup views as if they were on filesystem. In controller, you’ll tell where and when to use those views, using ```prepend_view_path``` and ```append_view_path``` on a controller or action basis (they are both implemented as class and instance methods). Depending from the method you’ll use, Rails will respectively look for views on database, then it will fallback on filesystem, or viceversa. Quite easy, right?

I’ll not show any code here, because there’s a full explanation on [Panoramic’s README](https://github.com/apeacox/panoramic/blob/master/README.md). As a final note, at the moment it works only with ActiveRecord, but I’ve planned to extend support to DataMapper and Mongoid as soon as possible. It’s very easy to implement, the main problem is about testing: maybe I’ll write some rspec’s custom matchers to reuse with different ORMs, like [carrierwave](https://github.com/jnicklas/carrierwave).

#### Notes:
This article was initially published on [http://blog.andreapavoni.com/post/6488774753/store-rails-views-on-database](http://blog.andreapavoni.com/post/6488774753/store-rails-views-on-database)
