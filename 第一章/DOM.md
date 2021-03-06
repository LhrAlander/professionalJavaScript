# DOM
> 文档对象模型 针对 XML 但经过扩展 用于 HTML 的应用程序编程接口（API）<br>
DOM 将页面映射为多层节点结构，并通过DOM提供的API可以增删改查任何节点

## DOM 级别
> 分 1、2、3级

1. DOM1级
> 主要目标是映射文档的结构，是一种标准。由两个模块组成：<br>
> <ul>
>  <li>DOM核心：规定了如何映射基于XML的文档结构
>  <li>DOM HTML: 在DOM核心的基础上加以扩展，添加了针对HTML的对象和方法

2. DOM2级
>  在DOM1级的基础上扩充了鼠标和用户界面事件、范围、遍历等新模块，而且增加了CSS的支持，添加了以下新接口：<br>
> <ul>
>  <li>DOM视图：定义了跟踪不同文档（例如应用CSS之前和CSS之后的文档）视图的接口
>  <li>DOM事件：定义了事件和事件处理的接口
> <li>DOM样式：定义了基于CSS为元素应用样式的接口
> <li>DOM遍历和范围：定义了遍历和操作文档树的接口

3. DOM3级
> DOM3级引入了以同一方式加载和保存文档的方法——在DOM加载和保存模块中定义；新增了验证文档的方法——在DOM验证模块中定义

# 