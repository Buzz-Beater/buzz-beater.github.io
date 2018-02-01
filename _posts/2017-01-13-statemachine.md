---
layout: post
title: 基于GO的状态机
tags: [statemachine]
comments: true
---

## Amazon 状态语言

> https://states-language.net/spec.html

### Hello World

状态机测行为由状态来控制，在这里使用JSON object来表示。下面我们来看一个例子
~~~java
{
    "Comment": "A simple minimal example of the States language",
    "StartAt": "Hello World",
    "States": {
    "Hello World": { 
      "Type": "Task",
      "Resource": "arn:aws:lambda:us-east-1:123456789012:function:HelloWorld",
      "End": true
    }
  }
}
~~~
顶这个例子中，我们可以看到，顶层的状态对象是"States"。同时还有终止状态End。其他的终止状态还包括
Succeed，Fail以及End。如果这个state的状态不是终止状态，那它会去寻找下一个next的field。并且这是一个
Task的state，所以状态机会去尝试执行它（这个例子中的task resource是一个lambda语言）。

### 重要关键字

States: 状态集合，顶层field。

StartAt: 状态机开始的那个状态.

