本文主要介绍了如何快速上手 Mermaid 流程图,不用贴图上传也不用拖拉点拽绘制,基于源码实时渲染流程图,操作简单易上手,广泛被集成于主流编辑器,包括 markdown 写作环境.

通过本节内容你将学习到以下主要内容:

了解什么是流程图以及Mermaid流程图;掌握并能记住如何绘制Mermaid流程图;了解 Gitbook 写作环境的相关集成插件.

![img](https://pics1.baidu.com/feed/ae51f3deb48f8c54c6d903f697f48cf3e2fe7fc1.jpeg?token=4f82112ff243cfbe0224b0a09ed6141b)

**什么是Mermaid流程图**

**关键词**

\- 项目地址- 在线编辑- 官方文档千言万语不如一张图,使用图形展示事物处理流程的图形称之为**流程图**.

Mermaid是一个基于 Javascript 的图解和制图工具.它基于 markdown 语法来简化和加速生成流程图的过程,也不止于生成流程图.

**源码**

graph TD A[Christmas] -->|Get money| B(Go shopping) B --> C{Let me think} C -->|One| D[Laptop] C -->|Two| E[iPhone] C -->|Three| F[fa:fa-car Car]**效果**

![img](https://pics0.baidu.com/feed/fcfaaf51f3deb48fcb71687a5ec29b2f2cf5785a.jpeg?token=01950224bbf8d863cddb77ff2c5a1f1f)

mermaid-flow-chart-letmethink-mermaid.png

项目地址: **https://github.com/mermaid-js/mermaid**在线编辑: **https://mermaidjs.github.io/mermaid-live-editor/**官方文档: **https://mermaid-js.github.io/mermaid/#/flowchart****Mermaid流程图快速入门**

**布局方向**

**关键词**

\+ TB+ BT+ LR+ RL

![img](https://pics2.baidu.com/feed/58ee3d6d55fbb2fbeb375200e19781a24723dcec.jpeg?token=60210bb0949148c6150ec4cc6eee6bda)

mermaid-flow-chart-layout-simplemindmap.png

流程图布局方向,由四种基本方向组成,分别是英文单词: top(上), bottom(下),left(左)和 right(右).其中可选值: TB (从上到下),BT (从下到上),LR (从左往右)和 RL (从右往左)四种.

**核心**: 仅支持上下左右四个垂直方向,是英文单词首字母大写缩写.

TB从上到下: from **T**op to **B**ottom

**源码**

graph TB Start --> Stop**效果**

![img](https://pics2.baidu.com/feed/e1fe9925bc315c60afd3ef13236c6a154b5477e8.jpeg?token=bb96771918160dde445ffd92e65c8d07)

mermaid-flow-chart-layout-simplemindmap.png

BT从下到上: from **B**ottom to **T**op

**源码**

graph BT Start --> Stop**效果**

![img](https://pics4.baidu.com/feed/b7fd5266d0160924b97de97179da94fce7cd3467.jpeg?token=9ce690ae50a339cd02e58299f9051152)

mermaid-flow-chart-layout-simplemindmap.png

LR从左往右: from **L**eft to **R**ight

**源码**

graph LR Start --> Stop**效果**

![img](https://pics0.baidu.com/feed/35a85edf8db1cb13685927137289f74893584be5.jpeg?token=3e476d5c659afe2f16a47d389fe68332)

mermaid-flow-chart-LR-mermaid.png

RL从右往左: from **R**ight to **L**eft

**源码**

graph RL Start --> Stop**效果**

![img](https://pics2.baidu.com/feed/eaf81a4c510fd9f9e712ad5d95f0752c2934a4e5.jpeg?token=3026d8ebdeedc9c2ee02a689ef75b87c)

mermaid-flow-chart-RL-mermaid.png

形状

**关键词**

\- 节点形状 + [矩形] - [[暂不支持]] - [(圆柱)] - [{暂不支持}] - [/平行四边形/] - [\平行四边形\] - [/梯形\] - [\梯形/] + (圆角矩形) - ((圆形)) - ([体育场]) - ({暂不支持}) + {菱形} - {{六边形}} - {[暂不支持]} - {(暂不支持)} + >不对称矩形]

![img](https://pics2.baidu.com/feed/d058ccbf6c81800aa61128a006e892fc808b4788.jpeg?token=3260cd84b3cd00dd13df7d617fe2bf8b)

mermaid-flow-chart-shape-simplemindmap.png

流程图节点形状,默认支持矩形和圆两种基本形状,包括基本形状的简单变体,支持嵌套组合形式,其中 [] 表示矩形,() 表示圆弧,{} 表示尖角(窃以为 <> 更适合)等等.

**核心**: 最外层代表主形状,内层辅助修饰.

一次性节点

一次性节点,默认表现为矩形节点,其文本内容直接显示 id 的值,适合后续不会出现多次引用的情况.

id 建议直接写成有意义的文本描述而不是当成唯一标识.

**源码**

graph TD id**效果**

![img](https://pics4.baidu.com/feed/5366d0160924ab1895a659e39a2747cb79890b91.jpeg?token=2b27a03a1b06347304c5e1d49a855ad2)

mermaid-flow-chart-id-mermaid.png

可重复节点

可重复节点,指定节点形状,其文本内容不再是 id 的值而是 <node shape> 的值,适合后续出现多次引用相同节点的情况.

id 代表节点的唯一标识,当前节点的文本描述由 <node shape> 的值指定,建议 id 写成有意义的唯一标识.

矩形一般格式: [node description] ,[] 中括号表示节点是**矩形**形状,node description 是节点的描述文本.

**源码**

graph LR id1[This is the text in the box]**效果**

![img](https://pics5.baidu.com/feed/0824ab18972bd40717fd9626d5543f570eb30912.jpeg?token=1a6c9bff23b2cc163f75c9ba623bd67e)

mermaid-flow-chart-rectangular-mermaid.png

圆角矩形一般格式: (node description) ,() 小括号表示节点是**圆角矩形**形状,node description 是节点的描述文本.

**源码**

graph LR id1(This is the text in the box)**效果**

![img](https://pics0.baidu.com/feed/32fa828ba61ea8d39d70433738d79148241f5892.jpeg?token=ab4939162f18907ab9809456485abdcb)

mermaid-flow-chart-rounded-rectangular-mermaid.png

体育场一般格式: ([node description]) ,() 小括号嵌套 [] 中括号表示节点是大弧度的圆角矩形形状,也就是**体育场**形状,node description 是节点的描述文本.

**源码**

graph LR id1([This is the text in the box])**效果**

![img](https://pics3.baidu.com/feed/ae51f3deb48f8c5413b84bc18af48cf3e2fe7fdd.jpeg?token=203afe85bd507f142046dde1223e5f20)

mermaid-flow-chart-stadium-mermaid.png

圆柱一般格式: [(node description)] ,[] 中括号嵌套 () 小括号表示节点是**圆柱**形状,node description 是节点的描述文本.

**源码**

graph LR id1[(Database)]**效果**

![img](https://pics3.baidu.com/feed/3bf33a87e950352a9e04a552e29e5af4b3118b63.jpeg?token=1144f63c19825f6f6136abd0a3cb9e36)

mermaid-flow-chart-cylindrical-mermaid.png

圆形一般格式: ((node description)) ,() 小括号嵌套 () 小括号表示节点是**圆形**形状,node description 是节点的描述文本.

**源码**

graph LR id1((This is the text in the circle))**效果**

![img](https://pics1.baidu.com/feed/024f78f0f736afc37fab96c206c44ac2b54512d1.jpeg?token=5eb9bb3fbdf9e053df69ebed1a7b54f5)

mermaid-flow-chart-circle-mermaid.png

不对称矩形一般格式: >node description] ,左边是右尖括号 > ,右边是右中括号 ] 表示**不对称矩形**形状,node description 是节点的描述文本.

**源码**

graph LR id1>This is the text in the box]**效果**

![img](https://pics6.baidu.com/feed/a08b87d6277f9e2f8098111fa9ed4822b999f358.jpeg?token=e411d6d470f38c9fcd12a4a52cdce525)

mermaid-flow-chart-asymmetrical-rectangle-mermaid.png

菱形一般格式: {node description} ,{} 大括号表示**菱形**形状,node description 是节点的描述文本.

**源码**

graph LR id1{This is the text in the box}**效果**

![img](https://pics0.baidu.com/feed/a8014c086e061d95e2c9eb30d529abd763d9ca14.jpeg?token=4813a058c8b615c8122d6d132d6ea219)

mermaid-flow-chart-diamond-mermaid.png

六角形一般格式: {{node description}} ,{} 大括号嵌套 {} 大括号表示**六角形**形状,node description 是节点的描述文本.

**源码**

graph LR id1\{\{This is the text in the box\}\}Gitbook 语法中双大括号 {} 表示特殊意义,上述源码只能转义处理,实际上并不需要 \ 进行转义.

**效果**

![img](https://pics2.baidu.com/feed/e1fe9925bc315c609d01fcc5226c6a154854776c.jpeg?token=9c00d8b89953c549200e644ff401bb3c)

mermaid-flow-chart-hexagonal-mermaid.png

平行四边形一般格式: [/node description/] ,[] 中括号嵌套 // 左斜杠表示**左斜平行四边形**形状,node description 是节点的描述文本.

**源码**

graph TD id1[/This is the text in the box/]**效果**

![img](https://pics3.baidu.com/feed/b3119313b07eca807871f7d23ffe36dba04483f7.jpeg?token=bee9a83eb671a4eadaf0578ef8f94c2f)

mermaid-flow-chart-left-oblique-parallelogram-mermaid.png

平行四边形一般格式: [\node description\] ,[] 中括号嵌套 \\ 右斜杠表示**右斜平行四边形**形状,node description 是节点的描述文本.

**源码**

graph TD id1[\This is the text in the box\]**效果**

![img](https://pics7.baidu.com/feed/4bed2e738bd4b31c695776672b0b86799c2ff8ec.jpeg?token=5efa7ec1a3d297b0a8354e11399a08f2)

mermaid-flow-chart-right-oblique-parallelogram-mermaid.png

梯形一般格式: [/node description\] ,[] 中括号嵌套 /\ 左右斜杠表示**上短下长梯形**形状,node description 是节点的描述文本.

**源码**

graph TD A[/Christmas\]**效果**

![img](https://pics5.baidu.com/feed/3bf33a87e950352a04b45eccfd9e5af4b0118b05.jpeg?token=28779fb16b88fe1b1fff4f669c0e5aa7)

mermaid-flow-chart-top-trapezoidal-mermaid.png

另一种梯形一般格式: [\node description/] ,[] 中括号嵌套 \/ 右左斜杠表示**上长下短梯形**形状,node description 是节点的描述文本.

**源码**

graph TD B[\Go shopping/]**效果**

![img](https://pics0.baidu.com/feed/37d3d539b6003af3e2dddf2898f7675a1138b673.jpeg?token=9467a18a2586336788f8362d30802a22)

mermaid-flow-chart-bottom-trapezoidal-mermaid.png

**连接线**

**关键词**

\+ 实线/虚线 - -- - -.+ 有箭头/无箭头 - > - -+ 有描述/无描述 - 实线 + --描述文字 + |描述文字| - 虚线 + -.描述文字 + |描述文字|+ 加粗 - ==+ 组合形式 - --> - --- - -.-> - -.- - 有描述实线有箭头 + --描述文字--> + -->|描述文字| - 有描述实线无箭头 + --描述文字--- + ---|描述文字| - 有描述虚线有箭头 + -.描述文字-.-> + -.->|描述文字| - 有描述虚线无箭头 + -.描述文字-.- + -.-|描述文字| - ==> - === - 有描述加粗实线有箭头(2) + ==描述文字==> + ==>|描述文字| - 有描述加粗实线无箭头(2) + ==描述文字=== + ===|描述文字|

![img](https://pics5.baidu.com/feed/b3b7d0a20cf431ad9084b131e4eb0da92fdd984c.jpeg?token=c558e1bad52e840981fb30fe21e3da75)

mermaid-flow-line-simplemindmap.png

流程图连接线样式,支持实线和虚线以及有箭头样式和无箭头样式,除此之外还支持添加连接线描述文字,其中 -- 代表实线,实线中间多一点 -.- 代表虚线,添加箭头用右尖括号 > ,没有箭头继续用短横线 -.

**核心**: 先实线再虚线,先有箭头再去箭头,左边位置添加描述文字需要区分实现还是虚线,右边位置添加描述文字格式一致.

有箭头无描述实线一般格式: --> ,其中 -- 表示实线,> 表示有箭头.

**源码**

graph LR A-->B**效果**

![img](https://pics7.baidu.com/feed/ae51f3deb48f8c549576b20995f48cf3e1fe7f56.jpeg?token=9608550c4f608d6f08f804b556b02897)

mermaid-flow-chart-solid-line-arrow-mermaid.png

无箭头实线一般格式: --- ,其中 -- 表示实线,- 表示无箭头.

**源码**

graph LR A --- B**效果**

![img](https://pics4.baidu.com/feed/00e93901213fb80e968c158c990c8e28b8389474.jpeg?token=625ce56a90e187ea4f3ce526c7954727)

mermaid-flow-chart-solid-line-arrow-mermaid.png

带描述的有箭头实线一般格式: --connection line description--> ,其中左边的 -- 添加到**实线左边位置**,右边的 --> 表示**带箭头的实线**.

**源码**

graph LR A-- text -->B**效果**

![img](https://pics0.baidu.com/feed/b64543a98226cffc463ed00e14dceb96f703ea5e.jpeg?token=58efe3d39525a63cde3d27147057d20a)

mermaid-flow-chart-solid-line-arrow-left-desc-mermaid.png

一般格式: |connection line description| ,其中 || 添加到**连接线右边位置**.

**源码**

graph LR A-->|text|B**效果**

![img](https://pics6.baidu.com/feed/dcc451da81cb39db1cdd8eb263cba822aa18301d.jpeg?token=3abc2a27b6bd195cb5dfabebf50e6095)

mermaid-flow-chart-solid-line-arrow-right-desc-mermaid.png

带描述的无箭头实线一般格式: --connection line description ,其中左边的 -- 添加到**实线左边位置**,右边的 --- 表示**不带箭头的实线**.

**源码**

graph LR A-- This is the text ---B**效果**

![img](https://pics1.baidu.com/feed/8d5494eef01f3a2989a343b237f81d375d607c43.jpeg?token=e718da74bb8578deb26ca93ebfad1e6a)

mermaid-flow-chart-solid-line-noarrow-left-desc-mermaid.png

一般格式: |connection line description| ,其中 || 添加到**连接线右边位置**.

**源码**

graph LR A---|This is the text|B**效果**

![img](https://pics2.baidu.com/feed/f9dcd100baa1cd11217165c716cf69fac1ce2d71.jpeg?token=92df3c4897a5a491b403509aedae22b8)

mermaid-flow-chart-solid-line-noarrow-right-desc-mermaid.png

有箭头虚线一般格式: -.connection line description.-> ,其中左边的 -. 添加到**虚线左边位置**,右边的 .-> 表示**带箭头的虚线**.

**源码**

graph LR A-. text .-> B**效果**

![img](https://pics2.baidu.com/feed/810a19d8bc3eb135913c34e713c309d5ff1f44d3.jpeg?token=71f30f45263b171b488ee6b9636d2191)

mermaid-flow-chart-dotted-line-arrow-left-desc-mermaid.png

有箭头加粗实线一般格式: ==> ,表示加粗实线.

**源码**

graph LR A ==> B**效果**

![img](https://pics0.baidu.com/feed/cefc1e178a82b901f3e20d34dd5008713812ef4b.jpeg?token=d0ed23f575ddaffe16eeb8eeaf596038)

mermaid-flow-chart-bold-solid-line-arrow-mermaid.png

带描述的有箭头加粗实线一般格式: ==connection line description ,左边的 == 添加到加粗实现左边,右边的 ==> 代表加粗实线.

**源码**

graph LR A == text ==> B**效果**

![img](https://pics0.baidu.com/feed/9345d688d43f87940ba1a9f87ec6aff219d53aea.jpeg?token=6d646868234723417c38ab320d04a10e)

mermaid-flow-chart-bold-solid-line-arrow-left-desc-mermaid.png

带描述的有箭头加粗实线一般格式: |connection line description| ,其中 || 添加到**连接线右边位置**.

**源码**

graph LR A ==>|text| B**效果**

![img](https://pics3.baidu.com/feed/4afbfbedab64034f4f3486fc031ed83708551db6.jpeg?token=4bc342786900c99b24d00ff253dc86ac)

mermaid-flow-chart-bold-solid-line-arrow-right-desc-mermaid.png

**高级用法**

**关键词**

\+ -->-->+ &+ ""+ %%+ subgraph

![img](https://pics6.baidu.com/feed/9922720e0cf3d7ca6182df2f43c21f0f6a63a953.jpeg?token=fa01234ac87d905d406912f4e656592c)

mermaid-flow-chart-advance-simplemindmap.png

多节点链式连接**源码**

支持链式连接方式,A-->B-->C 等价于 A-->B 和 B-->C 形式.

graph LR A -- text --> B -- text2 --> C**效果**

![img](https://pics1.baidu.com/feed/caef76094b36acaf24c2be87d2042c1600e99c8a.jpeg?token=4e99d773098c7e2444bfa36ec4bae204)

mermaid-flow-chart-chain-link-mermaid.png

多节点共同连接支持共同连接方式,A-->B & C 等价于 A-->B 和 A-->C 形式.

**源码**

graph LR a --> b & c--> d**效果**

![img](https://pics4.baidu.com/feed/ca1349540923dd54fa8f3bf87dd412d89e824882.jpeg?token=964d1b15eadf62d4bdb0ed7963a79e18)

mermaid-flow-chart-common-link-mermaid.png

多节点相互连接多节点共同连接的变体形式,A & B --> C & D 等价于 A-->C ,A-->D,B-->C 和 B-->D 四种组合形式.

**源码**

graph TB A & B--> C & D**效果**

![img](https://pics5.baidu.com/feed/4afbfbedab64034fd4100dbf011ed83708551d8a.jpeg?token=0c62991c850bf4c476018535340e924c)

mermaid-flow-chart-eachother-link-mermaid.png

双引号包裹特殊字符连接线描述文字存在特殊字符使用双引号 "" 包裹处理,如遇到 [] 和 () 以及 {} 等特殊字符情况.

**源码**

graph LR id1["This is the (text) in the box"]**效果**

![img](https://pics3.baidu.com/feed/2e2eb9389b504fc2843367645400461791ef6d43.jpeg?token=cdbaf1dd2e98eda8f47cf72d237c6dd4)

mermaid-flow-chart-special-character-mermaid.png

双引号包裹转义字符支持 Html 转移字符

**源码**

graph LR A["A double quote:#quot;"] -->B["A dec char:#9829;"]**效果**

![img](https://pics3.baidu.com/feed/b21c8701a18b87d658cad6b7aad5893e1d30fdf6.jpeg?token=3bb35134480f76468e5306aa7a95ddc7)

mermaid-flow-chart-escape-character-mermaid.png

嵌套子流程图**定义**

subgraph title graph definitionend**示例**

graph TB c1-->a2 subgraph one a1-->a2 end subgraph two b1-->b2 end subgraph three c1-->c2 end

![img](https://pics5.baidu.com/feed/c2cec3fdfc0392452a99547a374905c47c1e25ff.jpeg?token=f79a796829d46a8ef9df5432b433d561)

mermaid-flow-chart-subgraph-mermaid.png

注释语法注释以 %% 开头并且独占一行.

graph LR%% this is a comment A -- text --> B{node} A -- text --> B -- text2 --> C

![img](https://pics1.baidu.com/feed/838ba61ea8d3fd1f20c995519f93841997ca5ff8.jpeg?token=1b9784c730bacae44c93cf2934313274)

mermaid-flow-chart-comment-mermaid.png

**快速入门流程图回顾总结**

**关键词**

\- 英文单词缩写- 几何化形状- 有限语法

![img](https://pics4.baidu.com/feed/f31fbe096b63f624788aed0235994afe184ca3c4.jpeg?token=59b66ca9e5eff35fd48d15fe14dbf2b0)

mermaid-flow-chart-summary-simplemindmap.png

Mermaid 是一款开源的制图工具,可使用 Markdown 语法绘制流程图,支持更改流程图节点形状,添加描述文字以及更改连接线样式等等.

**英文单词缩写**

四种布局方向的值是英文单词首字母大写缩写形式,默认仅支持垂直方向.

![img](https://pics6.baidu.com/feed/8d5494eef01f3a29125ee6c729f81d375d607c0c.jpeg?token=057c66f86673785c1c40573afa10af86)

**几何化形状**

键盘符号形象化几何形状,组合形式表示形状的叠加,其中最外层符号是主形状,嵌套符号是辅助形状.

基本单元

![img](https://pics4.baidu.com/feed/8644ebf81a4c510f8dc07d89ce84842bd52aa505.jpeg?token=90d25251b69d2ce732b57819fc7fc7d0)

组合单元

![img](https://pics1.baidu.com/feed/a08b87d6277f9e2f69e27da2aced4822ba99f3ad.jpeg?token=d3dcc669421b16eebedec1a7d1230885)

**有限语法**

不论是节点形状还是连接线样式,语法支持是有限的,并不是随意组合的叠加状态,也可能随着后续更新会支持更多,一切以官方文档为主.

除了提供最基础的操作节点的能力之外,还可以根据 JS 和 CSS 相关知识高度自定义流程图行为表现,具体可参考官方文档.

官方文档: **https://mermaid-js.github.io/mermaid/#/flowchart?id=styling-and-classes**

交互能力 Interaction : **https://mermaid-js.github.io/mermaid/#/flowchart?id=interaction**外观样式 Styling and classes : **https://mermaid-js.github.io/mermaid/#/flowchart?id=interaction**字体支持 Basic support for fontawesome: **https://mermaid-js.github.io/mermaid/#/flowchart?id=basic-support-for-fontawesome**空格分隔 **https://mermaid-js.github.io/mermaid/#/flowchart?id=graph-declarations-with-spaces-between-vertices-and-link-and-without-semicolon**







