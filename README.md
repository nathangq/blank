相似图像检索
====
思路流程
----
提取图片全局特征 + 建索引检索<br>

提取图片全局特征
----

用来描述图片的cnn特征，我们采用 vgg-19 pre-train imagenet 的 fc6 层数据，该层数据4096维，用mxnet1.0.0作为框架提取。<br>
模型地址 http://data.dmlc.ml/mxnet/models/imagenet/vgg/ <br>
提取模型的代码在temp_faiss_index_query.py中 <br>

建索引检索
----

用faiss来建库+索引，faiss是高维向量检索工具，支持无损／有损编码，cpu／gpu，多种编码方式， <br>
可参考https://cf.qiniu.io/pages/viewpage.action?pageId=62048443 <br>

主要脚本
----
