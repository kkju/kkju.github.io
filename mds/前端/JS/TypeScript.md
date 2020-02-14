一、联合类型

```typescript

const a: number|string = "123"; 

```

二、断言 

判断联合类型真实类型

> <类型>值

或者   

> 值 as 类型


```typescript

const a: number|string = "123"; 

if((<string>a).length){
    // to do with string
}else{
    // to do with number or 
}

```

三、声明

declare var 声明全局变量
declare function 声明全局方法
declare class 声明全局类
declare enum 声明全局枚举类型
declare namespace 声明（含有子属性的）全局对象
interface 和 type 声明全局类型