## 问题：

在TypeScript中，这些语句（ `interface` vs `type` ）之间有什么区别？

```ts
interface X {
    a: number
    b: string
}

type X = {
    a: number
    b: string
};
```

## 答案：

注：答案会随着 typescript 的发展而不断变化。

### 2019年更新

以下有一些差异对比：

#### 1. Objects / Functions 对象和函数

两者都能用来定义一个对象类型或一个函数类型。但语法有些不同。

```ts

// 接口
interface Point {
  x: number;
  y: number;
}

interface SetPoint {
  (x: number, y: number): void;
}

// 类型别名
type Point = {
  x: number;
  y: number;
};

type SetPoint = (x: number, y: number) => void;
```

#### 2. Other Types 其他类型

与 `接口` 不同的是，`类型别名` 也可以用于其他类型的定义，如原始类型、联合类型和元祖。

```ts
// 基本类型
type Name = string;

// object
type PartialPointX = { x: number; };
type PartialPointY = { y: number; };

// 联合类型
type PartialPoint = PartialPointX | PartialPointY;

// 元组
type Data = [number, string];
```

#### 3. Extend 扩展

两者都可以被 `扩展`，但语法有些不同。

此外，请注意，`接口` 和 `类型别名` 并不互斥。`接口` 可以扩展 `类型别名`，反之亦然。

**接口 extends 接口**

```ts
interface PartialPointX { x: number; }

interface Point extends PartialPointX {
  y: number;
}
```

**类型别名 extends 类型别名**

```ts
type PartialPointX = { x: number; };

type Point = PartialPointX & {
  y: number;
};
```

**接口 extends 类型别名**

```ts
type PartialPointX = { x: number; };

interface Point extends PartialPointX {
  y: number;
}
```

**类型别名 extends 接口**

```ts
interface PartialPointX {
  x: number;
}

type Point = PartialPointX & {
  y: number;
};
```

### 4. Implements 实现

`class` 可以以相同的方式 `Implements(实现)` 接口 或 类型别名，但是请注意，`class` 和 `接口 interface` 被视为静态蓝图/设计图，因此，他们不能  `Implements(实现)` / `extentd(扩展)` 一个联合类型的类型别名。

```ts
interface Point {
  x: number;
  y: number;
}

class SomePoint implements Point {
  x = 1;
  y = 2;
}

type Point2 = {
  x: number;
  y: number;
};

class SomePoint2 implements Point2 {
  x = 1;
  y = 2;
}

type PartialPoint = { x: number; } | { y: number; };

// FIXME: can not implement a union type
class SomePartialPoint implements PartialPoint {
  x = 1;
  y = 2;
}
```

### 5. Declaration merging 声明合并

与 `类型别名` 不同，一个 `接口` 可以被多次定义，并将被视为一个 `接口`（所有声明的成员会被合并一个）

```ts
// 这里的两个定义将合并为：
// interface Point { x: number; y: number; }
interface Point { x: number; }
interface Point { y: number; }

const point: Point = { x: 1, y: 2 };
```

## 其他答案

官方文档增加了关于两者的区别于差异的介绍：[differences-between-type-aliases-and-interfaces](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#differences-between-type-aliases-and-interfaces)

> 问题来源：[https://stackoverflow.com/questions/37233735/interfaces-vs-types-in-typescript](https://stackoverflow.com/questions/37233735/interfaces-vs-types-in-typescript)