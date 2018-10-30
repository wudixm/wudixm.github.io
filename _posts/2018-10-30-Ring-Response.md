# Creating responses

[Edit](https://github.com/ring-clojure/ring/wiki/Creating-responses/_edit)[New Page](https://github.com/ring-clojure/ring/wiki/_new)

[原文](https://github.com/ring-clojure/ring/wiki/Creating-responses)

Nico Ritschel edited this page on 10 Aug 2016 · [10 revisions](https://github.com/ring-clojure/ring/wiki/Creating-responses/_history)

You can create Ring response maps manually (see [Concepts](https://github.com/ring-clojure/ring/wiki/Concepts)), but the [ring.util.response](http://ring-clojure.github.com/ring/ring.util.response.html)namespace contains a number of useful functions to make this task easier.

The `response` function creates a basic "200 OK" response:

```
(response "Hello World")

=> {:status 200
    :headers {}
    :body "Hello World"}
```

You can then use functions like `content-type` to alter the base response to add in additional headers and other components:

```
(content-type (response "Hello World") "text/plain")

=> {:status 200
    :headers {"Content-Type" "text/plain"}
    :body "Hello World"}
```

Special functions also exist to create redirects:

```
(redirect "http://example.com")

=> {:status 302
    :headers {"Location" "http://example.com"}
    :body ""}
```

And to return static files or resources:

```
(file-response "readme.html" {:root "public"})

=> {:status 200
    :headers {}
    :body (io/file "public/readme.html")}
(resource-response "readme.html" {:root "public"})

=> {:status 200
    :headers {}
    :body (io/input-stream (io/resource "public/readme.html"))}
```

More information on these functions and others can be found in the [ring.util.response API documentation](http://ring-clojure.github.com/ring/ring.util.response.html).