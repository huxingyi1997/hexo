---
title:  JS中浮点数精度问题
date:  2021-03-26 14:30:00
categories: 
- web前端
tags:
- 计算机组成
- JavaScript
- 浮点数
---
js面试种有一个很经典的问题`0.1 + 0.2≠0.3` 的，该方法涉及`浮点数`的表示方法，以及`浮点数`的加减乘除运算的方法，本文力图在介绍这个问题的基础上解决这个问题，当然，别的语言的双精度浮点数也适用这一方法。

<!-- more -->

## 1.问题的发现

在浮点数运算中出现了精度问题，想通过内置的toFixed修复问题，结果造成了新的问题，一共有2个问题

### 1.1 浮点数运算后的精度问题

  在计算浮点数加减乘除时，会出现精度问题，一些常见的例子如下：

```
// 加法 =====================
0.1 + 0.2 = 0.30000000000000004
0.7 + 0.1 = 0.7999999999999999
0.2 + 0.4 = 0.6000000000000001

// 减法 =====================
1.5 - 1.2 = 0.30000000000000004
0.3 - 0.2 = 0.09999999999999998
 
// 乘法 =====================
19.9 * 100 = 1989.9999999999998
0.8 * 3 = 2.4000000000000004
35.41 * 100 = 3540.9999999999995

// 除法 =====================
0.3 / 0.1 = 2.9999999999999996
0.69 / 10 = 0.06899999999999999
```

### 1.2 toFixed修复产生的新问题

  在遇到浮点数运算后出现的精度问题时，自然联想toFixed()函数来解决的，toFixed()方法可把Number四舍五入为指定小数位数的数字。

  但是在chrome下测试结果不太令人满意：

```
1.35.toFixed(1) // 1.4 正确
1.335.toFixed(2) // 1.33  错误
1.3335.toFixed(3) // 1.333 错误
1.33335.toFixed(4) // 1.3334 正确
1.333335.toFixed(5)  // 1.33333 错误
1.3333335.toFixed(6) // 1.333333 错误
```

使用IETester在IE下面测试的结果却是正确的。



## 2.为什么会产生浮点数精度问题

  想要知道为什么会产生这样的问题，首先，让我们回到计算机组成原理的内容。

### 2.1 浮点数的存储

  和其它语言如Java和Python不同，JavaScript中所有数在BigInt出现之前包括整数和小数，都只有一种类型——Number。它的实现遵循 IEEE 754 标准，使用64位固定长度来表示，也就是标准的 double 双精度浮点数（相关的还有float 32位单精度浮点数）。这样的存储结构优点是可以归一化处理整数和小数，节省存储空间。

  64位bit又可分为三个部分：

- 符号位S：第 1 位是正负数符号位（sign），0代表正数，1代表负数
- 指数位E：中间的 11 位存储指数（exponent），用来表示次方数
- 尾数位M：最后的 52 位是尾数（mantissa），超出的部分自动进一舍零

![Storage](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210423144825.webp)

### 2.2 浮点数的运算

  以计算0.1+0.2为例，分析JavaScript在浮点数计算时到底发生了什么呢？

  首先，十进制的0.1和0.2会被转换成二进制，但是由于浮点数用二进制表示时是无穷的：

```
0.1 -> 0.0001 1001 1001 1001...(1100循环)
0.2 -> 0.0011 0011 0011 0011...(0011循环)
```

  IEEE 754 标准的 64 位双精度浮点数的小数部分最多支持53位二进制位，所以两者相加之后得到二进制为：

```
0.0100110011001100110011001100110011001100110011001100 
```

  因浮点数小数位的限制而截断的二进制数字，再转换为十进制，就成了0.30000000000000004。所以在进行算术计算时会产生误差。



## 3 精度问题的解决方法

  针对两个精度问题，有如下解决方案

### 3.1 解决toFixed的问题

  针对toFixed的兼容性问题，可以通过重写toFix函数来解决，代码如下：

```
// toFixed兼容方法
Number.prototype.toFixed = function(len){
    if(le n> 20 || len < 0){
        throw new RangeError('toFixed() digits argument must be between 0 and 20');
    }
    // 讲.123转为0.123
    var number = Number(this);
    if (isNaN(number) || number >= Math.pow(10, 21)) {
        return number.toString();
    }
    if (typeof (len) == 'undefined' || len == 0) {
        return (Math.round(number)).toString();
    }
    var result = number.toString(),
        numberArr = result.split('.');

    if(numberArr.length<2){
        //整数的情况
        return padNum(result);
    }
    var intNum = numberArr[0], //整数部分
        deciNum = numberArr[1],//小数部分
        lastNum = deciNum.substr(len, 1);//最后一个数字
    
    if(deciNum.length == len){
        //需要截取的长度等于当前长度
        return result;
    }
    if(deciNum.length < len){
        //需要截取的长度大于当前长度 1.3.toFixed(2)
        return padNum(result)
    }
    //需要截取的长度小于当前长度，需要判断最后一位数字
    result = intNum + '.' + deciNum.substr(0, len);
    if(parseInt(lastNum, 10)>=5){
        //最后一位数字大于5，要进位
        var times = Math.pow(10, len); //需要放大的倍数
        var changedInt = Number(result.replace('.',''));//截取后转为整数
        changedInt++;//整数进位
        changedInt /= times;//整数转为小数，注：有可能还是整数
        result = padNum(changedInt+'');
    }
    return result;
    //对数字末尾加0
    function padNum(num){
        var dotPos = num.indexOf('.');
        if(dotPos === -1){
            //整数的情况
            num += '.';
            for(var i = 0;i<len;i++){
                num += '0';
            }
            return num;
        } else {
            //小数的情况
            var need = len - (num.length - dotPos - 1);
            for(var j = 0;j<need;j++){
                num += '0';
            }
            return num;
        }
    }
}
```

  我们通过判断最后一位是否大于等于5来决定需不需要进位，如果需要进位先把小数乘以倍数变为整数，加1之后，再除以倍数变为小数，这样就不用一位一位的进行判断是否需要进位。

