---
layout: post

title: "Rails 3: An improved agnostic search model using Arel"
tags:
  - rails
  - arel
  - active_record

excerpt: "In the [last post](http://andreapavoni.com/blog/2010/7/rails3-using-arel-to-make-conditional-searches-based-on-conditional-params), I explained how to make conditional searches based on conditional params. I wrote a very simple Search class that returns a real model with query done by Arel. I refactored the code to make it simpler to use."

---

{% highlight ruby %}
class Search
  attr_accessor :attributes

  def initialize(model, attrs = {})
    @attributes = {}
    @model = model
    @arel = model.arel_table

    model.columns.each do |field|
      pattr = field.name

      attrs = Hash[attrs.select {|k,v| !v.empty? }]
      searchable = !attrs[pattr].nil?

      @attributes[pattr.to_sym] = attrs[pattr] if searchable

      self.class.send(:define_method,field.name) do |cond, *value|
        attr = field.name.to_sym
        value = value.first || @attributes[attr] || nil
        @model = @model.where(@arel[attr].send(cond,value)) if searchable
      end
    end

    if block_given?
      yield self
    end
  end

  def result
    @model
  end
end
{% endhighlight %}

The constructor takes the model you want to search in, and an hash of params to use, they’ll be the request params when used from controller. Note that if some param value is an empty string, it will be ignored: this lets the visitor to leave blank some fields in the search form and make it conditional.

To save typings, when specifying conditions, you can omit the value, because it’s taken from the params hash. By the way, if you need some more control, you can specify another value. Finally, you can pass a block to make it easier to read. Here’s a real usage example from a controller:

{% highlight ruby %}
class RealtyRequestController < ApplicationController
  #... other actions ...
  def search
    @s = Search.new(RealtyRequest,params[:search]) do |s|
      # value to search defaults to params[:search][:contract_id]
      s.contract_id :eq
      s.price_max :lteq # same here
      s.price_min :gteq
      s.zone :matches, "%#{params[:search][:zone]}%" # here I look for '%param%'
      s.province :matches, "%#{params[:search][:province]}%"
      s.municipality :matches, "%#{params[:search][:municipality]}%"
      s.rooms :eq
      s.mq :eq
    end

    @realty_requests = @s.result # assign result
    @realty_requests.order(:created_at) # add order option

    # ... other rendering stuff ...
  end
end
{% endhighlight %}

When ```Search#result``` is called a model instance is returned, it can still be ordered. According to [Arel's README](http://github.com/rails/arel#readme), there’s no support for *OR* conditions yet, I hope it will be included soon.

#### Notes:
This article was initially published on [http://blog.andreapavoni.com/post/776670218/rails-3-an-improved-agnostic-search-model-using-arel](http://blog.andreapavoni.com/post/776670218/rails-3-an-improved-agnostic-search-model-using-arel)
