相似图像检索
====
思路流程
----
提取图片全局特征 + 建索引检索<br>

#### 提取图片全局特征

用来描述图片的cnn特征，我们采用 vgg-19 pre-train imagenet 的 fc6 层数据，该层数据4096维，用mxnet1.0.0作为框架提取。<br>
模型地址 http://data.dmlc.ml/mxnet/models/imagenet/vgg/ <br>
提取模型的代码在temp_faiss_index_query.py中 <br>

#### 建索引检索

用faiss来建库+索引，faiss是高维向量检索工具，支持无损／有损编码，cpu／gpu，多种编码方式， <br>
可参考 https://cf.qiniu.io/pages/viewpage.action?pageId=62048443 <br>

主要脚本
----

***similar.py*** | 主脚本，调用其他脚本，注意在里面设置路径，以及一些参数 
-------- | :-----------: 
***temp_extraction.py*** | mxnet提取特征的脚本，注意指定层数的名称设置在similar.py中,它会生成底库(N)和检索库(Q)图片的cnn特征，存在 .npy中  
**faiss** 目录下 | 
***temp_faiss_index_query.py*** | faiss读取全局特征，并将其加入index中，通过对比距离得到 I,D 两个矩阵，I(Q*k)是返回 top k 的索引结果， D(Q*k)是返回对应的距离，注意I返回的是 按照加入顺序（0,1,2,3,...）顺序的索引，需要在list中找到image的path。存下I,D成为 .npz。
***temp_pull.py*** | 这个是从.npz中读取I, 这样从图片的list中的第i行反找出图片，复制到特定目录下，打包上传到bucket中。因为在k8s下无法看图片，所以我们需要这个脚本上传到存储云中。<br>

如何跑起来
----

**安装**：mxnet1.0.0；faiss编译，按照faiss网页上安装说明来操作不难，注意在makefies.inc里面注明，有安装openblas的命令，以及要注明python cuda的路径.<br>
<br>
**数据集准备**：分别下载（底库／检索库）到特定目录，然后用 ls -1 | grep ".*$" > xxx.txt 获得图像的路径名称(这个需要在similar.py中关联路径）
**改写similar.py**：主要是路径，用的模型名称和取的层数，如果不用k8s，那么temp_pull.py中要改写不用最后的上传步骤。




