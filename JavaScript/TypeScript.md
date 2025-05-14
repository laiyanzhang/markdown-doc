# TypeScript

标签（空格分隔）： JavaScript

---

## 1.基础类型
- 定义：let name：string = 'lyz'(let 变量名：变量类型 = 值)
- 数组：
  - 第一种方式：let list: number[] = [1, 2, 3]
  - 第二种方式：let list: Array<number> = [1, 2, 3]
- Any：let notSure: any = 4  变量可被重复赋予各种类型的值，不做类型校验
- 函数类型检查：function name：{类型检查}{函数内容}，对函数返回对象进行类型检查
- 类型断言：类似于类型转换
  - 第一种方式：<类型>变量
  - 第二种方式：变量 as 类型（JSX语法中唯一被允许）
- 混合类型：一个对象同时拥有多种类型，同时作为函数和对象使用


  

## 2.接口
- 定义：interface 定义对象名及其类型，用作后续的类型检查调用
- 可选属性：属性？
- 只读属性：readonly 属性
- 额外的属性检查：对象字面量赋值给对象或作为参数传递，对象字面量存在任何目标类型不包含的属性将得到错误，绕过类型检查
  - 类型断言
  - 字符串索引签名
- 函数类型：
  - interface 函数名 { （参数类型检查）：函数返回值类型检查 }
  - let 变量 = 函数名 ; 变量 = function（）{}
  - 对于函数类型的类型检查来说
     - 函数的参数名不需要与接口定义名字相匹配，函数对参数逐个检查，要求对应位置上的参数类型兼容
     - 函数的返回值类型根据返回值判断，若不符合接口定义类型则类型检查器发出警告
- 可索引类型
  - 支持索引签名：字符串和数字，数字索引的返回值必须是字符串索引返回值类型的子类型
  - 定义索引签名后，所有属性的返回值类型必须与索引签名返回值类型相同
- 实现接口implements：interface定义接口，implements继承接口并可在类里实现接口中的方法
- 继承接口extends：extends继承父类接口，可同时继承多个接口
- 接口继承类：接口继承类类型后，仅声明类成员但未实现。对于继承拥有私有或受保护的成员的类时，接口类型只能被这个类或子类实现



## 3.类
- 定义class：constructor定义类构造器
- 继承extends：super()调用超类构造方法，可重写超类方法属性
- 公共、私有与保护修饰符：
  - public：默认
  - private：仅在定义类内调用，只有具有相同的private成员才能兼容互相赋值
  - protected：可在定义类的派生类内调用
- 存取器：类内可定义set()、get()方法用于获取或设置属性值
- 静态属性static：访问静态属性要在前面加上类名
- 抽象类abstract：不被实例化，抽象方法必须在派生类中实现


## 4.函数
- 定义：`let class: (x: number, y: number) => number`
- 具体：`let class: (x: number, y: number) => number = function(x: number, y: number): number { return x+y; }`
- 推断类型：`let class: (baseValue: number, increment: number) => number = function(x, y) { return x + y; }`按上下文归类，参数类型匹配，参数名正确与否不重要
- 可选参数：`function buildName(firstName: string, lastName?: string)`
- 默认值：`function buildName(firstName: string, lastName = "Smith")`，调用时可省略默认参数
- 剩余参数：`function buildName(firstName: string, ...restOfName: string[])`
- this：
  - 箭头函数：保留创建函数时的this
  - this参数：显示写入this参数，可指定this参数类型
- 重载：调用函数时根据重载列表依次尝试，匹配则使用该函数，在下方实例中两个重载，对应参数为对象/数字的情况，其他参数调用则产生错误
``` javaScript
function pickCard(x: {suit: string; card: number; }[]): number;
function pickCard(x: number): {suit: string; card: number; };
function pickCard(x): any { }
```


## 5.泛型
- 定义：`function identity<T>(arg: T): T`，T捕获传入类型，确保参数类型与返回值类型相同
- 类型推论：调用参数时可选择性传入类型，不传入时根据参数类型进行类型推论
- 泛型接口：`interface GenericIdentityFn<T> {(arg: T): T; } let myIdentity: GenericIdentityFn<number>`根据传入参数确定泛型类型
- 泛型约束：`function loggingIdentity<T extends Lengthwise>(arg: T): T`Lengthwise规定约束类型，传入值需要符合约束类型的值，必须包含必须的属性



## 6.装饰器
- 定义：expression求值后必须为一个函数，运行时被调用，被装饰的声明信息作为参数传入
- 引用：@expression，能够被附加到类声明、方法、访问符、属性或参数上
- 装饰器组合：多个装饰器应用在一个声明上时
  - 由上至下依次对装饰器表达式求值
  - 求值的结果会被当作函数，由下至上依次调用
- 类装饰器：
```javaScript
// 参数target为装饰类
function helloWord(isTest: boolean) {
    return function(target: any) {
        // 添加静态变量
        target.isTestable = isTest;
    }
}
```


## 7.命名空间
- 声明命名空间namespace：通过空间名可调用空间内的属性
- 输出export：表示提供外界调用
- 别名import：import 别名 = 命名空间
- 声明declare：用作编译时声明


