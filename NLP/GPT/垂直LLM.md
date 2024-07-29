# 通用LLM缺点
- 成本高
- 垂直能力不行，数据老旧
- 性能不稳定(幻觉问题)
- 难以优化
	- 从通用信息中稳定的萃取出专用信息很难
	- 性能难以定性定量评估
	- 比较黑盒，难以调参
# 三种技术
## prompt engineering
	从用户方向优化，写出高质量prompt，参考结构化prompt等。
## RAG
外挂知识库，主要优化检索速度和准确度
- 优点
	- 数据本地化
	- 随时新增减少数据
	- 便宜
	
- 流程：数据提取——embedding（向量化）——创建索引——检索——自动排序（Rerank）——LLM归纳生成
![[3.png]]

## RAG技术细节
![[2.png]]
### 数据索引
#### 数据清洗
- 数据提取：pdf, word, markdown 等dataLoader。API调用。
- 数据处理：格式处理、删除无效数据、压缩、格式化。
- 元数据：文件名、时间、title、图片
#### 分块 Chunking
- 一般是固定256/512个token为一个分块。为了避免同一个句子被分在两个chunk的情况，要增加冗余。512的token只保存480，前后32token保存前后chunk的头尾信息。
- 分块方式
	- 根据标点符号分块
	- 根据意图分块。NLTK spaCy
	- 递归，根据size分割
- 分块因素
	- 根据原文本大小，比如微博会固定140字
	- 根据模型允许的token size
	- 根据需要的回答长度，最好回答长度和chunk长度差不多
	- 根据应用类型
#### 向量化
- BGE: 中文embedding模型
- M3E: 中文embedding模型
- 通义千文
- Text-embedding-ada-002
- transformer encoder
### 检索
#### 元数据过滤
根据时间、数据权限等先把数据过滤一遍，提升效率。eg. ** 部门5月数据
#### 图关系检索
建立chunk之间的关联
#### 检索技术
- 相似度检索：各种distance，log function
- 关键字检索：需要给chunk做关键字摘要
- sql检索
#### 重排序
- 符合prompt的chunk过多，需要一些策略对检索内容做重新排序。
#### 查询轮换
- 子查询：在不同场景中采用不同的查询策略
- HyDE：生成相似的，更标准的promp模板
### 生成
基本就是靠LLM。


**进阶RAG (agents)
https://baoyu.io/translations/rag/advanced-rag-techniques-an-illustrated-overview
## Fine-tuning微调
自己上传数据集，基于3.5等模型进行二次训练，优点
- Setting the style, tone, format, or other qualitative aspects
- Improving reliability at producing a desired output
- Correcting failures to follow complex prompts
- Handling many edge cases in specific ways
- Performing a new skill or task that’s hard to articulate in a prompt
### 一般不建议使用fine-tuning
- 通过优化prompt能得到更好的结果
- 成本较大，反馈不明显
- 哪怕fine-tuning之后还是需要写好的prompt
### 准备数据集
### 调整超参
