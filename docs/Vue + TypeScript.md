# Vue + TypeScript

## 安装运行

### 安装

1. 安装node.js + npm

   直接到官网安装，安装node.js后会自动安装npm

   ```
   node -v
   npm -v
   ```

2. 安装TypeScript

   ```
   安装：npm install -g typescript
   校验：tsc -v
   ```

   ![image-20211015195312019](D:\Note\前端学习笔记\vue+typescript\pictures\image-20211015195312019.png)

tsc作用：负责将ts代码转成浏览器和nodejs识别的js代码。

### 示例

1. 编写ts代码，创建`.ts`文件

	![image-20211015205048183](D:\Note\前端学习笔记\vue+typescript\pictures\image-20211015205048183.png)

2. 编译特殊，使用命令进行编译`tsc ./xxxx.ts`，生成`.js`文件。

	*问题：新安装的vscode无法使用cmd命令*

	使用管理员身份运行vscode，使用`get-ExecutionPolicy`命令查询，如果为Restricted表示禁止终端命令的意思，运行如下命令即可修改`set-ExectionPolicy RemoteSigned`

	![image-20211015204650197](D:\Note\前端学习笔记\vue+typescript\pictures\image-20211015204650197.png)
	
3. 运行JS，将JS文件引入HTML页面执行

### 自动编译

1. 运行：tsc --init，创建tsconfig.json文件
2. 修改tsconfig.json文件，设置js文件夹："outDir":"./js/"
3. 设置vscode监视任务，之后修改项目中的ts，自动生成js

## TypeScript基础

### 基本数据类型

#### 布尔值

基本数据类型，有两个值：true/false

```typescript
const isDone: boolean = false;
```

注意，不能将构造函数Boolean实例化的值赋给boolean，因为Boolean示例化以后的类型为Boolean，并给boolean。

```typescript
const isDone: Boolean = new Boolean(false);
```

区分：boolean是基本类型，Boolean是js中的构造函数，返回的对象是以Boolean为原型的js对象。

#### 数字

基本数据类型，js和ts中的数据全部都是浮点数。

```typescript
// 十进制
let decLiteral: number = 6;
// 十六进制
let hexLiteral: number = 0xf00d;
// 二进制
let binaryLiteral: number = 0b1010;
// 八进制
let octalLiteral: number = 0o744;
```

#### 字符串

基本数据类型，js和ts都可以使用"或者'符号包裹来表示字符串，也可以使用模板字符串`符号进行包裹：

```typescript
let str: string = '前端开发';
let name: string = "学习";
// 模板字符串
let tempStr: string = `欢迎${name}${str}`;
```

#### Null和undefined

这两个基本数据类型，同样可以在ts中分别使用null和undefined来表示

```typescript
let u: undefined = undefined;
let n: null = null;
```

默认情况下，null和undefined是其他类型的子类型。也就是说，可以将null和undefined赋值给其他类型的数据而不会编译报错。例如：

```typescript
let age: number = undefined;
let people: string = null;
```

这个规则是可以定制化的，在tsconfig.json的文件中，修改配置文件里面的strictNullChecks属性为true，就会发现上述两行代码就会报错了。

#### void

这个是ts独有的类型。可以理解为空值，表示没有任何类型。声明为void的变量只能赋值为null或者undefined：

```typescript
let age: void = undefined;
```

这里插播一点，null可以赋值给void是ts官方的定义。但是我亲自测试的时候发现无论是变量的null还是函数返回值null赋值给void都会报错。**所以关于null是否能赋值给void这一点还有待确定**。

void除了可以赋值为undefined，还可以表示没有返回值的函数：

```typescript
// (): void的写法表示函数的返回值类型
let getData = (): void => console.log('这里没有返回值');
```

要注意没有返回值的函数并非是指返回undefined，实际上它的返回值类型就是void。所以不要定义函数返回类型为undefined，会报错。

#### any

ts独有的类型，有时候我们还不能确定一个变量到底会被赋予什么类型的值的时候，就可以定义为any类型。any表示可以是任意的类型：

```typescript
let a: any = 1;
let b: any = true;
let c: any = null;
```