### 3.2 解决浮点数运算精度

  既然我们发现了浮点数的这个问题，又不能直接让两个浮点数运算，那怎么处理呢？

  我们可以把需要计算的数字升级（乘以10的n次幂）成计算机能够精确识别的整数，等计算完成后再进行降级（除以10的n次幂），这是大部分变成语言处理精度问题常用的方法。例如：

```
0.1 + 0.2 == 0.3 // false
(0.1 * 10 + 0.2 * 10) / 10 == 0.3 // true
```

  但是这样就能完美解决上述问题么？细心的读者可能在上面的例子里已经发现了问题：

```
35.41 * 100 = 3540.9999999999995
```

  看来进行数字升级也不是完全的可靠啊。

  但是魔高一尺道高一丈，这样就能难住我们么，我们可以将浮点数toString后indexOf('.')，记录一下小数位的长度，然后将小数点抹掉，完整的代码如下：

```
 /*** method **
 *  add / subtract / multiply /divide
 * floatObj.add(0.1, 0.2) >> 0.3
 * floatObj.multiply(19.9, 100) >> 1990
 *
 */
var floatObj = function() {

    /*
     * 判断obj是否为一个整数
     */
    function isInteger(obj) {
        return Math.floor(obj) === obj
    }

    /*
     * 将一个浮点数转成整数，返回整数和倍数。如 3.14 >> 314，倍数是 100
     * @param floatNum {number} 小数
     * @return {object}
     *   {times:100, num: 314}
     */
    function toInteger(floatNum) {
        var ret = {times: 1, num: 0}
        if (isInteger(floatNum)) {
            ret.num = floatNum
            return ret
        }
        var strfi  = floatNum + ''
        var dotPos = strfi.indexOf('.')
        var len    = strfi.substr(dotPos+1).length
        var times  = Math.pow(10, len)
        var intNum = Number(floatNum.toString().replace('.',''))
        ret.times  = times
        ret.num    = intNum
        return ret
    }

    /*
     * 核心方法，实现加减乘除运算，确保不丢失精度
     * 思路：把小数放大为整数（乘），进行算术运算，再缩小为小数（除）
     *
     * @param a {number} 运算数1
     * @param b {number} 运算数2
     * @param digits {number} 精度，保留的小数点数，比如 2, 即保留为两位小数
     * @param op {string} 运算类型，有加减乘除（add/subtract/multiply/divide）
     *
     */
    function operation(a, b, digits, op) {
        var o1 = toInteger(a)
        var o2 = toInteger(b)
        var n1 = o1.num
        var n2 = o2.num
        var t1 = o1.times
        var t2 = o2.times
        var max = t1 > t2 ? t1 : t2
        var result = null
        switch (op) {
            case 'add':
                if (t1 === t2) { // 两个小数位数相同
                    result = n1 + n2
                } else if (t1 > t2) { // o1 小数位 大于 o2
                    result = n1 + n2 * (t1 / t2)
                } else { // o1 小数位 小于 o2
                    result = n1 * (t2 / t1) + n2
                }
                return result / max
            case 'subtract':
                if (t1 === t2) {
                    result = n1 - n2
                } else if (t1 > t2) {
                    result = n1 - n2 * (t1 / t2)
                } else {
                    result = n1 * (t2 / t1) - n2
                }
                return result / max
            case 'multiply':
                result = (n1 * n2) / (t1 * t2)
                return result
            case 'divide':
                result = (n1 / n2) * (t2 / t1)
                return result
        }
    }

    // 加减乘除的四个接口
    function add(a, b, digits) {
        return operation(a, b, digits, 'add')
    }
    function subtract(a, b, digits) {
        return operation(a, b, digits, 'subtract')
    }
    function multiply(a, b, digits) {
        return operation(a, b, digits, 'multiply')
    }
    function divide(a, b, digits) {
        return operation(a, b, digits, 'divide')
    }

    // exports
    return {
        add: add,
        subtract: subtract,
        multiply: multiply,
        divide: divide
    }
}();
```

  如果觉得floatObj调用麻烦，我们可以在Number.prototype上添加对应的运算方法floatObj。


