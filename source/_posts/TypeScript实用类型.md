---
title:  TypeScript实用类型
date: 2021-08-28 00:00:00
categories: 
- web前端
tags:
- TypeScript
- 实用类型
---

面试被问到partial，一脸懵逼。`TypeScript`标准库自带了一些实用类型。这些实用类都是方便接口`Interface`使用。这里只列举几个常用的，更多实用类型[官网](https://link.juejin.cn/?target=https%3A%2F%2Fwww.typescriptlang.org%2Fdocs%2Fhandbook%2Futility-types.html)

<!-- more -->

### 1. Exclude

从一个类型中排除另一个类型，只能是**联合类型**，从`TypesTest`类型中排除`UtilityLast`类型。

**适用于：并集类型**

```typescript
interface UtilityFirst {
    name: string;
}

interface UtilityLast {
    age: number;
}

type TypesTest = UtilityFirst | UtilityLast;

const ObjJson: Exclude<TypesTest, UtilityLast> = {
    name: "前端娱乐圈",
};
```

### 2. Extract

`Extract`正好跟上面那个相反，这是选择某一个可赋值的**联合类型**，从`TypesTest`类型中只选择`UtilityLast`类型。

**适用于：并集类型**

```typescript
interface UtilityFirst {
    name: string;
}

interface UtilityLast {
    age: number;
}

type TypesTest = UtilityFirst | UtilityLast;

const ObjJson: Extract<TypesTest, UtilityLast> = {
    age: 1
};
```

### 3. Readonly

把数组或对象的所有属性值转换为只读的。这里只演示一下对象栗子，数组同样的写法。

**适用于：对象、数组**

```typescript
interface UtilityFirst {
    name: string;
}

const ObjJson: Readonly<UtilityFirst> = {
    name: "前端娱乐圈"
}
ObjJson.name = "蛙人"; // 报错 只读状态
```

### 4. Partial

把对象的所有属性设置为选的。我们知道`interface`只要不设置`?`修饰符，那么对象都是必选的。这个实用类可以将属性全部转换为可选的。

**适用于：对象**

```typescript
interface UtilityFirst {
    name: string;
}

const ObjJson: Partial<UtilityFirst> = {};
```

### 5. Pick

Pick选择对象类型中的部分`key`值，提取出来。第一个参数`目标值`，第二个参数**联合**`key`

**适用于：对象**

```typescript
interface UtilityFirst {
    name: string;
    age: number;
    hobby: [];
}

const ObjJson: Pick<UtilityFirst, "name" | "age"> = {
    name: "前端娱乐圈",
    age: 18
};
```

### 6. Omit

Omit选择对象类型中的部分`key`值，过滤掉。第一个参数`目标值`，第二个参数**联合**`key`

**适用于：对象**

```typescript
interface UtilityFirst {
    name: string;
    age: number;
    hobby: string[];
}

const ObjJson: Pick<UtilityFirst, "name" | "age"> = {
    hobby: ["code", "羽毛球"]
};
```

### 7. Required

`Required`把对象所有可选属性转换成必选属性。

**适用于：对象**

```typescript
interface UtilityFirst {
    name?: string;
    age?: number;
    hobby?: string[];
}

const ObjJson: Required<UtilityFirst> = {
    name: "蛙人",
    age: 18,
    hobby: ["code"]
};
```

### 8. Record

创建一个对象结果集，第一个参数则是`key`值，第二个参数则是`value`值。规定我们只能创建这里面字段值。

**适用于：对象**

```typescript
type IndexList = 0 | 1 | 2;

const ObjJson: Record<IndexList, "前端娱乐圈"> = {
    0: "前端娱乐圈",
    1: "前端娱乐圈",
    2: "前端娱乐圈"
};
```

