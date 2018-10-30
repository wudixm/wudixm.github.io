### jQuery 与option

[原文](https://blog.csdn.net/u010002184/article/details/78574385)

**jquery获取radio被选中的值：**

```
<input type="radio" name="rd" id="rd1" value="1">1
<input type="radio" name="rd" id="rd2" value="2">2
<input type="radio" name="rd" id="rd3" value="3">3
```

三种方法都可以：

```
$('input:radio:checked').val()；
$("input[type='radio']:checked").val();
$("input[name='rd']:checked").val();
```

 

**jquery获取select被选中的值：**

```
  <select name="products" id="sel">
    <option value='1'>option1</option>
    <option value='2' selected>option2</option>
    <option value='3'>option3</option>
    <option value='4'>option4</option>
  </select>
```

获取选中项的Value值：

```
$('select#sel option:selected').val();
或者
$('select#sel').find('option:selected').val();
```

获取选中项的Text值：

```
$('select#seloption:selected').text();
或者
$('select#sel').find('option:selected').text();
```

获取当前选中项的索引值：

```
$('select#sel').get(0).selectedIndex;
```

 

**jquery获取checkbox被选中的值：**

```
<input type="checkbox" name="ck" value="checkbox1">checkbox1     
<input type="checkbox" name="ck" value="checkbox2" checked>checkbox2     
<input type="checkbox" name="ck" value="checkbox3">checkbox3      
```

获取单个checkbox选中项： 

```
$("input:checkbox:checked").val() 
或者 
$("input:[type='checkbox']:checked").val(); 
或者 
$("input:[name='ck']:checked").val(); 
```

获取多个checkbox选中项： 

```
$('input:checkbox').each(function() { 
    if ($(this).attr('checked') ==true) { 
        alert($(this).val()); 
    } 
}); 
```