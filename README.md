Flushable
=========

This dependency can be used to flush an item from the cache. It allows you to invalidate any cached item like a cached ActiveRecord, a DAO query result, a cached fragment or a cached page.

##Usage

### Import the class file

Either add this line on top of every class file where you want to use it:

```php
Yii::import('ext.flushable.FlushableDependency');
```

or add `ext.flushable.FlushableDependency` right to the `import` section of your `main.php` configuration file.

### How to use the dependency

When you create the dependency you have to provide a unique `$id` wich identifies your cached content.

```php
$dependency = new FlushableDependency('myKey');
```

Now you can use it for data, fragment or page caching.

If you want to cache some ActiveRecord or DAO result the `$id` is usually the primary key. In this case you should add the model name to avoid conflicts among different cached model classes:

```php
// Cache a ActiveRecord
$dependency = new FlushableDependency($id,'Post');
$post = Post::model()->cache(3600,$dependency)->findByPk($id);
```

### How to flush a content from cache

Whenever you want to invalidate the cached data you can now call:

```php
FlushableDependency::flushItem('myKey');
```

If you used the dependency for data caching and added a model name you must provide the same name again:

```php
FlushableDependency::flushItem($id,'Post');
```

You could put this into the `afterSave()` method of your active record (change `$id` to `$this->id`) or just call it manually whenever you update the record.

You can also add an `$expire` parameter to the `flushItem()` call. It will specify how long the information about the change should be kept in the cache. So obviously this should be more than the longest time which you cache your content. The default is `3600`.

## How it works

Whenever an item is cached with this dependency it scans the cache for another key. This key is unique per cached item and composed from the two parameters you pass along to the constructor. This other cache value basically indicates *'The item has expired!'*. So if no such key is found, the item in the cache is still valid.

With a call to `flushItem()` you will create such an expiration entry in the cache for the cached item. So when the dependency is evaluated the next time it will find something now and the cached content will no longer be seen as valid.

## Changelog

### Release 1.1.2 (IMPORTANT: This update requires to flush your cache!)

* Changed dependency keys to work flawlessly with file caches (Thanks yiqing-95).

### Release 1.1.1

* Changed dependency value from `time()` to `microtime()` to fix timing issues in some scenarios

### Release 1.1.0

* Refactored class interface

### Release 1.0.1

* Initial release
