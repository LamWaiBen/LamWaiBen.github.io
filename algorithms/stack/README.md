## Stack
栈结构常用于处理:
1. 符号闭合问题(括号, AST的编译)
2. 单调递增栈  
3. 逆波兰表达式


### 符号闭合



### 单调栈

代码模板
```javascript
// 单调递减栈模板
function decStackTemplate(arr){
    const stack = [arr[0]]
    for(let i = 1; i < arr.length; i++) {
        // 当新元素比栈顶元素大的时候, 需要先出栈
        while(stack.length > 0 && stack[stack.length - 1] < arr[i]) {
            const cur = stack.pop()
            // do something  cur & arr[i]
        }
        stack.push(arr[i])
    }
}

```

### 逆波兰表达式的解析
逆波兰表达式(后缀表达式):  计算机易于读的计算表达式, 如: `ab+c*` 转换成中缀表达式 `(a + b) * c`

```javascript
// number 入栈, 遇到符号时出栈, 并把运算结果再次入栈
const arr = [10, 2, "+", 3, "*"]
function rpn(arr){
    let sum = 0, stack = [];
    for(let v of arr){
        if(typeof v === 'number') {
            stack.push(v)
        } else {
            sum = stack.reduce((a, b) => {
                if(a === null) return b
                if(v === "+") return a + b
                if(v === "-") return a - b
                if(v === "*") return a * b
                if(v === "/") return a / b
            }, null)
            stack = [sum]
        }
    }
    return sum
}

rpn(arr) // 36
```


### leetcode
#### 单调递增/减栈
- 42. 接雨水
- 84. 柱状图中最大的矩形
- 496. 下一个更大元素