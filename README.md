# Service Worker Elements
A set of Polymer elements that simplify service worker registration and caching, powered by the
[`sw-toolbox` library](https://github.com/googlechrome/sw-toolbox).
Full documentation is available at https://PolymerElements.github.com/service-worker/index.html

# Considerations

## Top-level `sw-import.js`
While `<service-worker>` abstracts away many of the details of working with service workers, there
is one specific requirement that developers must fulfill: it needs to register a JavaScript file
located at the top-level of your site's web root. (Details behind this requirement can be found in
the Service Worker specification [issue tracker](https://github.com/slightlyoff/ServiceWorker/issues/468#issuecomment-60276779).)

In order to use `<service-worker>`, it's recommended that you create a `sw-import.js` file in your
site's web root. The file's only contents should be
    importScripts('bower_components/service-worker/service-worker.js');
You can adjust the path to `service-worker.js` if your project has its Polymer elements
installed somewhere other than `bower_components/`.

If you have multiple subdirectories worth of pages on your site, it's recommend that you include the
`<service-worker>` element on a top-level entry page that all visitors will access first; once they
visit the top-level page and the service worker is registered, it will automatically apply to all
sub-pages, which will fall under its
[scope](https://slightlyoff.github.io/ServiceWorker/spec/service_worker/index.html#service-worker-registration-scope).

## `cacheOnly` & `cacheFirst` `defaultCacheStrategy` Considered Harmful
The [`sw-toolbox` library](https://github.com/googlechrome/sw-toolbox),
which `<service-worker-cache>` is built on, supports a number of
[caching strategies](https://github.com/googlechrome/sw-toolbox#built-in-handlers).
Two of them, `cacheOnly` and `cacheFirst`, are strongly discouraged to be used as the `defaultCacheStrategy`
for `<service-worker-cache>`. With both of those strategies, all HTTP requests, including requests for
the page which contains the `<service-worker-cache>` element, are served directly from the [Cache Storage
API](https://slightlyoff.github.io/ServiceWorker/spec/service_worker/index.html#cache-objects) without
first consulting the network for a fresh copy. Once the copy of the host page is cached,
it's extremely difficult to change the configuration of the service worker (since the configuration
depends on the page's contents), and developers could find themselves deploying sites that can never
update.

In a future release of `<service-worker-cache>`, using `cacheOnly` and `cacheFirst` as `defaultCacheStrategy`
may lead to an explicit error condition, but for the meantime, please consider a more reasonable default
(like `networkFirst`).