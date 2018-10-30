# data.json

[原文](https://github.com/clojure/data.json)

JSON parser/generator to/from Clojure data structures.

Follows the specification on <http://json.org/>

## Releases and Dependency Information

Latest stable release is [0.2.6](https://github.com/clojure/data.json/tree/data.json-0.2.6)

[Leiningen](http://leiningen.org/) dependency information:

```
[org.clojure/data.json "0.2.6"]

```

[Maven](http://maven.apache.org/) dependency information:

```
<dependency>
  <groupId>org.clojure</groupId>
  <artifactId>data.json</artifactId>
  <version>0.2.6</version>
</dependency>

```

[Gradle](http://www.gradle.org/) dependency information:

```
compile "org.clojure:data.json:0.2.6"

```

Other versions:

- [All Released Versions](http://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22org.clojure%22%20AND%20a%3A%22data.json%22)
- [Development Snapshots](https://oss.sonatype.org/index.html#nexus-search;gav%7Eorg.clojure%7Edata.json%7E%7E%7E)
- [Development Snapshot Repositories](http://dev.clojure.org/display/doc/Maven+Settings+and+Repositories)

## Usage

[API Documentation](http://clojure.github.com/data.json/)

Example usage:

```
(ns example
  (:require [clojure.data.json :as json]))

```

To convert to/from JSON strings, use `json/write-str` and `json/read-str`:

```
(json/write-str {:a 1 :b 2})
;;=> "{\"a\":1,\"b\":2}"

(json/read-str "{\"a\":1,\"b\":2}")
;;=> {"a" 1, "b" 2}

```

Note that these operations are not symmetric: converting Clojure data into JSON is lossy.

### Converting Key/Value Types

You can specify a `:key-fn` to convert map keys on the way in or out:

```
(json/read-str "{\"a\":1,\"b\":2}"
               :key-fn keyword)
;;=> {:a 1, :b 2}

(json/write-str {:a 1 :b 2}
                :key-fn #(.toUpperCase %))
;;=> "{\"A\":1,\"B\":2}"

(json/read-str "{\"a\":1,\"b\":2}"
               :key-fn #(keyword "com.example" %))
;;=> {:com.example/a 1, :com.example/b 2}

```

You can specify a `:value-fn` to convert map values on the way in or out. The value-fn will be called with two arguments, the key and the value, and it returns the updated value.

```
(defn my-value-reader [key value]
  (if (= key :date)
    (java.sql.Date/valueOf value)
    value))

(json/read-str "{\"number\":42,\"date\":\"2012-06-02\"}"
               :value-fn my-value-reader
               :key-fn keyword) 
;;=> {:number 42, :date #inst "2012-06-02T04:00:00.000-00:00"}

```

Be aware that `:value-fn` only works on maps (JSON objects). If your root data structure is, for example, a vector of dates, you will need to pre- or post-process it outside of data.json. [clojure.walk](http://clojure.github.io/clojure/clojure.walk-api.html) may be useful for this.

### Order of key-fn / value-fn

If you specify both a `:key-fn` and a `:value-fn` when **reading**, the value-fn is called **after** the key has been processed by the key-fn.

The **reverse** is true when **writing**:

```
(defn my-value-writer [key value]
  (if (= key :date)
    (str (java.sql.Date. (.getTime value)))
    value))

(json/write-str {:number 42, :date (java.util.Date. 112 5 2)}
                :value-fn my-value-writer
                :key-fn name) 
;;=> "{\"number\":42,\"date\":\"2012-06-02\"}"

```

### Reading/Writing a Stream

You can also read JSON directly from a java.io.Reader with `json/read` and write JSON directly to a java.io.Writer with `json/write`.

### 