any可以在编译过程中被赋予任意类型的值：

```typescript
let a: any = 1;
a = undefined;
a = '字符串';
```

可以任意访问any类型的内部属性：

```typescript
let a: any;
console.log(a.anyProp.anyOtherFn());
```

在声明变量的时候未指定类型也会被识别为any类型：

```typescript
let a;
a = '任意类型';
a = 2;
```

**一定要避免使用any！！**否则ts和js没什么差别了。

#### never

ts独有的类型，表示永远不会存在的值的类型。never类型的值可以赋值给任意类型，但是除了never类型，没有其他类型可以赋值给never类型，**即使是any类型也不行**。

```typescript
let nerverType: never;
let anyType: any;
neverType = anyType;
/*
  不能将类型“any”分配给类型“never”。
*/
```

never类型一般使用在哪些总是会抛出异常的函数中：

```typescript
function throwErr(): never {
    throw new Error('something throng');
}
```

又或者是无法结束循环的函数中：

```typescript
function infiniteLoop(): never {
    while(true) {}
}
```

### 引用数据类型

#### 对象

ts中使用接口（interface）来定义对象。

```typescript
interface IAnimal {
    name: string;
    eat (): void;
}
```

上面定义了Animal接口，ts中规定interface的第一个字母必须是大写的"i"。

```typescript
let cat: IAnimal = {
    name: 'Tom';
    eat (): {
        console.log('喝牛奶');
    }
}
```

按照上面的接口定义，只要有缺失的属性，ts编译就会报错。同样的，有多余的属性编译也是不通过的。

**接口可以定义可选属性**，表示该属性可以存在也可以不存在。

```typescript
interface IAnimal {
    name: string;
    eat (): void;
    age?: number; // 表示可选
}
```

这时候无论实现的对象是否包含age属性都不会报错。

**接口可以定义任意属性**，任意属性允许我们传入一个任意的变量，一个接口以允许定义一个任意属性：

```typescript
interface IAnimal {
    name: string;
    eat (): void;
    age?: number;
    [propName: string]: any;
}

let cat: IAnimal = {
    name: 'Tom',
    eat () {
        console.log('喝牛奶');
    },
    age: 18,
    anyProp: '这是任意变量',
    anyProp2: true
}
```

其中，IAnimal中的[propName: string]: any这句话：

```typescript
[propName: string]: any;
```

接口如果定义了这句话，你就可以在具体的对象中放置任意数目的属性而不用担心编译错误。**propName: string表示的是最终只会取键类型是string的值**，所以如果键的类型是number、symbol等等最后是不会存在cat中的。

上面的any表示的是允许的值类型，要注意一旦定义了任意属性，那么确定属性和可选属性都必须是任意类型的子集。例如下面的任意属性就会报错：

```typescript
interface IAnimal {
  name: string;
  eat (): void;
  age?: number;
  [propName: string]: string;
}

/*
  类型“() => void”的属性“eat”不能赋给字符串索引类型“string”。
  类型“number | undefined”的属性“age”不能赋给字符串索引类型“string”。
*/
```

因为任意属性定义的类型是string，所以其他属性必须为string。所以这也是为什么要在特定场合才使用任意属性的原因，限制太多。

**接口可以定义只读属性，**只需要在前面添加一个readonly即可：

```typescript
interface IAnimal {
  readonly name: string;
  eat (): void;
  age?: number;
}
```

当我们打算为只读属性赋值就会报错：

```typescript
let cat: IAnimal = {
  name: 'Tom',
  eat () {
    console.log('喝牛奶');
  },
  age: 18,
}
cat.name = 'Jerry';

/*
  无法分配到 "name" ，因为它是只读属性。
*/
```

#### 数组

数组可以用3种方式定义

##### 第一种：类型+方括号表示法（推荐）

```typescript
const arr: number[] = [1, 2, 3];
```

最简单的一种，表示每一项都是number类型。

##### 第二种：泛型

```typescript
const arr: Array<number> = [1, 2, 3];
```

Array后面接尖括号，里面的number参数就表示数组的每一项必须是number，约束和第一种写法是一样的。

##### 第三种：接口

