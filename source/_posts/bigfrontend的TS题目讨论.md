---
title:  bigfrontend的TS题目讨论
date: 2022-08-17 22:00:00
categories: 
- web前端
tags:
- bigfrontend
- 前端
- 题库
---

国外前端会考察哪些问题？js函数、系统设计、TS类型体操的用法，大大拓宽了我的眼界，去思考和实战更加相关的问题吧，还有一个Type-challenges，经常更新一个新的Type实现：[TypeScript 类型体操姿势合集](https://github.com/type-challenges/type-challenges)
<!-- more -->

# [TypeScript类型体操](https://bigfrontend.dev/typescript)

这一部分很有意思，你写过一遍之后，对于TS里怎么用这些方法更加得心应手

## [1. implement Partial\<T>](https://bigfrontend.dev/typescript/implement-Partial-T)

### 题目

`Partial<T>` returns a type whichs represents all subsets of type `T`.

Please implement `MyPartial<T>` by yourself.

```ts
type Foo = {
  a: string
  b: number
  c: boolean
}

// below are all valid

const a: MyPartial<Foo> = {}

const b: MyPartial<Foo> = {
  a: 'BFE.dev'
}

const c: MyPartial<Foo> = {
  b: 123
}

const d: MyPartial<Foo> = {
  b: 123,
  c: true
}

const e: MyPartial<Foo> = {
  a: 'BFE.dev',
  b: 123,
  c: true
}
```

### 答案

找到键的索引，全部用`?`转化为可选

```ts
type MyPartial<T> = {
  [key in keyof T] ?: T[key];
}
```

`keyof` 索引查询 对应任何类型 `T`,`keyof` `T`的结果为该类型上所有公有属性`key`的联合：

```typescript
interface Eg1 {
  name: string,
  readonly age: number,
}
// T1的类型实则是name | age
type T1 = keyof Eg1

class Eg2 {
  private name: string;
  public readonly age: number;
  protected home: string;
}
// T2实则被约束为 age
// 而name和home不是公有属性，所以不能被keyof获取到
type T2 = keyof Eg2
```



## [2. implement Required\<T>](https://bigfrontend.dev/typescript/implement-Required-T)

### 题目

As the opposite of [Partial](https://bigfrontend.dev/typescript/implement-Partial-T), `Required<T>` sets all properties of `T` to required.

Please implement `MyRequired<T>` by yourself.

```ts
// all properties are optional
type Foo = {
  a?: string
  b?: number
  c?: boolean
}


const a: MyRequired<Foo> = {}
// Error

const b: MyRequired<Foo> = {
  a: 'BFE.dev'
}
// Error

const c: MyRequired<Foo> = {
  b: 123
}
// Error

const d: MyRequired<Foo> = {
  b: 123,
  c: true
}
// Error

const e: MyRequired<Foo> = {
  a: 'BFE.dev',
  b: 123,
  c: true
}
// valid
```

### 答案

找到键的索引，全部用`-?`转化为必选

```ts
type MyPartial<T> = {
  // the 'optional' symbol = '?'
  // the 'not' symbol = '-'
  // key is not optional in T
  [key in keyof T] -?: T[key];
}
```



## [3. implement Readonly\<T>](https://bigfrontend.dev/typescript/implement-Readonly-T)

### 题目

`Readonly<T>` returns a type that sets all properties of `T` to `readonly`.

Pleaes implement `MyReadonly<T>` by yourself.

```ts
type Foo = {
  a: string
}

const a:Foo = {
  a: 'BFE.dev',
}
a.a = 'bigfrontend.dev'
// OK

const b:MyReadonly<Foo> = {
  a: 'BFE.dev'
}
b.a = 'bigfrontend.dev'
// Error
```

### 答案

找到键的索引，全部用`readonly`转化为只读属性，keyof   索引类型查询操作符。 对于任何类型 `T`， `keyof T`的结果为 `T`上已知的公共属性名的联合。T[P] 索引访问操作符。根据属性名获得对应类型。

```ts
type MyReadonly<T> = {
  readonly [key in keyof T]: T[key]
}
```



## [4. implement Record<K, V>](https://bigfrontend.dev/typescript/implement-Record-K-V)

### 题目

`Record<K, V>` returns an object type with keys of K and values of V.

Please implement `MyRecord<K, V>` by yourself.

Notice that property key could only be `number | string | symbol`.

```ts
type Key = 'a' | 'b' | 'c'

const a: Record<Key, string> = {
  a: 'BFE.dev',
  b: 'BFE.dev',
  c: 'BFE.dev'
}
a.a = 'bigfrontend.dev' // OK
a.b = 123 // Error
a.d = 'BFE.dev' // Error

type Foo = MyRecord<{a: string}, string> // Error
```

### 答案

这个稍微复杂一些，需要用`extends`表明键的类型

```ts
type MyRecord <K extends string | number | symbol, V> = {
  [key in K]: V
}
```



## [5. implement Pick<T, K>](https://bigfrontend.dev/typescript/implement-Pick-T-K)

### 题目

`Pick<T, K>`, as the name implies, returns a new type by picking properties in K from T.

Please implement `MyPick<T, K>` by yourself.

```ts
type Foo = {
  a: string
  b: number
  c: boolean
}

type A = MyPick<Foo, 'a' | 'b'> // {a: string, b: number}
type B = MyPick<Foo, 'c'> // {c: boolean}
type C = MyPick<Foo, 'd'> // Error
```

### 答案

同样，需要用`extends`表明键的范围



```ts
type MyPick<T, K extends keyof T> = {
  [key in K]: T[key]
}

// we only want the property types from A that exist in B
// so the keys of B extend T
// meaning that B is found in T
// so we only return the keys(B) found in T
```



## [6. implement Omit<T, K>](https://bigfrontend.dev/typescript/implement-Omit-T-K)

### 题目

`Omit<T, K>` returns a new type by picking the properties in T but not in K.

Please implement `MyOmit<T, K>` by yourself.

```ts
type Foo = {
  a: string
  b: number
  c: boolean
}

type A = MyOmit<Foo, 'a' | 'b'> // {c: boolean}
type B = MyOmit<Foo, 'c'> // {a: string, b: number}
type C = MyOmit<Foo, 'c' | 'd'>  // {a: string, b: number}
```

### 答案

需要用`extends`结合三目判断符进行判断，将找不到的键名赋值为never

Here is documentation about remapping: https://www.typescriptlang.org/docs/handbook/2/mapped-types.html#key-remapping-via-as

```ts
type MyOmit<T, K extends keyof any> = {
  [key in keyof T as key extends K ? never : key]: T[key]
}
```



## [7. implement Exclude<T, E>](https://bigfrontend.dev/typescript/implement-Exclude-T-E)

### 题目

`Exclude<T, K>` returns a type by removing from T the union members that are assignable to K.

Please implement `MyExclude<T, K>` by yourself.

```ts
type Foo = 'a' | 'b' | 'c'

type A = MyExclude<Foo, 'a'> // 'b' | 'c'
type B = MyExclude<Foo, 'c'> // 'a' | 'b
type C = MyExclude<Foo, 'c' | 'd'>  // 'a' | 'b'
type D = MyExclude<Foo, 'a' | 'b' | 'c'>  // never
```

### 答案

需要用`extends`结合三目判断符进行判断，将找不到的键名赋值为never

```ts
type MyExclude<T, E> = T extends E ? never : T
// for the part in T that exists in E
// don't use it
// else, leave it 
```



## [8. implement Extract<T, U>](https://bigfrontend.dev/typescript/implement-Extract-T-U)

### 题目

As the opposite of [Exclude](https://bigfrontend.dev/typescript/implement-Exclude-T-E), `Extract<T, U>` returns a type by extracting union members from T that are assignable to U.

Please implement `MyExtract<T, U>` by yourself.

```ts
type Foo = 'a' | 'b' | 'c'

type A = MyExtract<Foo, 'a'> // 'a'
type B = MyExtract<Foo, 'a' | 'b'> // 'a' | 'b'
type C = MyExtract<Foo, 'b' | 'c' | 'd' | 'e'>  // 'b' | 'c'
type D = MyExtract<Foo, never>  // never
```

### 答案

需要用`extends`结合三目判断符进行判断，将找不到的键名赋值为never

```ts
type MyExtract<T, U> = T extends U ? T : never
```



## [9. implement NonNullable\<T>](https://bigfrontend.dev/typescript/NonNullable)

### 题目

`NonNullable<T>` returns a type by excluding `null` and `undefined` from T.

Please implement `MyNonNullable<T>` by yourself.

```ts
type Foo = 'a' | 'b' | null | undefined

type A = MyNonNullable<Foo> // 'a' | 'b'
```

### 答案

需要用`extends`结合三目判断符进行判断，将找不到的键名赋值为never

```ts
type MyNonNullable<T> = T extends(null | undefined) ? never : T

// your code here, don't use NonNullable<T> in your code
// i like this solution very much
// if the type is null | undefined
// that cannot be allowed 
// all other types, we allow
```

[Stackoverflow post for help](https://stackoverflow.com/questions/51851677/how-to-get-argument-types-from-function-in-typescript/51851844)



## [10. implement Parameters\<T>](https://bigfrontend.dev/typescript/Parameters)

### 题目

For function type T, `Parameters<T>` returns a tuple type from the types of its parameters.

Please implement `MyParameters<T>` by yourself.

```ts
type Foo = (a: string, b: number, c: boolean) => string

type A = MyParameters<Foo> // [a:string, b: number, c:boolean]
type B = A[0] // string
type C = MyParameters<{a: string}> // Error
```

### 答案

需要用`infers`将输入参数提取出来，后面需要补充三目判断符做兜底

```ts
type MyParameters<T extends (...args: any[]) => any> = T extends (...args: infer P) => any ? P : never;
// the type must be a function
// infers the type that is passed
// read the parameters from the function
// and returns a type that only allows those parameters
```

[Stackoverflow post for help](https://stackoverflow.com/questions/51851677/how-to-get-argument-types-from-function-in-typescript/51851844)



## [11. implement ConstructorParameters\<T>](https://bigfrontend.dev/typescript/ConstructorParameters)

### 题目

[Parameters](https://bigfrontend.dev/typescript/Parameters) handles function type. Similarly, `ConstructorParameters<T>` is meant for class, it returns a tuple type from the types of a constructor function T.

Please implement `MyConstructorParameters<T>` by yourself.

```ts
class Foo {
  constructor (a: string, b: number, c: boolean) {}
}

type C = MyConstructorParameters<typeof Foo> 
// [a: string, b: number, c: boolean]
```

### 答案

需要用`infers`将输入参数提取出来，后面需要补充三目判断符做兜底

```ts
type MyConstructorParameters<T extends new (...args: any) => any> =
T extends new (...args: infer P) => any ? P : never
// check if the generic Type is a class type
// we can check using a property check
// then get all the args from the constructor
// and return those args
```



## [12. implement ReturnType\<T>](https://bigfrontend.dev/typescript/ReturnType)

### 题目

Similar to [Parameters](https://bigfrontend.dev/typescript/Parameters), `ReturnType<T>`, as the name says itself, returns the return type of function type T.

Please implement `MyReturnType<T>` by yourself.

```ts
type Foo = () => {a: string}

type A = MyReturnType<Foo> // {a: string}
```

### 答案

需要用`infers`将结果提取出来，后面需要补充三目判断符做兜底

```ts
type MyReturnType<T extends (...args: any[]) => any> =
T extends (...args: any[]) => infer P ? P : never
// our type is a function
// we infer the return type
```



## [13. implement InstanceType\<T>](https://bigfrontend.dev/typescript/InstanceType)

### 题目

For a constructor function type T, `InstanceType<T>` returns the instance type.

Please implement `MyInstanceType<T>` by yourself.

```ts
class Foo {}
type A = MyInstanceType<typeof Foo> // Foo
type B = MyInstanceType<() => string> // Error
```

### 答案

需要用`infers`将结果提取出来，后面需要补充三目判断符做兜底

```ts
/**
* same as ReturnType<T> expect T extends constructor function type
*/ 
type MyInstanceType<T extends new (...args: any[]) => any> = 
T extends new (...args: any[]) => infer P ? P : never
// return the instanceType
// T extends a constructor function
// and we return the constructor functions type - we infer the return type
```



## [14. implement ThisParameterType\<T>](https://bigfrontend.dev/typescript/ThisParameterType)

### 题目

For a function type T, `ThisParameterType<T>` extracts the `this` type. If `this` is not set, `unknown` is used.

Please implement `MyThisParameterType<T>` by yourself.

```ts
function Foo(this: {a: string}) {}
function Bar() {}

type A = MyThisParameterType<typeof Foo> // {a: string}
type B = MyThisParameterType<typeof Bar> // unknown
```

### 答案

需要用`infers`将第一个输入参数提取出来，后面需要补充三目判断符做兜底

```ts
type MyThisParameterType<T extends (...args: any[]) => any> = 
T extends (this: infer P, ...args: any[]) => any ? P : unknown
```

Sometimes problem is easier than you thought. The question wants to get the 'this ' parameter type, just return this type.



## [15. implement OmitThisParameter\<T>](https://bigfrontend.dev/typescript/OmitThisParameter)

### 题目

When `Function.prototype.bind()` is used, the returned function has a bound `this`. `OmitThisParameter<T>` could be used to type this.

Please implement `MyOmitThisParameter<T>` by yourself.

```ts
function foo(this: {a: string}) {}
foo() // Error

const bar = foo.bind({a: 'BFE.dev'})
bar() // OK


type Foo = (this: {a: string}) => string
type Bar = MyOmitThisParameter<Foo> // () => string
```

### 答案

需要用`infers`将第一个输入参数提取出来，后面需要补充三目判断符做兜底

```ts
type MyOmitThisParameter<T extends (...args: any[]) => any> = 
T extends (this: any, ...args: infer P) => infer Q ? (...args: P) => Q : unknown
```



## [16. implement FirstChar\<T>](https://bigfrontend.dev/typescript/FirstChar)

### 题目

Please implement `FirstChar<T>` to get the first character of a string.

```ts
type A = FirstChar<'BFE'> // 'B'
type B = FirstChar<'dev'> // 'd'
type C = FirstChar<''> // never
```

### 答案

需要用`infers`将参数首字母提取出来，后面需要补充三目判断符做兜底

```ts
type FirstChar<T extends string> = T extends `${infer S}${any}` ? S : never
```



## [17. implement LastChar\<T>](https://bigfrontend.dev/typescript/implement-LastChar-T)

### 题目

Similar to [FirstChar](https://bigfrontend.dev/typescript/FirstChar), please implment `LastChar<T>` to get the last character.

```ts
type A = LastChar<'BFE'> // 'E'
type B = LastChar<'dev'> // 'v'
type C = LastChar<''> // never
```

### 答案

需要用`infers`将参数首字母提取出来，后面需要补充三目判断符做兜底，递归调用至最后一个字母

```ts
type LastChar<T extends string> = T extends `${infer P}${infer S}` ? (S extends '' ? P : LastChar<S>) : never
```



## [18. implement TupleToUnion\<T>](https://bigfrontend.dev/typescript/implement-TupleToUnion-T)

### 题目

Given a tuple type, implement `TupleToUnion<T>` to get a union type from it.

```ts
type Foo = [string, number, boolean]

type Bar = TupleToUnion<Foo> // string | number | boolean
```

### 答案

需要用数字索引将数组每一个参数提取出来

```ts
type TupleToUnion<T extends any[]> = T[number]
```

也可以利用数组递归调用，后面需要补充三目判断符做兜底

```ts
type TupleToUnion<T extends any[]> = T extends [
  first: infer F,
  ...rest: infer R
] ?
  F | TupleToUnion<R>
  :
  never
```



## [19. implement FirstItem\<T>](https://bigfrontend.dev/typescript/implement-FirstItem-T)

### 题目

Similar to [16. FirstChar](https://bigfrontend.dev/typescript/FirstChar), please implement `FirstItem<T>` to obtain first item of a tuple type.

```ts
type A = FirstItem<[string, number, boolean]> // string
type B = FirstItem<['B', 'F', 'E']> // 'B'
```

#### 答案

需要用数字索引将数组第一个参数提取出来，后面需要补充三目判断符做兜底

```ts
type FirstItem<T extends any[]> = T[0] extends undefined ? never : T[0]
```



## [20. implement IsNever\<T>](https://bigfrontend.dev/typescript/20-IsNever-T)

### 题目

Please implement a utility type `IsNever<T>` which returns true if T is never and false otherwise.

```ts
type A = IsNever<never> // true
type B = IsNever<string> // false
type C = IsNever<undefined> // false
```

### 答案

需要用将数组never提取出来进行判断，因为没有类型extends never

```ts
type IsNever<T> = [T] extends [never] ? true : false
```



## [21. implement LastItem\<T>](https://bigfrontend.dev/typescript/implement-LastItem-T)

### 题目

Similar to [19. FirstItem](https://bigfrontend.dev/typescript/implement-FirstItem-T), please implement `LastItem<T>` to obtain last item of a tuple type.

```ts
type A = LastItem<[string, number, boolean]> // boolean
type B = LastItem<['B', 'F', 'E']> // 'E'
type C = LastItem<[]> // never
```

### 答案

需要用将数组最后一个参数提取出来，后面需要补充三目判断符做兜底

```ts
type LastItem<T extends any[]> = T extends [...any[], infer P] ? P : never
```



## [22. implement StringToTuple\<T>](https://bigfrontend.dev/typescript/implement-StringToTuple-T)

### 题目

Given a string literal type, please implement `StringToTuple<T>` to genrate a tuple type by spreading each characters.

```ts
type A = StringToTuple<'BFE.dev'> // ['B', 'F', 'E', '.', 'd', 'e','v']
type B = StringToTuple<''> // []
```

### 答案

需要用将字符串第一个字母提取出来然后递归调用转化为数组，后面需要补充三目判断符做兜底

```ts
type StringToTuple<T extends string> = 
T extends `${infer P}${infer R}`
? [P, ...StringToTuple<R>]
: []
```



## [23. implement LengthOfTuple\<T>](https://bigfrontend.dev/typescript/implement-LengthOfTuple-T)

### 题目

Here is an easy problem, please implement `LengthOfTuple<T>` to return the length of tuple.

```ts
type A = LengthOfTuple<['B', 'F', 'E']> // 3
type B = LengthOfTuple<[]> // 0
```

### 答案

需要用T['length']方法求得数组长度

```ts
type LengthOfTuple<T extends any[]> = T['length']
```



## [24. implement LengthOfString\<T>](https://bigfrontend.dev/typescript/implement-LengthOfString-T)

### 题目

Implement `LengthOfString<T>` to return the length of a string.

```ts
type A = LengthOfString<'BFE.dev'> // 7
type B = LengthOfString<''> // 0
```

### 答案

结合字符串转数组和计算数组长度计算字符串长度

```ts
type StringToTuple<T extends string> = 
T extends `${infer P}${infer R}`
? [P, ...StringToTuple<R>]
: []
type LengthOfString<T extends string> = StringToTuple<T>['length'] // your code here
```



## [25. implement UnwrapPromise\<T>](https://bigfrontend.dev/typescript/implement-UnwrapPromise-T)

### 题目

Implement `UnwrapPromise<T>` to return the resolved type of a promise.

```ts
type A = UnwrapPromise<Promise<string>> // string
type B = UnwrapPromise<Promise<null>> // null
type C = UnwrapPromise<null> // Error
```

### 答案

结合字符串转数组和计算数组长度计算字符串长度

```ts
type UnwrapPromise<T> = T extends Promise<infer U> ? U : never
```



## [26. implement ReverseTuple\<T>](https://bigfrontend.dev/typescript/implement-ReverseTuple-T)

### 题目

Implement `ReverseTuple<T>` to reverse a tuple type.

```ts
type A = ReverseTuple<[string, number, boolean]> // [boolean, number, string]
type B = ReverseTuple<[1,2,3]> // [3,2,1]
type C = ReverseTuple<[]> // []
```

### 答案

递反转原先的数组

```ts
type ReverseTuple<T extends any[]> = 
T extends [...infer L, infer R] ? [R, ...ReverseTuple<L>] : [];
```



## [27. implement Flat\<T>](https://bigfrontend.dev/typescript/implement-Flat-T)

### 题目

Implement `Flat<T>` to flatten a tuple type.

```ts
type A = Flat<[1,2,3]> // [1,2,3]
type B = Flat<[1,[2,3], [4,[5,[6]]]]> // [1,2,3,4,5,6]
type C = Flat<[]> // []
```

### 答案

从左到右依次递归展开

```ts
type Flat<T extends any[]> = 
T extends [infer L, ...infer R] ? (L extends any[] ? [...Flat<L>, ...Flat<R>] : [L, ...Flat<R>]) : [];
```



## [28. implement IsEmptyType\<T>](https://bigfrontend.dev/typescript/implement-IsEmptyType-T)

### 题目

Implement `IsEmptyType<T>` to check if T is empty type `{}`.

```ts
type A = IsEmptyType<string> // false
type B = IsEmptyType<{a: 3}> // false
type C = IsEmptyType<{}> // true
type D = IsEmptyType<any> // false
type E = IsEmptyType<object> // false
type F = IsEmptyType<Object> // false
```

### 答案

确认是对象且没有键返回true，否则为false

```ts
type IsEmptyType<T> = number extends T
? keyof T extends never
  ? true
  : false
: false
```



## [29. implement Shift\<T>](https://bigfrontend.dev/typescript/implement-Shift-T)

### 题目

Implement `Shift<T>` to shift the first item of a tuple type.

```ts
type A = Shift<[1,2,3]> // [2,3]
type B = Shift<[1]> // []
type C = Shift<[]> // []
```

### 答案

把数组第一个元素删去，后面需要补充三目判断符做兜底

```ts
type Shift<T extends any[]> = T extends [any, ...infer R] ? [...R] : []
```



## [30. implement IsAny\<T>](https://bigfrontend.dev/typescript/implement-IsAny-T)

### 题目

Implement `IsAny<T>` to check against `any`.

```ts
type A = IsAny<string> // false
type B = IsAny<any> // true
type C = IsAny<unknown> // false
type D = IsAny<never> // false
```

### 答案

利用T & any = any的性质且任意类型extends any的性质判断

```ts
type IsAny<T> = false extends true & T ? true : false
```



## [31. implement Push<T, I>](https://bigfrontend.dev/typescript/implement-Push-T-I)

### 题目

Implement `Push<T, I>` to return a new type by pushing new type into tuple type.

```ts
type A = Push<[1,2,3], 4> // [2,3]
type B = Push<[1], 2> // [1, 2]
type C = Push<[], string> // [string]
```

### 答案

拼接数组与新的类型

```ts
type Push<T extends any[], I> = T extends [...infer P] ? [...P, I] : [I]
```

或者直接用展开运算符

```ts
type Push<T extends any[], I> = [...T, I]
```



## [32. implement RepeatString<T, C>](https://bigfrontend.dev/typescript/implement-RepeatString-string-number)

### 题目

Similar to `String.prototype.repeat()`, implement `RepeatString<T, C>` to create a new type by repeating string `T` for `C` times.

```ts
type A = RepeatString<'a', 3> // 'aaa'
type B = RepeatString<'a', 0> // ''
```

### 答案

递归减少C，增加A的长度

```ts
type RepeatString<
  T extends string,
  C extends number,
  A extends string[] = [T]
> = C extends 0 ? '' : (A['length'] extends C ? T : `${T}${RepeatString<T, C, [...A, T]>}`)
```



## [33. implement TupleToString\<T>](https://bigfrontend.dev/typescript/implement-TupleToString-T)

### 题目

Implement `TupleToString<T>` to return a new type by concatenating all the string to a new string type.

```ts
type A = TupleToString<['a']> // 'a'
type B = TupleToString<['B', 'F', 'E']> // 'BFE'
type C = TupleToString<[]> // ''
```

### 答案

拼接字符串，直到剩下的数组长度为0

```ts
type TupleToString<T extends string[]> = 
  T extends [infer P, ...infer U] ? 
    (U extends string[] ?
      `${P & string}${TupleToString<U>}`:
      P
    )
  : 
  ''
```



## [34. implement Repeat<T, C>](https://bigfrontend.dev/typescript/implement-Repeat-T-C)

### 题目

Implement `Repeat<T, C>` to return a tuple by repeating.

```ts
type A = Repeat<number, 3> // [number, number, number]
type B = Repeat<string, 2> // [string, string]
type C = Repeat<1, 1> // [1, 1]
type D = Repeat<0, 0> // []
```

### 答案

> negative case of C could be ignored.

拼接字符串，直到字符串数组长度为等于设置的C

```ts
type Repeat<T, C extends number, A extends T[] = []> = 
A['length'] extends C ? A : Repeat<T, C, [...A, T]>
```



## [35. implement Filter<T, A>](https://bigfrontend.dev/typescript/implement-Filter-T-A)

### 题目

Implement `Filter<T, A>` to return a new tuple type by filtering all the types from T that are assignable to A.

```ts
type A = Filter<[1,'BFE', 2, true, 'dev'], number> // [1, 2]
type B = Filter<[1,'BFE', 2, true, 'dev'], string> // ['BFE', 'dev']
type C = Filter<[1,'BFE', 2, any, 'dev'], string> // ['BFE', any, 'dev']
```

### 答案

把每项展开，如果符合条件加入结果数组中

```ts
type Filter<T extends any[], A> = T extends [infer L, ...infer R]
  ? ([L] extends [A]
    ? [L, ...Filter<R, A>]
    : Filter<R, A>
    )
  : []
```



## [36. implement LargerThan<A, B>](https://bigfrontend.dev/typescript/implement-LargerThan-A-B)

### 题目

Implement `LargerThan<A, B>` to compare two positive integers.

```ts
type A = LargerThan<0, 1> // false
type B = LargerThan<1, 0> // true
type C = LargerThan<10, 9> // true
```

### 答案

把每项展开，如果符合条件加入结果数组中，利用数组长度进行判断

```ts
type LargerThan<A extends number, B extends number, S extends number[] = []> = 
S['length'] extends A
? false
: (S['length'] extends B
  ? true 
  :
  LargerThan<A, B, [...S, A]>
  )
  // Keep pushing the Array until it reaches one of the number
  // the first number being reached is the smaller one
```

如果考虑小数和负数是怎么写的？这样写！

```ts
type NSymbol = 0 | 1

const enum Res {
  Victory,
  Failure,
  Tie
}

interface NumberObject<S extends NSymbol, I extends string[], F extends string[]> {
  symbol: S
  integer: I
  fractional: F
}
type IsNegative<T extends number> = `${T}` extends `-${string}` ? true : false
type IsFractional<T extends number> = `${T}` extends `${string}.${string}` ? true : false

type Split<T extends string, P extends string[] = []> = 
  T extends ''
    ? P
    : T extends `${infer F}${infer R}` 
      ? Split<R, [...P, F]>
      : never


type NumberObjectByNF<T extends number> = 
  `${T}` extends `${infer _}${infer I}.${infer F}`
    ? NumberObject<1, Split<I>, Split<F>>
    : never
type NumberObjectByPF<T extends number> = 
  `${T}` extends `${infer I}.${infer F}`
    ? NumberObject<0, Split<I>, Split<F>>
    : never

type NumberObjectByN<T extends number> =
  `${T}` extends `${infer _}${infer I}`
    ? NumberObject<1, Split<I>, []>
    : never

type NumberObjectByP<T extends number> = NumberObject<0, Split<`${T}`>, []>

type CreateNumberObject<T extends number, N = IsNegative<T>, F = IsFractional<T>> = 
  N extends true 
    ? F extends true
      ? NumberObjectByNF<T>
      : NumberObjectByN<T>
    : F extends true
      ? NumberObjectByPF<T>
      : NumberObjectByP<T>

type NumberCompare<A extends string, B extends string, T extends string[] = [], L = `${T['length']}`> = 
  L extends A
    ? L extends B
      ? Res.Tie
      : Res.Failure
    : L extends B
      ? Res.Victory
      : NumberCompare<A, B, [...T, '_']>

type Pop<T extends string[]> = T[0]
type Shift<T extends string[]> = T extends [infer _, ...infer R] ? R : never

type IsStringArray<T extends unknown> = 
  T extends Array<infer I> ? 
    I extends string 
      ? T 
      : never 
    : never

type NumberArrayCompareHandler<A extends string[], B extends string[]> = 
  A['length'] extends 0
    ? B['length'] extends 0
      ? Res.Tie
      : Res.Failure
    : NumberCompare<Pop<A>, Pop<B>> extends Res.Tie
      ? NumberArrayCompareHandler<IsStringArray<Shift<A>>, IsStringArray<Shift<B>>>
      : NumberCompare<Pop<A>, Pop<B>>


type SymbolCompareResMap = {
  '00': Res.Tie,
  '11': Res.Tie,
  '01': Res.Victory,
  '10': Res.Failure
}
type SymbolCompare<A extends 0 | 1, B extends 0 | 1> = SymbolCompareResMap[`${A}${B}`]

type NumberArrayCompare<A extends string[], B extends string[], CL extends boolean, LR = NumberCompare<`${A['length']}`, `${B['length']}`>> = 
  CL extends true
    ? LR extends Res.Tie
      ? NumberArrayCompareHandler<A, B>
      : LR
    : NumberArrayCompareHandler<A, B>

type LargerThan<
    A extends number, 
    B extends number, 
    AO extends NumberObject<NSymbol, [], []> = CreateNumberObject<A>, 
    BO extends NumberObject<NSymbol, [], []> = CreateNumberObject<B>,
    S =  SymbolCompare<AO['symbol'], BO['symbol']>,
    I = NumberArrayCompare<AO['integer'], BO['integer'], true>,
    F = NumberArrayCompare<AO['fractional'], BO['fractional'], false>
  > = 
    S extends Res.Failure ? false :
    S extends Res.Victory ? true :
      AO['symbol'] extends 1
        ? I extends Res.Failure ? true :
          I extends Res.Victory ? false :
          F extends Res.Failure ? true :
          false
        : I extends Res.Failure ? false :
          I extends Res.Victory ? true :
          F extends Res.Victory ? true :
          false
```



## [37. implement SmallerThan<A, B>](https://bigfrontend.dev/typescript/implement-SmallerThan-A-B)

### 题目

Implement `SmallerThan<A, B>` to compare two positive integers.

```ts
type A = SmallerThan<0, 1> // true
type B = SmallerThan<1, 0> // false
type C = SmallerThan<10, 9> // false
```

### 答案

把每项展开，如果符合条件加入结果数组中，利用数组长度进行判断

```ts
type SmallerThan<A extends number, B extends number, S extends number[] = []> = 
S['length'] extends B
? false
: (S['length'] extends A
  ? true 
  :
  SmallerThan<A, B, [...S, A]>
  )
```



## [38. implement Add<A, B>](https://bigfrontend.dev/typescript/implement-Add-A-B)

### 题目

Implement `Add<A, B>` to get the sum of two positive integers.

```ts
type A = Add<1, 2> // 3
type B = Add<0, 0> // 0
```

### 答案

用两个数组分别记录2个数对应的长度，利用数组长度相加的结果

```ts
type Add<A extends number, B extends number, C extends any[] = [], D extends any[] = []> = 
C['length'] extends A
? (
    D['length'] extends B
    ? [...C, ...D]['length']
    : Add<A, B, C, [...D, B]>
  )
: Add<A, B, [...C, A], D>
```



## [39. implement ToNumber\<T>](https://bigfrontend.dev/typescript/implement-ToNumber-T)

### 题目

Implement `ToNumber<T>` to get an integer number type from integer string.

```ts
type A = ToNumber<'1'> // 1
type B = ToNumber<'40'> // 40
type C = ToNumber<'0'> // 0
```

### 答案

> we can just consider smaller numbers

用数组分别记长度，然后利用模板引擎转化为字符串

```ts
type ToNumber<
  T extends string,
  A extends number[] = []
> = `${A['length']}` extends T ? A['length'] : ToNumber<T, [1, ...A]>;
```



## [40. implement UnionToIntersection\<T>](https://bigfrontend.dev/typescript/implement-UnionToTuple-T)

### 题目

Implement `UnionToIntersection<T>` to get an Intersection type from Union type.

```ts
type A = UnionToIntersection<{a: string} | {b: string} | {c: string}> 
// {a: string} & {b: string} & {c: string}
```

### 答案

The article : [TypeScript: Union to intersection ](https://fettblog.eu/typescript-union-to-intersection/) https://zhuanlan.zhihu.com/p/526988538

After reading about the contra-variance thing, I finally understand why casting `T` to function works. Great explanation, huge kudos to the author 🎉

利用函数的输入参数将T中的|转化为&

```ts
type UnionToIntersection<T> = 
(T extends any ? (args: T) => void : never) extends ((args: infer R) => void) ? R : never;
```



## [41. implement FindIndex<T, E>](https://bigfrontend.dev/typescript/Search)

### 题目

Just like [Array.prototype.findIndex()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/findIndex), please implement `FindIndex<T, E>`.

```ts
type A = [any, never, 1, '2', true]
type B = FindIndex<A, 1> // 2
type C = FindIndex<A, 3> // never
```

### 答案

首先判断2个元素是否相同，再通过数组第一个元素是否相同递归查找有无相同元素，通过一个辅助数组长度来计算个数

```ts
type Equal<A, B> = 
  (<T>() => T extends A ? 1 : 2) extends (<T>() => T extends B ? 1 : 2)
  ? true
  : false;

/*倒叙查找，有问题，如果有重复的，得到的index不是第一个出现的index*/
// type FindIndex<T extends any[], K> = T extends [...infer left, infer last] ? Equal<K, last> extends true ? left["length"] : FindIndex<left, K> : never

/*顺序查找*/
type FindIndex<T extends any[], E, A extends any[] = []> = T extends [
  infer F,
  ...infer R
]
  ? (Equal<F, E> extends true
    ? A['length']
    : FindIndex<R, E, [...A, F]>)
  : never;
```



## [42. implement Equal<A, B>](https://bigfrontend.dev/typescript/Euqual-A-B)

### 题目

Implement `Equal<A, B>` to check if two types are identical.

```ts
Equal<any, any> // true
Equal<any, 1> // false
Equal<never, never> // true
Equal<'BFE', 'BFE'> // true
Equal<'BFE', string> // false
Equal<1 | 2, 2 | 1> // true
Equal<{a : number}, {a: number}> // true
```

### 答案

> there might not be a 100% perfect solution, so are the test cases from us.

> let's all try with our best effort.

其实上一题就需要这种判断方法了

```ts
type Equal<A, B> = 
  (<T>() => T extends A ? 1 : 2) extends (<T>() => T extends B ? 1 : 2)
  ? true
  : false
```



## [43. implement Trim\<T>](https://bigfrontend.dev/typescript/Trim)

### 题目

Please implement `Trim<T>` just like `String.prototype.trim()`.

```ts
type A = Trim<'    BFE.dev'> // 'BFE'
type B = Trim<' BFE. dev  '> // 'BFE. dev'
type C = Trim<'  BFE .   dev  '> // 'BFE .   dev'
```

### 答案

用递归的方法先去除左边的空格，再去除右边的空格

```ts
//extends 如果T包含的类型 是 U包含的类型的 '子集'，那么取结果X，否则取结果Y。
// infer X 就相当于声明了一个变量，这个变量随后可以使用，有点像for循环里面的声明语句
type Trim<T extends string> = T extends ` ${infer R}`
? Trim<R>
: (T extends `${infer L} `
  ? Trim<L>
  : T
  )

// 参考链接
// https://juejin.cn/post/6844904146877808653
// https://zhuanlan.zhihu.com/p/133249506
```



## [44. implement ReplaceAll<S, F, T>](https://bigfrontend.dev/typescript/Replace)

### 题目

Just like `String.prototype.replaceAll()`,

please implement `ReplaceAll<S, F, T>`.

```ts
type A = ReplaceAll<'aba', 'b', ''> // 'aa'
type B = ReplaceAll<'ababbab', 'b', ''> // 'aaa'
```

### 答案

1、前、中、后分别取一下infer。 2、 判断F不为空

```ts
type ReplaceAll<
  S extends string, 
  F extends string, 
  T extends string
  > = F extends "" ? S : 
  (S extends `${F}${infer R}` ? `${T}${ReplaceAll<R, F, T>}` : 
    (S extends `${infer A}${F}${infer B}` ? `${A}${T}${ReplaceAll<B, F, T>}` : 
      S extends `${infer A}${F}` ? `${ReplaceAll<A, F, T>}${T}` : S
    )
  )
```



## [45. implement Slice<A, S, E>](https://bigfrontend.dev/typescript/Slice)

### 题目

Just like what `Array.prototype.slice()` does, please implement Slice<A, S, E>.

```ts
type A = Slice<[1,2,3,4], 0, 2> // [1, 2]
type B = Slice<[1,2,3,4], 2> // [3, 4]
type C = Slice<[number, boolean, bigint], 2, 5> // [bigint]
type D = Slice<[string, boolean], 0, 1> // [string]
type E = Slice<[number, boolean, bigint], 5, 6> // []
```

> Let's assume end index `E` won't be negative

### 答案

采用了每次比较大小的方式，到20时候就爆zhan了， 想到了优化方式：先转化成数组，然后根据数组infer出来

https://typescript.easy-pp.com/modules.html

```ts
type LessThan<A extends number, B extends number, S extends any[] = []> =
  S['length'] extends B
  ? false
  : S['length'] extends A
    ? true
    : LessThan<A, B, [...S, '']>

type Slice<
  A extends any[], // Array
  S extends number = 0, // start
  E extends number = A['length'], // end
  I extends any[] = [], // current index
  O extends any[] = [] // output array
  > =
  A extends [infer F, ...infer R]
  ? LessThan<I['length'], S> extends false // index >= start
    ? LessThan<I['length'], E> extends true // index < end
      ? Slice<R, S, E, [...I, ''], [...O, F]>
      : O // index >= start && index >= end => index >= end => return
    : Slice<R, S, E, [...I, ''], O> // index < start
  : O // A == []
```



## [46. implement Subtract<A, B>](https://bigfrontend.dev/typescript/Subtract-A-B)

### 题目

Similar to [38. implement Add](https://bigfrontend.dev/typescript/implement-Add-A-B), please implement `Subtract<A, B>`.

1. only need to consider positive integers
2. B is guaranteed to be smaller or equal to A

```ts
type A = Subtract<1, 1> // 0
type B = Subtract<10, 3> // 7
type C = Subtract<3, 10> // never
```

### 答案

```ts
type LessThan<A extends number, B extends number, S extends any[] = []> =
  S['length'] extends B
  ? false
  : S['length'] extends A
    ? true
    : LessThan<A, B, [...S, '']>

type Subtract<A extends number, B extends number, I extends any[] = [], O extends any[] = []> =
LessThan<A, B> extends true
? never
: LessThan<I['length'], A> extends true // i < a
  ? Subtract<
    A,
    B,
    [...I, ''], // i++
      LessThan<I['length'], B> extends true ? O : [...O, ''] // !(i < b) => o++
  >
  : O['length'] // return o
```



## [47. implement Multiply<A, B>](https://bigfrontend.dev/typescript/Subtract-A-B)

### 题目

Implement `Multiply<A, B>`

```ts
type A = Multiply<1, 0> // 0
type B = Multiply<4, 6> // 24
```

> Only need to consider non-negative integers.

### 答案

```ts
type RepeatAny <B extends number , C extends any[] = []> =
C['length'] extends B
  ? C
  : RepeatAny<B, [...C, any]>;

type Multiply<A extends number, B extends number, L extends any[] = [], Sum extends any[] = []> =
L["length"] extends A 
?
  Sum["length"]
: 
  Multiply<A, B, [...L, any], [...Sum, ...RepeatAny<B>]>;
```



## [48. implement Divide<A, B>](https://bigfrontend.dev/typescript/Divide-A-B)

### 题目

Implement `Divide<A, B>`

```ts
type A = Divide<1, 0> // never
type B = Divide<4, 2> // 2
type C = Divide<10, 3> // 3
```

> Only need to consider non-negative integers.

### 答案

通过减法实现除法

```ts
type Tuple<T extends number, U extends any[] = []> =
  U['length'] extends T ? U : Tuple<T, [...U, any]>

type Subtract<
  A extends number,
  B extends number
> =
  Tuple<A> extends [...Tuple<B>, ...infer R] ? R['length'] : never

type SmallerThan<
  A extends number,
  B extends number,
  S extends number[] = []
> = S['length'] extends B
  ? false
  : S['length'] extends A
  ? true
  : SmallerThan<A, B, [A, ...S]>

type Divide<A extends number, B extends number, S extends number[] = []> = 
  B extends 0
    ? never
    : SmallerThan<A,B> extends true
      ? S['length']
      : Divide<Subtract<A, B>, B, [...S, any]>;
```



## [49. asserts never](https://bigfrontend.dev/typescript/asserts-never)

### 题目

In `switch`, it is easy for us to miss out some cases.

```ts
type Value = 'a' | 'b' | 'c';
declare let value: Value

switch (value) {
  case 'a':
    break;
  case 'b':
    break;
  default:
    break;
}
```

We missed out case `'c'` in above code and compiler doesn't compain.

Plase create function `assertsNever()` to check for exhaustiveness.

```ts
type Value = 'a' | 'b' | 'c';
declare let value: Value

switch (value) {
  case 'a':
    break;
  case 'b':
    break;
  default:
    assertsNever(value)
    break;
}
```

Now TypeScript would complaint the missing case `'c'`.

### 答案

断言函数输入

```ts
declare const assertsNever: (arg: never) => void;
```

或者

```ts
function assertsNever (arg: never) {
  throw new TypeError(`Missing case ${arg}`);
};
```



## [50. implement Sort\<T>](https://bigfrontend.dev/typescript/sort)

### 题目

You are able to compare two non-negative integers in [LargerThan](https://bigfrontend.dev/typescript/implement-LargerThan-A-B) and [SmallerThan](https://bigfrontend.dev/typescript/implement-SmallerThan-A-B), then you should be able to implement `Sort<T>` in ascending order.

```ts
type A = Sort<[100, 9, 20, 0, 0 55]>
// [0, 0, 9, 55, 100]
```

### 答案

选择排序

```ts
type LessThan<A, B, T extends any[] = []> = 
T["length"] extends B
  ? false
  : T["length"] extends A
    ? true
    :  LessThan<A, B, [...T, any]>;

type Smallest<T extends any[], S extends any = T[0]> =
T extends [infer A,...infer R]
  ? ( LessThan<A, S> extends true
    ? Smallest<R, A>
    : Smallest<R, S>
  )
  : S;

type RemoveFirstOccurence<T extends any [], N extends any, R extends any[] = []> =
T extends [infer A, ...infer Rest]
  ? (A extends N
    ? [...R, ...Rest]
    : RemoveFirstOccurence<Rest, N, [...R, A]>) 
  : R;

type Sort<T extends any[], Sorted extends any[] = []> =
T extends [infer A, ...infer B]
  ? (
    Sort<RemoveFirstOccurence<T, Smallest<[A, ...B]>>, [...Sorted, Smallest<[A, ...B]>]>
  )
  : Sorted;
```

或者

插入排序

判断数字是否在 number[] 中，剔除 number 中的该数字

```ts
type MyExclude<A extends any[], B extends number, N extends any[] = [], F extends boolean = false> = (
  A extends [infer X, ...infer Y]
    ? B extends X
      ? (
        F extends true
          ? MyExclude<Y, B, [...N, X], F>
          : MyExclude<Y, B, N, true>
      )
      : MyExclude<Y, B, [...N, X], F>
    : N
)


type Sort<T extends number[], N extends any[] = [], I extends any[] = []> = (
  T['length'] extends 0 
    ? N
    : (
      I['length'] extends T[number]
        ? Sort<MyExclude<T, I['length']>, [...N, I['length']]>
        : Sort<T, N, [...I, any]>
    )
);
```



## [51. implement Capitalize\<T>](https://bigfrontend.dev/typescript/capitalize)

### 题目

Implement `MyCapitalize<T>` to capitalize strings.

```ts
type A = MyCapitalize<'bfe'> // 'Bfe'
```

> There is built-in `Capitalize<T>` in TypeScript, maybe you can try not to use it.

### 答案

利用Uppercase将首字母转化为大写

```ts
type MyCapitalize<T extends string> = 
T extends `${infer P}${infer U}`
 ? `${Uppercase<P>}${U}` : T;
```



## [52. implement Split\<S, D>](https://bigfrontend.dev/typescript/split)

### 题目

Just like `String.prototype.split()`, please implement `Split<S, D>`.

```ts
type A = Split<'BFE.dev', '.'> // ['BFE', 'dev']
type B = Split<'bfe.dev', 'e'> // ['bf', '.d', 'v']
type C = Split<'bfe.bfe.bfe', 'bfe'> // ['', '.', '.', '']
```

### 答案

利用模板字符串分割

```ts
type Split<S extends string, D extends string, A extends string[] = []> =
S extends `${infer P}${D}${infer R}`
  ? Split<R, D, [...A, P]>
  : [...A, S];
```



## [53. Implement SnakeCase\<S>](https://bigfrontend.dev/typescript/snakecase)

### 题目

Implement `SnakeCase<S>` to turn string in camel case to snake case.

```ts
type A = SnakeCase<'BigFrontEnd'> // big_front_end
```

### 答案

将大写字母转成小写，且除首个之外前面加上`_`

```ts
type isUpperCase<T extends string> = 
T extends ''
  ? false
  : (
    Uppercase<T> extends T ? true : false
  );

type Prefix<T extends string, Skip extends boolean> = 
Skip extends false
  ? (
    isUpperCase<T> extends true
      ? `_${Lowercase<T>}` : T
  ) : Lowercase<T>;

type SnakeCase<S extends string, Skip extends boolean = true> =
S extends `${infer A}${infer B}`
  ? (
    `${Prefix<A, Skip>}${SnakeCase<B, false>}`
  ) : S
```



## [54. Implement CamelCase\<S>](https://bigfrontend.dev/typescript/camelcase)

### 题目

Implement `CamelCase<S>` to turn string in snake case to camel case.

```ts
type A = CamelCase<'big_front_end'> // BigFrontEnd
```

### 答案

模板引擎替换首字母和第一个字母

```ts
type CamelCase<S extends string> = 
S extends `${infer A}_${infer B}` ? 
  `${Capitalize<A>}${CamelCase<B>}`
  : Capitalize<S>
```



## [55. implement StringToNumber\<S>](https://bigfrontend.dev/typescript/implement-StringToNumber-S)

### 题目

Implement `StringToNumber<S>` to convert string to number. You can assume strings are all valid non-negative integers.

```ts
type A = StringToNumber<'12'> // 12
```

### 答案

数组长度将字符串转数组转数字

```ts
type StringToNumber<S extends string, T extends any[] = []> =
 `${T['length']}` extends S ? T['length'] : StringToNumber<S, [...T, any]> 
```



## [56. implement Abs\<N>](https://bigfrontend.dev/typescript/implement-Abs-N)

### 题目

Implement `Abs<N>` to get the absolute value of a number. You can assume the number are integers.

### 答案

负数转字符串再计算长度

```ts
type StringToNumber<S extends string, T extends any[] = []> =
 `${T['length']}` extends S ? T['length'] : StringToNumber<S, [...T, any]> 

type Abs<N extends number> =
`${N}` extends `-${infer A}` ? Abs<StringToNumber<A>> : StringToNumber<`${N}`>
```



## [57. implement ObjectPaths\<O>](https://bigfrontend.dev/typescript/implement-ObjectPaths-O)

### 题目

Implement `ObjectPaths<O>` to return all valid paths to properties.

```ts
type Obj = {
  a: {
    b: {
      c: 1,
      d: 2
    },
    e: 1
  },
  f: 3
}

type A = ObjectPaths<Obj>
// 'a.b.c' | 'a.b.d' | 'a.e' | 'f'
```

### 答案

有难度，直接copy大佬的答案

```ts
type Key = string | number | symbol;

type Join<L extends Key | undefined, R extends Key | undefined> = L extends
  | string
  | number
  ? R extends string | number
    ? `${L}.${R}`
    : L
  : R extends string | number
  ? R
  : undefined;

type Union<
  L extends unknown | undefined,
  R extends unknown | undefined
> = L extends undefined
  ? R extends undefined
    ? undefined
    : R
  : R extends undefined
  ? L
  : L | R;

export type ObjectPaths<
  T extends object,
  Prev extends Key | undefined = undefined,
  Path extends Key | undefined = undefined
> = string &
  {
    [K in keyof T]: 
      // T[K] is an object?.
      T[K] extends object
      ? // Continue extracting
        ObjectPaths<T[K], Union<Prev, Path>, Join<Path, K>>
      : // Return all previous paths, including current key.
        Join<Path, K>;
  }[keyof T];
```



## [58. implement Diff\<A, B>](https://bigfrontend.dev/typescript/implement-Diff-A-B)

### 题目

Implement `DiffKeys<A, B>` to return the keys either in A or B, but not in both.

```ts
type A = DiffKeys<{a: 1, b: 2}, {b: 1, c: 2}> // 'a' | 'c'
```

### 答案

借助Exclude

```ts
type DiffKeys<
  A extends Record<string, any>,
  B extends Record<string, any>
> = Exclude<keyof A, keyof B> | Exclude<keyof B, keyof A>
```

