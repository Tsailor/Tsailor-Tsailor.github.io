---
    title: 关于babel的那些事情
    date: 2022-01-13
    tags: 
        - tools
        - babel
---

最近学习到了 babel, 看到一些好文章，mark标记一下。

@babel/core

babel编译器。被拆分三个模块：@babel/parser、@babel/traverse、@babel/generator

@babel/parser: 接受源码，进行词法分析、语法分析，生成AST。
@babel/traverse：接受一个AST，并对其遍历，根据preset、plugin进行逻辑处理，进行替换、删除、添加节点。
@babel/generator：接受最终生成的AST，并将其转换为 代码字符串，，同时此过程也可以创建source map。

<!--more-->
[https://zhuanlan.zhihu.com/p/72995336](babel深入教程)
[https://www.zhihu.com/question/277409645](babel,babel-core的关系)
[https://www.jiangruitao.com/babel/babel-core/](babel教程)

