1.1.2
-----

* 2012/05/28: Fixed cache key generation bug when using Mongoid 3, [Marc MacLeod](http://github.com/marbemac)

1.1.1
-----

* 2012/03/21: Fixed a bug in caching/invalidating referenced polymorphic documents, [Frank Macreery](http://github.com/macreery)

1.1
---

* 2012/02/29: Added support for versioning, [Daniel Doubrovkine](http://github.com/dblock)

1.0
---

* 2011/11/15: Initial implementation, [Aaron Windsor](http://github.com/aaw)
* 2011/11/29: Added support for `:markdown`, [Frank Macreery](http://github.com/macreery)
* 2012/02/17: Added `Mongoid::CachedJson::configure`, [Daniel Doubrovkine](http://github.com/dblock)
* 2012/02/17: Retired support for `:markdown` in favor of `Mongoid::CachedJson::transform`, [Daniel Doubrovkine](http://github.com/dblock)
* 2012/02/20: Initial public release
