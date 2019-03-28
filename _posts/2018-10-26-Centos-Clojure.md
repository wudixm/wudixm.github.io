[原文](https://jamesd3142.wordpress.com/2018/02/01/clojure-and-leiningen-on-centos-7-4/)

If you want to run Clojure on a CentOS 7.4 Linux server try this:
```
wgethttps://raw.githubusercontent.com/technomancy/leiningen/stable/bin/lein

chmod +x lein

~/lein new asdf

cd asdf

vi project.clj

...add some deps...

~/lein repl

...wait for deps...

done!
```

The headless JDK is great! No extraneous stuff. Awesome.

