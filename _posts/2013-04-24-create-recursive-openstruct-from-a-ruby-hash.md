---
layout: post

title: "Create recursive OpenStruct from a Ruby Hash"
tags:
  - ruby
  - rails
  - protip

excerpt: "Maybe you already know and used the Ruby's [OpenStruct](http://www.ruby-doc.org/stdlib-2.0/libdoc/ostruct/rdoc/OpenStruct.html) class. I've played a bit with it to create a very easy to use configuration system for Ruby apps."

---

When I develop some ruby app, mostly rails ones, I need to manage several configuration variables (eg: API keys) and I like to store them in a unique place. So I usually end up with something like this:

{% highlight ruby %}
# config/initializers/_load_config.rb

path = File.read("#{Rails.root}/config/config.yml")

APP_CONF = ActiveSupport::HashWithIndifferentAccess.new(
  YAML.load(ERB.new(path).result)[Rails.env]
)
{% endhighlight %}

It does a good job:

* it first compiles eventual ERB tags (so you can store values in ```ENV```)
* then it loads YAML
* finally initializes a [HashWithIndifferentAccess](http://apidock.com/rails/v3.2.13/ActiveSupport/HashWithIndifferentAccess/new/class), so I can lookup keys with either a String or a Symbol

The last point is a _plus_, kindly offered by Rails' ActiveSupport library, I prefer Symbol over String for Hash keys.


## The problem

However, I knew that there's a smarter way to achieve same goal, even with some more adavantages. I started from [OpenStruct](http://www.ruby-doc.org/stdlib-2.0/libdoc/ostruct/rdoc/OpenStruct.html):

* it solves the problem of Hash key lookup by providing them as methods, for example:

{% highlight ruby %}
require 'ostruct'

hash = {a: 1, b: 2}
mystruct = OpenStruct.new hash
mystruct.a # => 1
mystruct.b # => 2
{% endhighlight %}

* it's part of the Ruby's standard library (along with YAML and ERB as well)
* it's very simple to hack ;-)

Looks excellent, right? Well, it's not *perfect*, because it doesn't handle nested hashes, in other words, here's what happen when you pass an Hash that has other hashes as values:

{% highlight ruby %}
require 'ostruct'

hash = {a: {b: 1}}
mystruct = OpenStruct.new hash
mystruct.a # => {b: 1}
mystruct.a.b # => NoMethodError: undefined method 'b' for {:b=>1}:Hash
{% endhighlight %}

Moreover, there's no way to access the original hash (well, there's one but that's another point), it might be useful when you need to pass an entire Hash.


## A solution

```OpenStruct``` already does a great part fo the *dirty job*, it only needs some change to get what we need. I called it, ```DeepStruct```:

{% highlight ruby %}
# deep_struct.rb
require 'ostruct'

class DeepStruct < OpenStruct
  def initialize(hash=nil)
    @table = {}
    @hash_table = {}

    if hash
      hash.each do |k,v|
        @table[k.to_sym] = (v.is_a?(Hash) ? self.class.new(v) : v)
        @hash_table[k.to_sym] = v

        new_ostruct_member(k)
      end
    end
  end

  def to_h
    @hash_table
  end

end
{% endhighlight %}

There's not too much to explain here, I've overridden [OpenStruct#new](http://www.ruby-doc.org/stdlib-2.0/libdoc/ostruct/rdoc/OpenStruct.html#method-c-new) method to make two basic things:

* Iterate the key/values of the passed Hash, then:
  * initialize a new ```DeepStruct``` if a given value is an Hash
  * store original key/values in @hash_table, so I can retrieve it with ```#to_h``` method

That's all we need, here's an example:

{% highlight ruby %}
require 'deep_struct'

hash = {a: {b: 1}}
mystruct = DeepStruct.new hash
mystruct.a # => #<DeepStruct b=1>
mystruct.a.b # => 1
mystruct.a.to_h # => {b: 1}
{% endhighlight %}

## Conclusion

I've said I'd mainly use this to store configs for my apps, so go back to the initial example:

{% highlight ruby %}
# config/initializers/_load_config.rb

path = File.read("#{Rails.root}/config/config.yml")

APP_CONF = DeepStruct.new(YAML.load(ERB.new(path).result)[Rails.env])
{% endhighlight %}

Enjoy and/or leave a comment :-)
