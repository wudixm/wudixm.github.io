---
title: Java XML
excerpt: |
  Java XML
category: Java Frameworks
feature_image: "https://picsum.photos/2560/600?image=872"
---
## Java XML 学习

- XML Declaration 显示了xml 的版本和所用的encoding

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  ```

- Extensible Markup Language (**XML**) is used for storing information, discribe the structure of the information, transporting information

- If you would like your XMl node to show up **unedited** use:

  ```xml
     <![CDATA[
       <show>
      </show>
     ]]>
  ```

- Sepcial charactors 

  ```xml
  <!-- & &amp; ` &apos; > &gt; < &lt; " &quot; -->
  ```

- Extential style language — Styling XML

### 例子

例子网址：https://www.youtube.com/watch?v=HfGWVy-eMRc

```java
DocumentBuilderFactory factory = DocumentBuilderFactory.newInstance();
try {
    DocumentBuilder documentBuilder = factory.newDocumentBuilder();
    Document tableDoc = documentBuilder.parse("***/Practise0824/PractiseTest/src/JavaXML/Example/table.xml");
    NodeList animal = tableDoc.getElementsByTagName("animal");
    System.out.println("animal.getLength() = " + animal.getLength());
//            NodeList dog = tableDoc.getElementsByTagName("dog");
//            System.out.println("dog.getLength() = " + dog.getLength());
    for (int i = 0; i < animal.getLength(); i++) {
        Node item = animal.item(i);
        if (item.getNodeType() == Node.ELEMENT_NODE) {
            Element animalEle = (Element) item;
            String id = animalEle.getAttribute("id");
            NodeList childNodes = animalEle.getChildNodes();
            for (int j = 0; j < childNodes.getLength(); j++) {
                Node item1 = childNodes.item(j);
                if (item1.getNodeType() == Node.ELEMENT_NODE) {
                    Element item1Ele = (Element) item1;
                    System.out.println("animal "+ id + ": " + item1Ele.getTagName()
                        + " " + item1Ele.getTextContent());
                }
            }
        }
    }
```

其中table.xml文件如下：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<animals>
    <animal id="1"><name>dog</name><speak>bark</speak></animal>
    <animal id="2"><name>cat</name><speak>meao</speak></animal>
</animals>
```

打印结果是：

```java
animal.getLength() = 2
animal 1: name dog
animal 1: speak bark
animal 2: name cat
animal 2: speak meao
```





