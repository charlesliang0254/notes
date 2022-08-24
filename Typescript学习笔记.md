【Typescript学习笔记】

编译ts代码：tsc demo.ts

类型注解：

```typescript
function sayHello(message:string){
    console.log(message);
}

let msg1:string = "123";
let msg2 = [1,2,3];

sayHello(msg1);
sayHello(msg2);//error
```

接口：在ts中只要对象的内容结构和接口保持一致，即使不显式声明implements也能保持和接口的兼容

```typescript
interface Person{
    firstname:string;
    secondname:string;
}
```

类：

```typescript
class Student {
    fullName: string;
    //构造函数，使用public则创建同名的成员变量
    constructor(public firstName:string, public middleInitial:string, public lastName:string) {
        this.fullName = firstName + " " + middleInitial + " " + lastName;
    }
}
```

基础类型：