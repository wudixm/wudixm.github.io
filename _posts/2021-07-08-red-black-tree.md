## 视频教程

[原文](https://www.youtube.com/watch?v=A3JZinzkMpk&t=0s)

 R-B Tree，全称是Red-Black Tree，又称为“红黑树”，它一种特殊的二叉查找树。红黑树的每个节点上都有存储位表示节点的颜色，可以是红(Red)或黑(Black)。

红黑树的特性:
（1）每个节点或者是黑色，或者是红色。
（2）根节点是黑色。
（3）每个叶子节点（NIL）是黑色。 [注意：这里叶子节点，是指为空(NIL或NULL)的叶子节点！]
（4）如果一个节点是红色的，则它的子节点必须是黑色的。
（5）从一个节点到该节点的子孙节点的所有路径上包含相同数目的黑节点。

英文版本的有4 个特性

1. A node is either red or black
2. The root and leaves(nil) are black
3. If a node is red, then its children and black
4. All paths from a node to its NIL descendants contain the same number of black nodes

### 插入操作的strategy

1. insert Z and color it **red**
2. recolor and rotate nodes to fix violation

### 为什么插入红点？

只会破坏教程中的第2和第3 条，容易修改到最终好的状态
2. The root and leaves(nil) are black
3. If a node is red, then its children and black

### 插入红节点会遇到的问题

4 种场景
- Z = root
- Z.uncle = red
- Z.uncle = black(triangle)
- Z.uncle = black(line)

### 场景1：Z是root

解决：Z 颜色改成black

### 场景2：Z.uncle = red

解决：recolor Z's parent, grandparent and uncle
最终根还是要黑色的， 所以会出现如下全黑情况，这种情况并不违背原则
   黑
黑    黑

```java
private void fixAfterInsertion(Entry<K,V> x) {
    x.color = RED;

    while (x != null && x != root && x.parent.color == RED) {
        if (parentOf(x) == leftOf(parentOf(parentOf(x)))) {
            Entry<K,V> y = rightOf(parentOf(parentOf(x)));
            if (colorOf(y) == RED) {
                setColor(parentOf(x), BLACK);
                setColor(y, BLACK);
                setColor(parentOf(parentOf(x)), RED);
                x = parentOf(parentOf(x));
            } else {
   
```

### 场景3：Z.uncle = black(triangle)

解决：rotate Z.parent in opposite direction of Z

### 场景4：Z.uncle = black(line)

解决：rotate Z.grandparent in opposite direction of Z, and recolor the original parent and grandparent after the rotation

### 代码解释

```java
// RB-INSERT(T, z)  
y ← nil[T]                        // 新建节点“y”，将y设为空节点。
x ← root[T]                       // 设“红黑树T”的根节点为“x”
while x ≠ nil[T]                  // 找出要插入的节点“z”在二叉树T中的位置“y”
    do y ← x                      
       if key[z] < key[x]  
          then x ← left[x]  
          else x ← right[x]  
p[z] ← y                          // 设置 “z的父亲” 为 “y”
if y = nil[T]                     
   then root[T] ← z               // 情况1：若y是空节点，则将z设为根
   else if key[z] < key[y]        
           then left[y] ← z       // 情况2：若“z所包含的值” < “y所包含的值”，则将z设为“y的左孩子”
           else right[y] ← z      // 情况3：(“z所包含的值” >= “y所包含的值”)将z设为“y的右孩子” 
left[z] ← nil[T]                  // z的左孩子设为空
right[z] ← nil[T]                 // z的右孩子设为空。至此，已经完成将“节点z插入到二叉树”中了。
color[z] ← RED                    // 将z着色为“红色”
RB-INSERT-FIXUP(T, z)             // 通过RB-INSERT-FIXUP对红黑树的节点进行颜色修改以及旋转，让树T仍然是一颗红黑树

// RB-INSERT-FIXUP(T, z)
while color[p[z]] = RED                                                  // 若“当前节点(z)的父节点是红色”，则进行以下处理。
    do if p[z] = left[p[p[z]]]                                           // 若“z的父节点”是“z的祖父节点的左孩子”，则进行以下处理。
          then y ← right[p[p[z]]]                                        // 将y设置为“z的叔叔节点(z的祖父节点的右孩子)”
               if color[y] = RED                                         // Case 1条件：叔叔是红色
                  then color[p[z]] ← BLACK                    ▹ Case 1   //  (01) 将“父节点”设为黑色。
                       color[y] ← BLACK                       ▹ Case 1   //  (02) 将“叔叔节点”设为黑色。
                       color[p[p[z]]] ← RED                   ▹ Case 1   //  (03) 将“祖父节点”设为“红色”。
                       z ← p[p[z]]                            ▹ Case 1   //  (04) 将“祖父节点”设为“当前节点”(红色节点)
                  else if z = right[p[z]]                                // Case 2条件：叔叔是黑色，且当前节点是右孩子
                          then z ← p[z]                       ▹ Case 2   //  (01) 将“父节点”作为“新的当前节点”。
                               LEFT-ROTATE(T, z)              ▹ Case 2   //  (02) 以“新的当前节点”为支点进行左旋。
                          color[p[z]] ← BLACK                 ▹ Case 3   // Case 3条件：叔叔是黑色，且当前节点是左孩子。(01) 将“父节点”设为“黑色”。
                          color[p[p[z]]] ← RED                ▹ Case 3   //  (02) 将“祖父节点”设为“红色”。
                          RIGHT-ROTATE(T, p[p[z]])            ▹ Case 3   //  (03) 以“祖父节点”为支点进行右旋。
       else (same as then clause with "right" and "left" exchanged)      // 若“z的父节点”是“z的祖父节点的右孩子”，将上面的操作中“right”和“left”交换位置，然后依次执行。
color[root[T]] ← BLACK
```

