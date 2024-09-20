#Graphrag
##一、原理介绍
Basic RAG的处理流程为提出问题，进入向量数据库检索出含义类似的chunk，将chunk与问题整合形成prompt，最终给用户恢复。传统rag在具体问题中处理能力优秀，如询问某产品价格。但是在处理宏观问题上存在问题，如查询技术团队的成果或询问书籍中主人公的人物关系。即所需信息散落在多个chunks之中（connecting the dots），大模型需要理解全文，提取关键信息，所以效果不佳。

![图片1](./图片1.png)

这类问题有更高效的解决办法，即提前提取出关键信息，建立节点将其关联，构建知识图谱。提取信息构建知识图谱可以用llm构建，根据整理好的信息进行回复。这就是Graph RAG技术。比如“Who is Scrooge, and what are his main relationships?”，我们可以通过Scrooge的节点找到与他有重要关系的人，并再往下找的这些人的有关信息。这是Graph RAG另一大特征，即层次结构。将知识图谱进行聚类，把相应信息合并在一起，再整理出层次结构，即Community Detection算法。

![图片2](./图片2.png)
##二、实验分析
2.1 实验环境

2.1.1软硬件配置
1、硬件：macbook pro m2 内存8GB
2、服务器配置 RTX4090 内存24GB
3、软件pycharm、FileZilla
4、实验环境python3.10

2.1.2部署模型
1、构建知识图谱所用模型
a)llama3.1:8b
b)phi3:14b
c)phi3.5:3.8b
2、embeding模型
nomic-embed-text

2.2实验步骤
1、配置SSH
创建并开启服务器，注意python版本至少3.10以上
![图片3](./图片3.png)
输入相应主机名、端口号、用户名
![图片4](./图片4.png)
配置解释器，同步文件夹
![图片5](./图片5.png)
导入相应环境
2、部署ollama并下载大模型
Linux输入以下命令进行下载
`curl -fsSL https://ollama.com/install.sh | sh`

安装完成输入如下命令启动ollama服务
`ollama serve`

使用如下命令下载大模型llama3.1:8b、phi3:14b、phi3.5

`ollama run xxx:xb`

使用如下命令下载nomic-embed-text模型

`ollama pull nomic-embed-text`

通过ollama list命令查看已下载模型
![图片6](./图片6.png)
3、 安装graphrag所需环境
输入如下命令安装graphrag库

`pip install graphrag`

4、 创建input文件夹

`mkdir -p ./ragtest/input`

5、通过Filezilla导入预训练文本
![图片7](./图片7.png)
6、初始化
使用如下命令对项目进行初始化
python -m graphrag.index --init --root ./ragtest
根据需要对项目中的参数进行调整
7、构建知识图谱
使用如下命令启动训练
python -m graphrag.index --root ./ragtest
结果图如下
![图片9](./图片9.png)
![图片10](./图片10.png)
2.3实验结果

基于llama3.1

1、 A Chrirmas Carol
询问英文问题
![图片11](./图片11.png)
英文回答
![图片12](./图片12.png)
询问中文问题
![图片13](./图片13.png)
中文回答
![图片14](./图片14.png)
中文回答书名出现错误
问题中增加书名细节
![图片15](./图片15.png)
中文回答
![图片16](/./图片16.png)

2、Tree frog adhesion

英文问题1
![图片17](./图片17.png)
英文回答
![图片18](./图片18.png)
英文问题2
![图片19](./图片19.png)
英文回答
![图片20](./图片20.png)
中文问题
![图片21](./图片21.png)
中文回答
![图片22](./图片22.png)

小结：llama3.1在回答英文和中文问题人物特征上细节有所欠缺，回答中文问题上出现如书名的错误。

基于phi3:14b

1、 A Chrirmas Carol

询问英文问题
![图片23](./图片23.png)
英文回答
![图片24](./图片24.png)
询问中文问题
![图片25](./图片25.png)
中文回答
![图片26](./图片26.png)

2、Tree frog adhesion

英文问题1
![图片27](./图片27.png)
英文回答
![图片28](./图片28.png)
英文问题2
![图片29](./图片29.png)
英文回答
![图片30](./图片30.png)
中文问题
![图片31](./图片31.png)
中文回答
![图片32](./图片32.png)

基于phi3.5

1、 A Chrirmas Carol
询问英文问题
![图片33](./图片33.png)
英文回答
![图片34](./图片34.png)
询问中文问题
![图片35](./图片35.png)
中文回答
![图片36](/Users/lisideng/Desktop/project/图片36.png)

2、Tree frog adhesion

英文问题1
![图片37](./图片37.png)
英文回答
![图片38](./图片38.png)
英文问题2
![图片39](./图片39.png)
英文回答
![图片40](./图片40.png)
中文问题
![图片41](./图片41.png)
中文回答
![图片42](./图片42.png)

小结：phi3.5在英文回答中对人物特征的细节相较于llama3.1更丰富，中文回答上细节丰富度基本一致。
