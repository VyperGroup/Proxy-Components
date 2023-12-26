Proxy Components
A lightweight and refreshing abstraction from proxies. This minimizes project dependence. Proxy components allow you to do everything you want to do with proxies in vanilla HTML.

How to setup
This will include a SW bundle that you must include, if you want these features to work: Bare, proxy, and proxy middleware switching. Bare and proxy middleware switching will work by switching the BareClient import in a proxy bundle to one that will recieve bare servers through messages. The attributes in the proxied frame are in place to make this be done easily.

pc is the namespace, which means “Proxy Components”

Omnibox
This will be an extended Input box with customizable search suggestions. Obviously, it will be unstyled by default to allow freedom of the proxy site designer.

Examples
HTML element
<omnibox
  provider="..."
></omnibox>
Attributes
provider - Which search engine to use to provide search suggestions.
For the provider “Google”, I will not implement my own code for it, rather I will use a module from Ferrochrome (My Chromium browser port). Other providers will be implemented normally, with full rich results.

Proxied iframe
It will extend the iframe. This is essentially an iframe, but without any restricitons. If you choose to provide CORS attributes, it will be emulated inside of the proxy through a custom middleware built for this. If the service worker registration fails, the proxy will automatically be ran SW-less. This is my favorite feature of Proxy Components.

Examples
HTML element
<script type="application/json" id="pc-defaults">
{
    ...
}
</script>

<!-- "...(,)" means comma seperated -->
<pc:iframe
  proxy="...(,)"
  bareServers="...(,)"
  middlewareRepos="...(,)"
  middlewarePackages="...(,)"
  middlewares="...(,)"
  searchEngine="..."
  profile="..."
  ignoreDefaults="(...attrNames)(,)"
></pc:iframe>
Proxy attributes
proxy - Which proxy you want to use. If unset, it will use the default proxy on the page. You can either have this as a URL or a name. If you want to use a name you will have to set a window.proxyBundles map. This will support any proxy. This will allow the site to give the user more control over what the proxy is using and not have to write routes with SW messaging to determine which proxy to switch to. It will do that all for you.
profile - Different contextual identities. Unfortunately, no proxies support contextual identies yet, but I hope to soon with aero.
Attributes
middlewareRepos - URL to a middleware Repo .xml file
middlewarePackages - {Name}@{Version #} to retrieve from the Repos
middlewares - URL to a middleware bundle. This is allows you to add middleware, regardless if it is in a repo. You can still import middleware from a repo this way. For example. ${repo path}/{Name}@{Version #}/dl.
Other attributes
ignoreDefaults - Use the options (attributes) provided in the HTML element even if a default is found
JS APIs
From JS you will be able to customize how aero’s sandboxing works.

For example with all (semi)-interception proxies:

<script src=".../aero.config.js"></script>
<script src=".../dynamic.config.js"></script>
<script src=".../uv.config.js"></script>
const prefix = "/proxies/";
// Every time this Bundle is set, a message is sent to the SW to include this as an option.
pc.proxies.set("aero", {
  // This will let you customize where it is routed to. This can be a string or RegExp.
  endpoint: prefix + "aero",
  // Path to a the sw of aero. This can be external.
  path: __aero$config.bundle,
  // This will be the actual function that handles the "fetch" request
  handle: exports => exports.aeroHandler,
  urlEncoder: __aero$config.encodeUrl,
  urlDecoder: __aero$config.decodeUrl,
},
});
pc.proxies.set("dynamic", {
  endpoint: prefix + __dynamic$config.assets.prefix,
  path: __dynamic$config.assets.files.config,
  handle: exports => {
    return new Dynamic().fetch;
  },
  urlEncoder: url => {
    switch (__dynamic$config.encoding) {
      case "xor":
        return __dynamic.xor.encode(url)
      case "plain":
        return __dynamic.plain.encode(url)
      case "aes":
        return __dynamic.aes.encode(url)
      case "none":
        return self.__dynamic.none.encode(url)
    }
  },
  urlDecoder: url => {
    switch (__dynamic$config.encoding) {
      case "xor":
        return self.__dynamic.xor.encode(url)
      case "plain":
        return self.__dynamic.plain.encode(url)
      case "aes":
        return self.__dynamic.aes.encode(url)
      case "none":
        return self.__dynamic.none.encode(url)
    }
  },
},
});
pc.proxies.set("uv", {
  endpoint: prefix + "uv",
  path: __uv$config.bundle,
  handle: exports => {
    return new UVServiceWorker().fetch
  },
  urlEncoder: __uv$config.encodeUrl,
  urlDecoder: __uv$config.decodeUrl,
},
});

// attrName, value
pc.setDefault("proxy, aero") // A little biased I would say
Others
Proxy Redirect
This will be a script that you can import that will takeover any redirection attributes, like href, and will redirect you to the proxified version of a site, if that site is blocked on your network, or else function as normal. It will do this by using the click to determine, if the active element is a link, and will perform the redirection accordingly.

Client-side implementation of Proxy Lock
This will do exactly what the title says. This is for environments where you don’t get much control over the backend.
