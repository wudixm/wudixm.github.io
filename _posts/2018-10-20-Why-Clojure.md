## WHy Clojure
We knew it needed to be functional programming because that\`s where doer the tracking of mobile ads and we knew we\`re going to get a lot of info coming into the system and the best way, the easiest way to paralyze walk on massive data structures or massive data is using functional languages.
Clojure won, and the reason that it did is because clojure takes the functional programming paradigm and puts it to the extreme

### as refer 

when you require stuff from other clojure namespaces you have two keywords you can use.
- **as**: an alias for the entire namespace as a single whatever you want to call it
- **refer**: takes only the functions or symbols from this namespace and pull them into your own your namespace so you can use them without any prefix annotation, you can combine `[org.httpkit.server :refer [run-server] :as s]`, run-server is the function.
