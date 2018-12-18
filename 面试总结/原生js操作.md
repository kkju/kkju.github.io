#### 原生js获取操作

#### 获取子节点

children（不是标准方法，但是所有浏览器都支持，可放心使用）

childNodes (所有包括文本节点)

firstElementChild
lastElementChild

firstChild(可能是text)
lastChild(可能是text)

前面添加子节点
insertBefore

最后添加子节点
appendChild

删除操作：
removeChild(子节点)

属性修改：
getAttribute
removeAttribute
setAttribute
1）创建新节点

createDocumentFragment() //创建一个DOM片段
createElement() //创建一个具体的元素
createTextNode() //创建一个文本节点

2）添加、移除、替换、插入
appendChild() //添加
removeChild() //移除
replaceChild() //替换
insertBefore() //插入

3）查找
getElementsByTagName() //通过标签名称
getElementsByName() //通过元素的Name属性的值
getElementById() //通过元素Id，唯一性
