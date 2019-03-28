jQuery `replaceWith` should get it done for you ;)

```
$( "html" ).replaceWith( data );
```

Where `data` is `html` received from server...

Entire code will look something like this...

```
$.get( "example.com/fileToLoad.html", function( data ) {
  $( "html" ).html( data );
});
```

Learn more about jQuery here...