---

title: "聊聊前端优化手段防抖"
date: 2017-03-16 16:29:09 +0800
comments: true
categories: Frontend
tags: [JavaScript]
top: true
---

在前端开发中，**防抖（Debouncing）** 是一种常用的优化技术，用于处理频繁触发的事件，如浏览器窗口的resize、input输入等。**防抖的目标是在事件被触发后，等待一段时间，只执行一次事件处理函数，以避免频繁的重复操作**。

**防抖的原理**很简单：当一个事件被触发时，立即设置一个定时器，在规定的时间内没有再次触发该事件时，执行事件处理函数。如果在定时器规定的时间内再次触发了事件，那么就清除前一个定时器，并重新设置新的定时器。这样，只有在事件停止触发一段时间后，才会执行事件处理函数。

以下是一个防抖的基本实现示例（使用 JavaScript）：

```JavaScript
function debounce(func, delay) {
    let timer = null;
    return function () {
        const context = this;
        const args = arguments;

        clearTimeout(timer); // 每次执行时，清除之前的定时器
        timer = setTimeout(function () { //设置新的定时器
            func.apply(context, args);
        }, delay);
    }
}

const debounceFunction = debounce((args) => {
    console.log("Debounce function called", args);
}, 500);

window.addEventListener('resize', debounceFunction);
```
<!--more-->

在上面的示例中，debounce 函数接受两个参数：**要执行的事件处理函数**和**延迟时间**（规定的等待时间）。在事件被触发时，debounce 返回一个新的函数，该函数会设置一个定时器来等待延迟时间，然后执行事件处理函数。

防抖的实现可以根据需要进行适当的调整，例如增加立即执行选项，即在事件触发时立即执行一次事件处理函数，然后再进入防抖模式。这样可以在事件触发的瞬间即时响应，同时仍然保留了后续的防抖效果。

现在在上面防抖的基础上多增加一个参数，**immediate**， 参数值为true时，函数立即执行，为false时延时执行。

```JavaScript
function debounce(func, delay, immediate) {
  let timer;
  
  return function() {
    const context = this;
    const args = arguments;
    
    clearTimeout(timer);
    
    if (immediate) {    // immediate为true，立即执行
        if (!timer) {
            func.apply(context, args); // 立即执行一次事件处理函数
        }
        timer = setTimeout(function() {
            timer = null; // delay时间到后，将计时器设置null，下一次立即执行
        }, delay);
    } else {    //immediate为false，延时执行
        timer = setTimeout(function() {
            func.apply(context, args); // 在延迟时间后执行事件处理函数
        }, delay);
    }
  };
}

// 使用防抖处理事件，第一次触发立即执行，之后每次触发等待300毫秒后执行
const debouncedFunction = debounce(function() {
  console.log("Debounce function called.");
}, 500, true);

// 触发事件
window.addEventListener("resize", debouncedFunction);

```

总的来说，防抖是一种有效的优化手段，可以帮助减少频繁触发的事件造成的重复操作，提升前端应用的性能和用户体验。
