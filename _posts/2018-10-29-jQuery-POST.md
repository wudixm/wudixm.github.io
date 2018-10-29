## jQuery AJAX

[原文](http://api.jquery.com/jquery.ajax/)

### POST 时遇到问题

向后台传对象，

```js
data = {'how':1, 'how2':2}

$.post('/finish_question', data,
	function(returnedData){
		console.log(returnedData);
}, 'json');
```

但是后台利用`(slurp (:body req))`  打印出来的post 请求的body 是`how=1&how2=2`

```
(defn finish_question "finish_question" [req]
  (println req)
  (let [body  (slurp (:body req))]
    (println body))
  )
  
打印：
how=1&how2=2
```

### 解析

查看`$.ajax` 方法的参数：

**data**

Type: [PlainObject](http://api.jquery.com/Types/#PlainObject) or [String](http://api.jquery.com/Types/#String) or [Array](http://api.jquery.com/Types/#Array)

Data to be sent to the server. **It is converted to a query string, if not already a string**. It's appended to the url for GET-requests. See `processData` option to prevent this automatic processing. Object must be Key/Value pairs. If value is an Array, jQuery serializes multiple values with same key based on the value of the `traditional` setting (described below).

**processData** (default: `true`)

Type: [Boolean](http://api.jquery.com/Types/#Boolean)

By default, data passed in to the `data` option as an object (technically, anything other than a string) will be processed and transformed into a query string, fitting to the default content-type "application/x-www-form-urlencoded". If you want to send a DOMDocument, or other non-processed data, set this option to `false`.



如文章中所说，如果不是一个string，则会默认转换成一个query string。

改成如下：

```js
$.ajax({
    url: '/finish_question',
    type: 'POST',
    data: data,
    processData: false,
    complete: function (jqXHR, textStatus) {
        // callback
        console.log("complete");
        console.log(data);
    }...
```

如果还想用以前的格式（需要转换）：

```js
$.ajax({
    url: '/finish_question',
    type: 'POST',
    data: $.param(data), // 用param 方法
    complete: function (jqXHR, textStatus) {
        // callback
        console.log("complete");
        console.log(data);
    }...
```

param 方法解释：

**jQuery.param( obj )Returns: [String](http://api.jquery.com/Types/#String)**

**Description: **Create a serialized representation of an array, a plain object, or a jQuery object suitable for use in a URL query string or Ajax request. In case a jQuery object is passed, it should contain input elements with name/value properties.