```typescript
interface IArr {
    [i: number]: number
}

let arr: IArr = [1, 2, 3];
```

这种用法真的很巧妙！不过我们一般不推荐这种用法，比较复杂而且可读性差。

如果我们想要一个存放任意类型数据的数组，可以定义any类型的数组：

```typescript
let arr: any[] = [ 1, true, 2, '这是任意类型数组' ];
```

#### 函数

```typescript
let getData = (): void => console.log('这里没有任何返回值');
```

用箭头函数表示，函数不接受参数，返回值类型为void类型，也就是不返回任何值。

函数可以接收参数，参数的类型需要指定：

```typescript
function add(num1: number, num2: number): number {
    return num1 + num2;
}
```

函数还可以通过函数表达式的方式定义：

```typescript
let sum = (num1: number, num2: number): number => num1 + num2;
```

但是还有个问题，就是左侧的sum变量还没有赋予类型。要为sum赋予类型的话，必须赋值为完整的函数类型，包含参数和返回值：

```typescript
let sum: (num1: number, num2: number) => number 
  = (num1: number, num2: number): number => num1 + num2;
```

注意上面的 `=> number`和下面的箭头函数`=> num1 + num2`的差别，第一个是ts语法中的表示函数执行后返回值类型，第二个则是箭头函数特定写法。

**函数可以定义可选参数和默认参数**

和接口类似，函数的参数类型可以为可选参数，可以为参数添加默认参数。我们看个例子：

```typescript
function add(num1: number, num2: number, isDouble?: boolean): number {
  return isDouble ? (num1 + num2) * 2 : (num1 + num2);
}

add(1, 2); // 3
add(2, 5, true); // 14
```

上面函数中定义了一个可选参数：isDouble，调用者可以选择是否传递该参数，如果不传的话就是undefined。要注意可选参数不允许出现在必选参数前面：

```typescript
function add(isDouble?: boolean, num1: number, num2: number): number {
  return isDouble ? (num1 + num2) * 2 : (num1 + num2);
}

/*
  必选参数不能位于可选参数后。
*/
```

原因也很容易理解，可选参数在前面的话调用者就没办法传参了。下面我们来看下默认参数写法：

```typescript
function add(num1: number, num2: number, isDouble?: boolean, coefficient: number = 2): number {
  return isDouble ? (num1 + num2) * coefficient : (num1 + num2);
}

add(1, 2); // 3
add(4, 6, true); // 20
add(4, 6, true, 3); // 30
```

这里增加多了一个默认参数：coefficient，和js函数的默认参数定义是一样的，如果没有传递则用默认值。

**函数可以通过...rest来接收剩余参数**

你可以通过一个变量来收集所有的参数：

```typescript
function eat(food: string, ...rest: string[]): void {
  console.log('eat：', food, rest);
}

eat('香蕉', '菠萝', '苹果'); // eat： 香蕉  ["菠萝", "苹果"]
```

这个变量实际上就是一个数组，要注意它同样得放到参数最后面（在可选参数之后）。

### 类型推论

ts可以根据上下文语境动态推断类型。

- 在没有指定类型的时候推断：

```typescript
let str = '字符串1';

// 等价于

let str: string = '字符串1';
```

- 在定义时没有赋值：

```typescript
let str;

//等价于

let str: any;
```

### 联合接口

联合类型用于表示变量可能取值为多个变量之一，如下：

```typescript
let a: number | string;
```

变量a的可能取值是string或者number，在赋值之前它的类型都是不确定的状态。但是如果给它赋予其它类型的值就会报错：

```typescript
a = true;

/*
  不能将类型“true”分配给类型“string | number”。
*/
```

当联合类型不能确定是哪种类型时，我们只能访问**两种类型都存在的属性或者方法，**否则就会编译错误。

```typescript
let a: string | number;
console.log(a.length);

/*
  类型“string | number”上不存在属性“length”。
    类型“number”上不存在属性“length”。
*/
```

当为变量赋值之后，其类型也就确定下来了，无法再使用另一个类型的属性。

```typescript
let a: string | number = 12;
console.log(a.length);

/*
  类型“number”上不存在属性“length”。
*/
```

