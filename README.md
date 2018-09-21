# 写在开头
这是一份面向互联网开发者的比普遍意义上全栈更全栈的知识图谱。

一切的一切始于[linshuang](https://github.com/linshuang)在梳理最近一份工作所涉及的知识点与技能时，发现自己脑袋里的知识过于碎片化、不够体系化，也考虑到进入这行业多年是时候进行总结、归档。所以他拾起了抛弃多年的github，决定做这么一份知识图谱。

奈何写着写着想法也越来越多，想添加到图谱的东西也越来越多、越来越深入，也知道一个人的知识毕竟有限，所以想着可以约一些朋友一起来修订这个图谱。

## 作者
- [翔宇](https://github.com/philsavor)
- [林爽](https://github.com/linshuang)

## 目录结构
以下是当前项目的一个目录片段
```
│  README.md
│
│  full-stack.xmind
│
├─language
│  └─java
│          java.xmind
│          JVM.xmind
│          java.jpg
│          JVM.jpg
│          java.jmd
│          JVM.jmd
│
└─web-develop
    ├─ha-hc
    │      ha-hc.xmind
    │      ha-hc.jpg
    │
    └─java-web
            java-web.xmind
            java-web.jpg
```

### full-stack.xmind
该文件包含了各种能想到的各种话题（在这里我们用话题表示一系列逻辑上相关的知识点），它是第一层的图谱，起了提纲挈领的作用。

### 目录分层
各个话题通过目录结构形成树状结构。一个目录就是一个话题，其下的子目录则是该话题下的子话题。例如web-develop是一个大而广的话题，工业上有各种实现方式；java-web作为它的一个子目录已经缩小到可由java语言实现web服务器的一些列相关话题。

当然事实上我们是可以再细分的，例如可以再细分出servlet、spring等等目录，这些都视具体情况而定。实际编写实践中可以先不做过深的划分，等到一个篇幅无法承载或者不适合承载当下庞大的话题时，再做拆分。

### 目录下文件
- md 针对具体话题的阐述文档
- xmind 绘制图谱的原始文件
- jpg 由xmind导出的图片，往往用来被md文件引用

