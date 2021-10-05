---
title:  bigfrontend的TS题目讨论
date: 2021-06-17 21:07:33
categories: 
- web前端
tags:
- bigfrontend
- 前端
- 题库
---

国外前端会考察哪些问题？js函数、系统设计、TS类型体操的用法，大大拓宽了我的眼界，去思考和实战更加相关的问题吧，还有一个Type-challenges，经常更新一个新的Type实现：[TypeScript 类型体操姿势合集](https://github.com/type-challenges/type-challenges)
<!-- more -->

## TypeScript类型体操

这一部分很有意思，你写过一遍之后，对于TS里怎么用这些方法更加得心应手

### 1. implement Partial\<T>

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

My code:

找到键的索引，全部用`?`转化为可选

```ts
type MyPartial<T> = {
  [key in keyof T] ?: T[key];
}
```

### 2. implement Required\<T>

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

My code:

找到键的索引，全部用`-?`转化为必选

```ts
type MyPartial<T> = {
  [key in keyof T] -?: T[key];
}
```

### 3. implement Readonly\<T>

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

My code:

找到键的索引，全部用`readonly`转化为只读属性

```ts
type MyReadonly<T> = {
  readonly [key in keyof T]: T[key]
}
```

### 4. implement Record<K, V>

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

这个稍微复杂一些，需要用`extends`表明键的类型

```ts
type MyRecord <K extends string | number | symbol, V> = {
  [key in K]: V
}
```

### 5. implement Pick<T, K>

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

同样，需要用`extends`表明键的范围

```ts
type MyPick<T, K extends keyof T> = {
  [key in K]: T[key]
}
```

### 6. implement Omit<T, K>

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

需要用`extends`结合三目判断符进行判断，将找不到的键名赋值为never

```ts
type MyOmit<T, K extends keyof any> = {
  [key in keyof T as key extends K ? never : key]: T[key]
}
```

### 7. implement Exclude<T, E>

`Exclude<T, K>` returns a type by removing from T the union members that are assignable to K.

Please implement `MyExclude<T, K>` by yourself.

```ts
type Foo = 'a' | 'b' | 'c'

type A = MyExclude<Foo, 'a'> // 'b' | 'c'
type B = MyExclude<Foo, 'c'> // 'a' | 'b
type C = MyExclude<Foo, 'c' | 'd'>  // 'a' | 'b'
type D = MyExclude<Foo, 'a' | 'b' | 'c'>  // never
```

需要用`extends`结合三目判断符进行判断，将找不到的键名赋值为never

```ts
type MyExclude<T, E> = T extends E ? never : T
```

### 8. implement Extract<T, U>

As the opposite of [Exclude](https://bigfrontend.dev/typescript/implement-Exclude-T-E), `Extract<T, U>` returns a type by extracting union members from T that are assignable to U.

Please implement `MyExtract<T, U>` by yourself.

```ts
type Foo = 'a' | 'b' | 'c'

type A = MyExtract<Foo, 'a'> // 'a'
type B = MyExtract<Foo, 'a' | 'b'> // 'a' | 'b'
type C = MyExtract<Foo, 'b' | 'c' | 'd' | 'e'>  // 'b' | 'c'
type D = MyExtract<Foo, never>  // never
```

需要用`extends`结合三目判断符进行判断，将找不到的键名赋值为never

```ts
type MyExtract<T, U> = T extends U ? T : never
```

### 9. implement NonNullable\<T>

`NonNullable<T>` returns a type by excluding `null` and `undefined` from T.

Please implement `MyNonNullable<T>` by yourself.

```ts
type Foo = 'a' | 'b' | null | undefined

type A = MyNonNullable<Foo> // 'a' | 'b'
```

需要用`extends`结合三目判断符进行判断，将找不到的键名赋值为never

```ts
type MyExtract<T, U> = T extends U ? T : never
```

### 10. implement Parameters\<T>

For function type T, `Parameters<T>` returns a tuple type from the types of its parameters.

Please implement `MyParameters<T>` by yourself.

```ts
type Foo = (a: string, b: number, c: boolean) => string

type A = MyParameters<Foo> // [a:string, b: number, c:boolean]
type B = A[0] // string
type C = MyParameters<{a: string}> // Error
```

需要用`infers`将输入参数提取出来，后面需要补充三目判断符做兜底

```ts
type MyParameters<T extends (...args: any[]) => any> = T extends (...args: infer P) => any ? P : never;
```

### 11. implement ConstructorParameters\<T>

[Parameters](https://bigfrontend.dev/typescript/Parameters) handles function type. Similarly, `ConstructorParameters<T>` is meant for class, it returns a tuple type from the types of a constructor function T.

Please implement `MyConstructorParameters<T>` by yourself.

```ts
class Foo {
  constructor (a: string, b: number, c: boolean) {}
}

type C = MyConstructorParameters<typeof Foo> 
// [a: string, b: number, c: boolean]
```

需要用`infers`将输入参数提取出来，后面需要补充三目判断符做兜底

```ts
type MyConstructorParameters<T extends new (...args: any) => any> =
T extends new (...args: infer P) => any ? P : never
```

### 12. implement ReturnType\<T>

Similar to [Parameters](https://bigfrontend.dev/typescript/Parameters), `ReturnType<T>`, as the name says itself, returns the return type of function type T.

Please implement `MyReturnType<T>` by yourself.

```ts
type Foo = () => {a: string}

type A = MyReturnType<Foo> // {a: string}
```

需要用`infers`将结果提取出来，后面需要补充三目判断符做兜底

```ts
type MyReturnType<T extends (...args: any[]) => any> =
T extends (...args: any[]) => infer P ? P : never
```

### 13. implement InstanceType\<T>

For a constructor function type T, `InstanceType<T>` returns the instance type.

Please implement `MyInstanceType<T>` by yourself.

```ts
class Foo {}
type A = MyInstanceType<typeof Foo> // Foo
type B = MyInstanceType<() => string> // Error
```

需要用`infers`将结果提取出来，后面需要补充三目判断符做兜底

```ts
type MyInstanceType<T extends new (...args: any[]) => any> = 
T extends new (...args: any[]) => infer P ? P : never
```

### 14. implement ThisParameterType\<T>

For a function type T, `ThisParameterType<T>` extracts the `this` type. If `this` is not set, `unknown` is used.

Please implement `MyThisParameterType<T>` by yourself.

```ts
function Foo(this: {a: string}) {}
function Bar() {}

type A = MyThisParameterType<typeof Foo> // {a: string}
type B = MyThisParameterType<typeof Bar> // unknown
```

需要用`infers`将第一个输入参数提取出来，后面需要补充三目判断符做兜底

```ts
type MyThisParameterType<T extends (...args: any[]) => any> = 
T extends (this: infer P, ...args: any[]) => any ? P : unknown
```

### 15. implement OmitThisParameter\<T>

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

需要用`infers`将第一个输入参数提取出来，后面需要补充三目判断符做兜底

```ts
type MyOmitThisParameter<T extends (...args: any[]) => any> = 
T extends (this: any, ...args: infer P) => infer Q ? (...args: P) => Q : unknown
```

### 16. implement FirstChar\<T>

Please implement `FirstChar<T>` to get the first character of a string.

```ts
type A = FirstChar<'BFE'> // 'B'
type B = FirstChar<'dev'> // 'd'
type C = FirstChar<''> // never
```

需要用`infers`将参数首字母提取出来，后面需要补充三目判断符做兜底

```ts
type FirstChar<T extends string> = T extends `${infer S}${any}` ? S : never
```

### 17. implement LastChar\<T>

Similar to [FirstChar](https://bigfrontend.dev/typescript/FirstChar), please implment `LastChar<T>` to get the last character.

```ts
type A = LastChar<'BFE'> // 'E'
type B = LastChar<'dev'> // 'v'
type C = LastChar<''> // never
```

需要用`infers`将参数首字母提取出来，后面需要补充三目判断符做兜底

```ts
type LastChar<T extends string> = T extends `${infer P}${infer S}` ? (S extends '' ? P : LastChar<S>) : never
```

### 18. implement TupleToUnion\<T>

Given a tuple type, implement `TupleToUnion<T>` to get a union type from it.

```ts
type Foo = [string, number, boolean]

type Bar = TupleToUnion<Foo> // string | number | boolean
```

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

### 19. implement FirstItem\<T>

Similar to [16. FirstChar](https://bigfrontend.dev/typescript/FirstChar), please implement `FirstItem<T>` to obtain first item of a tuple type.

```ts
type A = FirstItem<[string, number, boolean]> // string
type B = FirstItem<['B', 'F', 'E']> // 'B'
```

需要用数字索引将数组第一个参数提取出来，后面需要补充三目判断符做兜底

```ts
type FirstItem<T extends any[]> = T[0] extends undefined ? never : T[0]
```

### 20. implement IsNever\<T>

Please implement a utility type `IsNever<T>` which returns true if T is never and false otherwise.

```ts
type A = IsNever<never> // true
type B = IsNever<string> // false
type C = IsNever<undefined> // false
```

需要用将数组never提取出来进行判断，因为没有类型extends never

```ts
type IsNever<T> = [T] extends [never] ? true : false
```

### 21. implement LastItem\<T>

Similar to [19. FirstItem](https://bigfrontend.dev/typescript/implement-FirstItem-T), please implement `LastItem<T>` to obtain last item of a tuple type.

```ts
type A = LastItem<[string, number, boolean]> // boolean
type B = LastItem<['B', 'F', 'E']> // 'E'
type C = LastItem<[]> // never
```

需要用将数组最后一个参数提取出来，后面需要补充三目判断符做兜底

```ts
type LastItem<T extends any[]> = T extends [...any[], infer P] ? P : never
```

### 22. implement StringToTuple\<T>

Given a string literal type, please implement `StringToTuple<T>` to genrate a tuple type by spreading each characters.

```ts
type A = StringToTuple<'BFE.dev'> // ['B', 'F', 'E', '.', 'd', 'e','v']
type B = StringToTuple<''> // []
```

需要用将字符串第一个字母提取出来然后递归调用转化为数组，后面需要补充三目判断符做兜底

```ts
type StringToTuple<T extends string> = 
T extends `${infer P}${infer R}`
? [P, ...StringToTuple<R>]
: []
```

### 23. implement LengthOfTuple\<T>

Here is an easy problem, please implement `LengthOfTuple<T>` to return the length of tuple.

```ts
type A = LengthOfTuple<['B', 'F', 'E']> // 3
type B = LengthOfTuple<[]> // 0
```

需要用T['length']方法求得数组长度

```ts
type LengthOfTuple<T extends any[]> = T['length']
```

### 24. implement LengthOfString\<T>

Implement `LengthOfString<T>` to return the length of a string.

```ts
type A = LengthOfString<'BFE.dev'> // 7
type B = LengthOfString<''> // 0
```

结合字符串转数组和计算数组长度计算字符串长度

```ts
type StringToTuple<T extends string> = 
T extends `${infer P}${infer R}`
? [P, ...StringToTuple<R>]
: []
type LengthOfString<T extends string> = StringToTuple<T>['length'] // your code here
```

### 25. implement UnwrapPromise\<T>

Implement `UnwrapPromise<T>` to return the resolved type of a promise.

```ts
type A = UnwrapPromise<Promise<string>> // string
type B = UnwrapPromise<Promise<null>> // null
type C = UnwrapPromise<null> // Error
```

结合字符串转数组和计算数组长度计算字符串长度

```ts
type UnwrapPromise<T> = T extends Promise<infer U> ? U : never
```

### 26. implement ReverseTuple\<T>

Implement `ReverseTuple<T>` to reverse a tuple type.

```ts
type A = ReverseTuple<[string, number, boolean]> // [boolean, number, string]
type B = ReverseTuple<[1,2,3]> // [3,2,1]
type C = ReverseTuple<[]> // []
```

递反转原先的数组

```ts
type ReverseTuple<T extends any[]> = 
T extends [...infer L, infer R] ? [R, ...ReverseTuple<L>] : [];
```

### 27. implement Flat\<T>

Implement `Flat<T>` to flatten a tuple type.

```ts
type A = Flat<[1,2,3]> // [1,2,3]
type B = Flat<[1,[2,3], [4,[5,[6]]]]> // [1,2,3,4,5,6]
type C = Flat<[]> // []
```

从左到右依次递归展开

```ts
type Flat<T extends any[]> = 
T extends [infer L, ...infer R] ? (L extends any[] ? [...Flat<L>, ...Flat<R>] : [L, ...Flat<R>]) : [];
```

### 28. implement IsEmptyType\<T>

Implement `IsEmptyType<T>` to check if T is empty type `{}`.

```ts
type A = IsEmptyType<string> // false
type B = IsEmptyType<{a: 3}> // false
type C = IsEmptyType<{}> // true
type D = IsEmptyType<any> // false
type E = IsEmptyType<object> // false
type F = IsEmptyType<Object> // false
```

确认是对象且没有键返回true，否则为false

```ts
type IsEmptyType<T> = number extends T
? keyof T extends never
  ? true
  : false
: false
```

### 29. implement Shift\<T>

Implement `Shift<T>` to shift the first item of a tuple type.

```ts
type A = Shift<[1,2,3]> // [2,3]
type B = Shift<[1]> // []
type C = Shift<[]> // []
```

把数组第一个元素删去，后面需要补充三目判断符做兜底

```ts
type Shift<T extends any[]> = T extends [any, ...infer R] ? [...R] : []
```

### 30. implement IsAny\<T>

Implement `IsAny<T>` to check against `any`.

```ts
type A = IsAny<string> // false
type B = IsAny<any> // true
type C = IsAny<unknown> // false
type D = IsAny<never> // false
```

利用T & any = any的性质且任意类型extends any的性质判断

```ts
type IsAny<T> = false extends true & T ? true : false
```

### 31. implement Push<T, I>

Implement `Push<T, I>` to return a new type by pushing new type into tuple type.

```ts
type A = Push<[1,2,3], 4> // [2,3]
type B = Push<[1], 2> // [1, 2]
type C = Push<[], string> // [string]
```

拼接数组与新的类型

```ts
type Push<T extends any[], I> = T extends [...infer P] ? [...P, I] : [I]
```

或者直接用展开运算符

```
type Push<T extends any[], I> = [...T, I]
```

### 32. implement RepeatString<T, C>

Similar to `String.prototype.repeat()`, implement `RepeatString<T, C>` to create a new type by repeating string `T` for `C` times.

```ts
type A = RepeatString<'a', 3> // 'aaa'
type B = RepeatString<'a', 0> // ''
```

递归减少C，增加A的长度

```ts
type RepeatString<
  T extends string,
  C extends number,
  A extends string[] = [T]
> = C extends 0 ? '' : (A['length'] extends C ? T : `${T}${RepeatString<T, C, [...A, T]>}`)
```

### 33. implement TupleToString\<T>

Implement `TupleToString<T>` to return a new type by concatenating all the string to a new string type.

```ts
type A = TupleToString<['a']> // 'a'
type B = TupleToString<['B', 'F', 'E']> // 'BFE'
type C = TupleToString<[]> // ''
```

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

### 34. implement Repeat<T, C>

Implement `Repeat<T, C>` to return a tuple by repeating.

```ts
type A = Repeat<number, 3> // [number, number, number]
type B = Repeat<string, 2> // [string, string]
type C = Repeat<1, 1> // [1, 1]
type D = Repeat<0, 0> // []
```

> negative case of C could be ignored.

拼接字符串，直到字符串数组长度为等于设置的C

```ts
type Repeat<T, C extends number, A extends T[] = []> = 
A['length'] extends C ? A : Repeat<T, C, [...A, T]>
```

### 35. implement Filter<T, A>

Implement `Filter<T, A>` to return a new tuple type by filtering all the types from T that are assignable to A.

```ts
type A = Filter<[1,'BFE', 2, true, 'dev'], number> // [1, 2]
type B = Filter<[1,'BFE', 2, true, 'dev'], string> // ['BFE', 'dev']
type C = Filter<[1,'BFE', 2, any, 'dev'], string> // ['BFE', any, 'dev']
```

把每项展开，如果符合条件加入结果数组中

```ts
type Filter<T extends any[], A> = T extends [infer L, ...infer R]
  ? ([L] extends [A]
    ? [L, ...Filter<R, A>]
    : Filter<R, A>
    )
  : []
```

### 36. implement LargerThan<A, B>

Implement `LargerThan<A, B>` to compare two positive integers.

```ts
type A = LargerThan<0, 1> // false
type B = LargerThan<1, 0> // true
type C = LargerThan<10, 9> // true
```

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
```

### 37. implement SmallerThan<A, B>

Implement `SmallerThan<A, B>` to compare two positive integers.

```ts
type A = SmallerThan<0, 1> // true
type B = SmallerThan<1, 0> // false
type C = SmallerThan<10, 9> // false
```

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

### 38. implement Add<A, B>

Implement `Add<A, B>` to get the sum of two positive integers.

```ts
type A = Add<1, 2> // 3
type B = Add<0, 0> // 0
```

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

### 39. implement ToNumber\<T>

Implement `ToNumber<T>` to get an integer number type from integer string.

```ts
type A = ToNumber<'1'> // 1
type B = ToNumber<'40'> // 40
type C = ToNumber<'0'> // 0
```

> we can just consider smaller numbers

用数组分别记长度，然后利用模板引擎转化为字符串

```ts
type ToNumber<
  T extends string,
  A extends number[] = []
> = `${A['length']}` extends T ? A['length'] : ToNumber<T, [1, ...A]>;
```

### 40. implement UnionToIntersection\<T>

Implement `UnionToIntersection<T>` to get an Intersection type from Union type.

```ts
type A = UnionToIntersection<{a: string} | {b: string} | {c: string}> 
// {a: string} & {b: string} & {c: string}
```

利用函数的输入参数将T中的|转化为&

```ts
type UnionToIntersection<T> = 
(T extends any ? (args: T) => void : never) extends ((args: infer R) => void) ? R : never;
```

### 41. implement FindIndex<T, E>

Just like [Array.prototype.findIndex()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/findIndex), please implement `FindIndex<T, E>`.

```ts
type A = [any, never, 1, '2', true]
type B = FindIndex<A, 1> // 2
type C = FindIndex<A, 3> // never
```

首先判断2个元素是否相同，再通过数组第一个元素是否相同递归查找有无相同元素，通过一个辅助数组长度来计算个数

```ts
type Equal<A, B> = 
  (<T>() => T extends A ? 1 : 2) extends (<T>() => T extends B ? 1 : 2)
  ? true
  : false;

type FindIndex<T extends any[], E, A extends any[] = []> = T extends [
  infer F,
  ...infer R
]
  ? (Equal<F, E> extends true
    ? A['length']
    : FindIndex<R, E, [...A, F]>)
  : never;
```

### 42. implement Equal<A, B>

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

> there might not be a 100% perfect solution, so are the test cases from us.

> let's all try with our best effort.

其实上一题就需要这种判断方法了

```ts
type Equal<A, B> = 
  (<T>() => T extends A ? 1 : 2) extends (<T>() => T extends B ? 1 : 2)
  ? true
  : false
```

### 43. implement Trim\<T>

Please implement `Trim<T>` just like `String.prototype.trim()`.

```ts
type A = Trim<'    BFE.dev'> // 'BFE'
type B = Trim<' BFE. dev  '> // 'BFE. dev'
type C = Trim<'  BFE .   dev  '> // 'BFE .   dev'
```

用递归的方法先去除左边的空格，再去除右边的空格

```ts
type Trim<T extends string> = T extends ` ${infer R}`
? Trim<R>
: (T extends `${infer L} `
  ? Trim<L>
  : T
  )
```

