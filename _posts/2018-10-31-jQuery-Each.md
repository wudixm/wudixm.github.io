# Jquery each - Stop loop and return object

Because when you use a `return` statement inside an `each` loop, a "non-false" value will act as a `continue`, wheras `false` will act as a `break`. You will need to return `false` from the `each`function. Something like this:

```
function findXX(word) {
    var toReturn; 
    $.each(someArray, function(i) {
        $('body').append('-> '+i+'<br />');
        if(someArray[i] == word) {
            toReturn = someArray[i];
            return false;
        }   
    }); 
    return toReturn; 
}
```

From the [docs](http://api.jquery.com/jQuery.each/):

> We can break the $.each() loop at a particular iteration by making the callback function return false. Returning non-false is the same as a continue statement in a for loop; it will skip immediately to the next iteration.

```js
$(function(){
    
var result = $all.each(function(index, item){
    var question_id = $(this).attr('id')
    console.log(question_id);
    var $select_radio = $("input[name='radio_name_"+question_id+"']:checked");
    if ($select_radio.val() === undefined) {
        alert('not select');
        return false;   // return false 可以结束each 循环
    }
    var data = {"question_id":question_id, "radio_val":$select_radio.val()};
    req_data.push(data)
});
if (! result ){
    return;
}
})
```

