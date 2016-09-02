---
title: 一个学习笔记
date: 2016-06-23 10:03:58
tags:
---
这是关于这篇文章的[浏览器的工作原理：新式网络浏览器幕后揭秘](http://www.html5rocks.com/zh/tutorials/internals/howbrowserswork/)记录
上次写了一半没保存全没了。姐姐重新写，哼，（反正也是复制粘贴 大雾）

## 浏览器的高层结构
- 用户界面 - 包括地址栏、前进/后退按钮、书签菜单等。除了浏览器主窗口显示的您请求的页面外，其他显示的各个部分都属于用户界面。
- 浏览器引擎 - 在用户界面和呈现引擎之间传送指令。
- 呈现引擎 - 负责显示请求的内容。如果请求的内容是 HTML，它就负责解析 HTML 和 CSS 内容，并将解析后的内容显示在屏幕上。
- 网络 - 用于网络调用，比如 HTTP 请求。其接口与平台无关，并为所有平台提供底层实现。
- 用户界面后端 - 用于绘制基本的窗口小部件，比如组合框和窗口。其公开了与平台无关的通用接口，而在底层使用操作系统的用户界面方法。
- JavaScript 解释器。用于解析和执行 JavaScript 代码。
- 数据存储。这是持久层。浏览器需要在硬盘上保存各种数据，例如 Cookie。
<!--more-->
## 呈现引擎
默认情况下，呈现引擎可显示 HTML 和 XML 文档与图片。通过插件（或浏览器扩展程序），还可以显示其他类型的内容。

## 主流程
呈现引擎将开始解析 HTML 文档，并将各标记逐个转化成“内容树”上的 DOM 节点。同时也会解析外部 CSS 文件以及样式元素中的样式数据。HTML 中这些带有视觉指令的样式信息将用于创建另一个树结构：呈现树( render tree )。
呈现树构建完毕之后，进入“布局”处理阶段，也就是为每个节点分配一个应出现在屏幕上的确切坐标。下一个阶段是绘制 - 呈现引擎会遍历呈现树，由用户界面后端层将每个节点绘制出来。
![WebKit 主流程](http://www.html5rocks.com/zh/tutorials/internals/howbrowserswork/webkitflow.png)
![ Gecko 呈现引擎主流程](http://www.html5rocks.com/zh/tutorials/internals/howbrowserswork/image008.jpg)

### 解析
解析的过程可以分成两个子过程：词法分析和语法分析。
词法分析是将输入内容分割成大量标记的过程。标记是语言中的词汇，即构成内容的单位。在人类语言中，它相当于语言字典中的单词。
语法分析是应用语言的语法规则的过程。
很多时候，解析树还不是最终产品。解析通常是在翻译过程中使用的，而翻译是指将输入文档转换成另一种格式。编译就是这样一个例子。编译器可将源代码编译成机器代码，具体过程是首先将源代码解析成解析树，然后将解析树翻译成机器代码文档。

#### HTML
算法由两个阶段组成：标记化和树构建。
标记化是词法分析过程，将输入内容解析成多个标记。HTML 标记包括起始标记、结束标记、属性名称和属性值。标记生成器识别标记，传递给树构造器，然后接受下一个字符以识别下一个标记；如此反复直到输入的结束。
在创建解析器的同时，也会创建 Document 对象。在树构建阶段，以 Document 为根节点的 DOM 树也会不断进行修改，向其中添加各种元素。标记生成器发送的每个节点都会由树构建器进行处理。

#### CSS
解析器都会将 CSS 文件解析成 StyleSheet 对象，且每个对象都包含 CSS 规则。CSS 规则对象则包含选择器和声明对象，以及其他与 CSS 语法对应的对象。

#### 脚本
预解析 在执行脚本时，其他线程会解析文档的其余部分，找出并加载需要通过网络加载的其他资源。通过这种方式，资源可以在并行连接上加载，从而提高总体速度。

### 呈现树构建
由可视化元素按照其显示顺序而组成的树，也是文档的可视化表示。它的作用是让您按照正确的顺序绘制内容。
Firefox 将呈现树中的元素称为“框架”。WebKit 使用的术语是呈现器或呈现对象。 呈现器知道如何布局并将自身及其子元素绘制出来。
呈现器是和 DOM 元素相对应的，但并非一一对应。非可视化的 DOM 元素不会插入呈现树中。如果元素的 display 属性值为“none”，那么也不会显示在呈现树中（但是 visibility 属性值为“hidden”的元素仍会显示）。有一些 DOM 元素对应多个可视化对象。它们往往是具有复杂结构的元素，无法用单一的矩形来描述。
在 Firefox 中，系统会针对 DOM 更新注册展示层，作为侦听器。展示层将框架创建工作委托给 FrameConstructor，由该构造器解析样式并创建框架。
在 WebKit 中，解析样式和创建呈现器的过程称为“附加”。每个 DOM 节点都有一个“attach”方法。附加是同步进行的，将节点插入 DOM 树需要调用新的节点“attach”方法。
![呈现树构建流程](http://www.html5rocks.com/zh/tutorials/internals/howbrowserswork/image025.png)
#### 样式计算
构建呈现树时，需要计算每一个呈现对象的可视化属性。这是通过计算每个元素的样式属性来完成的。
为了简化样式计算，Firefox 还采用了另外两种树：规则树和样式上下文树（rule tree and style context tree）。样式上下文包含端值。要计算出这些值，应按照正确顺序应用所有的匹配规则，并将其从逻辑值转化为具体的值。规则树包含了所有已知规则匹配的路径。规则的存储是延迟进行的。规则树不会在开始的时候就为所有的节点进行计算，而是只有当某个节点样式需要进行计算时，才会向规则树添加计算的路径。
样式上下文可分割成多个结构。这些结构体包含了特定类别（如 border 或 color）的样式信息。结构中的属性都是继承的或非继承的。继承属性如果未由元素定义，则继承自其父代。非继承属性（也称为“重置”属性）如果未进行定义，则使用默认值。
在计算某个特定元素的样式上下文时，首先计算规则树中的对应路径，或者使用现有的路径。然后我们沿此路径应用规则，在新的样式上下文中填充结构。我们从路径中拥有最高优先级的底层节点（通常也是最特殊的选择器）开始，并向上遍历规则树，直到结构填充完毕。如果该规则节点对于此结构没有任何规范，那么寻找路径更上层的节点，找到后指定完整的规范并指向相关节点。
WebKit 节点会引用样式对象 (RenderStyle)。它们不是保存在类似样式上下文树这样的树结构中，只是由 DOM 节点指向此类对象的相关样式。这些对象在某些情况下可以由不同节点共享。

样式表解析完毕后，系统会根据选择器将 CSS 规则添加到某个哈希表中。这些哈希表的选择器各不相同，包括 ID、类名称、标记名称等，还有一种通用哈希表，适合不属于上述类别的规则。如果选择器是 ID，规则就会添加到 ID 表中；如果选择器是类，规则就会添加到类表中，依此类推。 
#### 样式表层叠顺序
根据 CSS2 规范，层叠的顺序为（优先级从低到高）：
- 浏览器声明
- 用户普通声明
- 作者普通声明
- 作者重要声明
- 用户重要声明

选择器的特异性（specificity优先级？）由 CSS2 规范定义如下：
- 如果声明来自于“style”属性，而不是带有选择器的规则，则记为 1，否则记为 0 (= a)
- 记为选择器中 ID 属性的个数 (= b)
- 记为选择器中其他属性和伪类的个数 (= c)
- 记为选择器中元素名称和伪元素的个数 (= d)
将四个数字按 a-b-c-d 这样连接起来（位于大数进制的数字系统中），构成特异性。

找到匹配的规则之后，应根据级联顺序将其排序。WebKit 对于较小的列表会使用冒泡排序，而对较大的列表则使用归并排序。

WebKit 使用一个标记来表示是否所有的顶级样式表（包括 @imports）均已加载完毕。如果在附加过程中尚未完全加载样式，则使用占位符，并在文档中进行标注，等样式表加载完毕后再重新计算。

### 布局
呈现器在创建完成并添加到呈现树时，并不包含位置和大小信息。计算这些值的过程称为布局或重排。
HTML 采用基于流的布局模型，这意味着大多数情况下只要一次遍历就能计算出几何信息。处于流中靠后位置元素通常不会影响靠前位置元素的几何特征，因此布局可以按从左至右、从上至下的顺序遍历文档。但是也有例外情况，比如 HTML 表格的计算就需要不止一次的遍历。
坐标系是相对于根框架而建立的，使用的是上坐标和左坐标。
布局是一个递归的过程。它从根呈现器（对应于 HTML 文档的 <html> 元素）开始，然后递归遍历部分或所有的框架层次结构，为每一个需要计算的呈现器计算几何信息。

为避免对所有细小更改都进行整体布局，浏览器采用了一种“dirty 位”系统。如果某个呈现器发生了更改，或者将自身及其子代标注为“dirty”，则需要进行布局。

全局布局是指触发了整个呈现树范围的布局，触发原因可能包括：
- 影响所有呈现器的全局样式更改，例如字体大小更改。
- 屏幕大小调整。
布局可以采用增量方式，也就是只对 dirty 呈现器进行布局（这样可能存在需要进行额外布局的弊端）。 
当呈现器为 dirty 时，会异步触发增量布局。Firefox 将增量布局的“reflow 命令”加入队列，而调度程序会触发这些命令的批量执行。WebKit 也有用于执行增量布局的计时器：对呈现树进行遍历，并对 dirty 呈现器进行布局。 
如果布局是由“大小调整”或呈现器的位置（而非大小）改变而触发的，那么可以从缓存中获取呈现器的大小，而无需重新计算。
#### 布局处理
布局通常具有以下模式：
1. 父呈现器确定自己的宽度。
2.父呈现器依次处理子呈现器，并且：放置子呈现器（设置 x,y 坐标）。如果有必要，调用子呈现器的布局（如果子呈现器是 dirty 的，或者这是全局布局，或出于其他某些原因），这会计算子呈现器的高度。
3. 父呈现器根据子呈现器的累加高度以及边距和补白的高度来设置自身高度，此值也可供父呈现器的父呈现器使用。
4. 将其 dirty 位设置为 false。
Firefox 使用“state”对象 (nsHTMLReflowState) 作为布局的参数（称为“reflow”），这其中包括了父呈现器的宽度。 
Firefox 布局的输出为“metrics”对象 (nsHTMLReflowMetrics)，其包含计算得出的呈现器高度。

呈现器宽度是根据容器块的宽度、呈现器样式中的“width”属性以及边距和边框计算得出的。

如果呈现器在布局过程中需要换行，会立即停止布局，并告知其父代需要换行。父代会创建额外的呈现器，并对其调用布局。
### 绘制
在绘制阶段，系统会遍历呈现树，并调用呈现器的“paint”方法，将呈现器的内容显示在屏幕上。绘制工作是使用用户界面基础组件完成的。
和布局一样，绘制也分为全局（绘制整个呈现树）和增量两种。在增量绘制中，部分呈现器发生了更改，但是不会影响整个树。
块呈现器的堆栈顺序如下：
1. 背景颜色
2. 背景图片
3. 边框
4. 子代
5. 轮廓

Firefox 遍历整个呈现树，为绘制的矩形建立一个显示列表。列表中按照正确的绘制顺序（先是呈现器的背景，然后是边框等等）包含了与矩形相关的呈现器。等到重新绘制的时候，只需遍历一次呈现树，而不用多次遍历。
在重新绘制之前，WebKit 会将原来的矩形另存为一张位图，然后只绘制新旧矩形之间的差异部分。 
### 呈现引擎的线程
呈现引擎采用了单线程。几乎所有操作（除了网络操作）都是在单线程中进行的。在 Firefox 和 Safari 中，该线程就是浏览器的主线程。而在 Chrome 浏览器中，该线程是标签进程的主线程。
浏览器的主线程是事件循环。它是一个无限循环，永远处于接受处理状态，并等待事件（如布局和绘制事件）发生，并进行处理。
### CSS2 可视化模型
（这里有点前端知识的感觉了）
根据 CSS2 规范，“画布”这一术语是指“用来呈现格式化结构的空间”，也就是供浏览器绘制内容的区域。画布的空间尺寸大小是无限的，但是浏览器会根据视口的尺寸选择一个初始宽度。
CSS 盒模型（原文框模型英文box model 平时自己说盒模型比较多，还是改成盒模型）描述的是针对文档树中的元素而生成，并根据可视化格式模型进行布局的矩形框。 
![盒模型](http://www.html5rocks.com/zh/tutorials/internals/howbrowserswork/image046.jpg)
图就是哪个经典的。。。
每个盒都有一个内容区域（例如文本、图片等），还有可选的周围补白、边框和边距区域。
#### 定位
有三种定位方案：
- 普通：根据对象在文档中的位置进行定位，也就是说对象在呈现树中的位置和它在 DOM 树中的位置相似，并根据其框类型和尺寸进行布局。
- 浮动：对象先按照普通流进行布局，然后尽可能地向左或向右移动。
- 绝对：对象在呈现树中的位置和它在 DOM 树中的位置不同。
定位方案是由“position”属性和“float”属性设置的。
如果值是 static 和 relative，就是普通流；如果值是 absolute 和 fixed，就是绝对定位。

相对定位：先按照普通方式定位，然后根据所需偏移量进行移动。
绝对定位和固定定位是准确定义的，与普通流无关。元素不参与普通流。尺寸是相对于容器而言的。在固定定位中，容器就是可视区域。

#### 层级
这是由 z-index CSS 属性指定的。它代表了盒的第三个维度，也就是沿“z 轴”方向的位置。
这些盒分散到多个堆栈（称为堆栈上下文）中。在每一个堆栈中，会首先绘制后面的元素，然后在顶部绘制前面的元素，以便更靠近用户。如果出现重叠，新绘制的元素就会覆盖之前的元素。 
堆栈是按照 z-index 属性进行排序的。具有“z-index”属性的框形成了本地堆栈。视口具有外部堆栈。
