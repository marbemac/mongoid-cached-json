Mongoid::CachedJson [![Build Status](https://secure.travis-ci.org/dblock/mongoid-cached-json.png)](http://travis-ci.org/dblock/mongoid-cached-json)
===================

Typical *as_json* definitions may involve lots of database point queries and method calls. When returning collections of objects, a single call may yield hundreds of database queries that can take seconds. This library mitigates the problem by implementing a module called *CachedJson*.

CachedJson enables returning multiple JSON formats from a single class and provides some rules for returning embedded or referenced data. It then uses a scheme where fragments of JSON are cached for a particular (class, id) pair containing only the data that doesn't involve references/embedded documents. To get the full JSON for an instance, CachedJson will combine fragments of JSON from the instance with fragments representing the JSON for its references. In the best case, when all of these fragments are cached, this falls through to a few cache lookups followed by a couple Ruby hash merges to create the JSON.

Using Mongoid::CachedJson we were able to cut our JSON API average response time by about a factor of 10.

Resources
---------

* [Need Help?](http://groups.google.com/group/mongoid-cached-json)
* [Source Code](http://github.com/dblock/mongoid-cached-json)
* [Travis CI](https://secure.travis-ci.org/dblock/mongoid-cached-json)

Quickstart
----------

Add `mongoid-cached-json` to your Gemfile.

    gem "mongoid-cached-json"

Include `Mongoid::CachedJson` in your models.

``` ruby
class Gadget
  include Mongoid::CachedJson

  field :name
  field :extras

  belongs_to :widget

  json_fields \
    :name => { },
    :extras => { :properties => :public }

end

class Widget
  include Mongoid::CachedJson

  field :name
  has_many :gadgets

  json_fields \
    :name => { },
    :gadgets => { :type => :reference, :properties => :public }

end
```

Invoke `as_json`.

``` ruby
  Widget.first.as_json # the `:short` version of the JSON, `gadgets` not included
  Widget.first.as_json({ :properties => :short }) # equivalent to the above
  Widget.first.as_json({ :properties => :public }) # `:public` version of the JSON, `gadgets` returned with `:short` JSON, no `:extras`
  Widget.first.as_json({ :properties => :all }) # `:all` version of the JSON, `gadgets` returned with `:all` JSON, including `:extras`
```

Configuration
-------------

By default `Mongoid::CachedJson` will use an instance of `ActiveSupport::Cache::MemoryStore` in a non-Rails and `Rails.cache` in a Rails environment. You can configure it to use any other cache store.

``` ruby
Mongoid::CachedJson.configure do |config|
  config.cache = ActiveSupport::Cache::FileStore.new
end
```

Definining Fields
-----------------

Mongoid::CachedJson supports the following options.

* `:hide_as_child_json_when` is an optional function that hides the child JSON from `as_json` parent objects, eg. `cached_json :hide_as_child_json_when => lambda { |instance| ! instance.is_secret? }`

Mongoid::CachedJson field definitions support the following options.

* `:definition` can be a symbol or an anonymous function, eg. `:description => { :definition => :name }` or `:description => { :definition => lambda { |instance| instance.name } }`
* `:type` can be `:reference`, required for referenced objects
* `:properties` can be one of `:short`, `:public`, `:all`, in this order
* `:markdown` will convert HTML into markdown, eg. `:dont_trust_this_field => { :markdown => true }`

Turning It Off
--------------

Taking part in the whole Mongoid::CachedJson optimization scheme is entirely optional: you can still write *as_json* methods where it makes sense. You can also set `ENV['DISABLE_JSON_CACHING']=true`, which switches all of this caching off entirely in case this turns out not to be The Solution To All Of Your Problems (TM).

Contributing
------------

Fork the project. Make your feature addition or bug fix with tests. Send a pull request. Bonus points for topical branches.

Copyright and License
---------------------

MIT License, see [LICENSE](LICENSE.md) for details.

(c) 2012 [Art.sy Inc.](http://artsy.github.com) and [Contributors](HISTORY.md)
