# UPRR Web App

![QQ截图20220302234200](./static/img/uprrlogo.png)

## 项目简介

- 本项目基于协同过滤、聚类分析等算法，利用数据日志进行用户画像分析，并对个体用户进行个性化的资源推荐，以此构建一个具有可视化功能的分析平台。
- 研究目标 为了充分利用在线学习平台的数据，本项目挖掘数据背后的资源特征、学习者特征，探究在线学习平台的资源利用情况，从而进行学习资源个性化推荐，以提高课程学习的质量。
- 本项目基于 Python Flask 框架构建了一个 Web 应用平台，包括用户课程推荐、用户/课程搜索以及用户画像生成三个主要模块。本项目构建的 Web 平台已成功完成服务器部署工作。

## 如何在本地安装运行

- 首先，您应该设置位于 configs.py 文件中的系统路径：

  - `PROJECT_PATH` 应设置为项目文件夹的绝对路径；

  - `DATA_PATH` 应设置为 `data` 文件夹中 `features_sample.csv` 文件的绝对路径；


- 例如：

```python
PROJECT_PATH = "E:\\RecommAlgorithm\\UprrWebApp"
DATA_PATH = "E:\\RecommAlgorithm\\UprrWebApp\\data\\features_sample.csv"
```

- 然后，您应该检查 `requirements.txt` 文件中的Python依赖库是否全部安装，您可以通过命令 `pip install -r requirements.txt` 来安装依赖库。

- 最后，使用命令 `python server.py` 启动程序。如果一切正常，您应该看到 `Running on http://127.0.0.1:5000/` 的提示，这表明 Web
  应用已成功部署在本地服务器的5000端口上，访问该网址即可进入平台。

  - 如果遇到依赖包缺失问题，您可以通过 `pip install` 命令来安装缺失的依赖包；

  - 如果遇到编码问题，您可以使用记事本等工具打开报错的文件（例如数据集），然后保存为 UTF-8 格式；

> ⚠ 注意：本项目仅支持Python3版本。

## 如何部署在远程服务器

- 首先，您应该创建程序父目录，例如`mkdir ~/myproject`  `cd ~/myproject`，然后您可通过 `git clone git@github.com:CAPCOMIN/UprrWebApp.git`
  将项目下载到您的服务器上。
- 与在本地运行一样，您需要设置位于 configs.py 文件中的系统路径；

> ⚠ 注意：在Windows系统中，由于转义字符的原因，右斜杠 `\` 应写为 `\\` ；而在 Linux 系统中不用这么做。

- 然后，您应该运行命令 `sudo apt update` (Ubuntu) 以更新软件列表。随后您应该检查 `requirements.txt`
  文件中的Python依赖库是否全部安装，您可以通过命令 `pip install -r requirements.txt` 来安装依赖库。

- **在运行程序前，您应该检查服务器的防火墙安全策略，是否放行5000端口，包括服务器提供商的安全组策略和远程服务器上的防火墙策略。**

- 在服务器环境下，您应该首先使用命令 `python server.py` 测试程序。如果一切正常，您应该看到 `Running on http://your-ip:5000/` 的提示，这表明 Web
  应用已成功部署在服务器的5000端口上，此时您可以尝试访问服务器的5000端口。

- 一切正常的话，我们建议您在运行界面按下 `control+C` 关闭服务器，然后执行以下步骤。因为在远程服务器上不使用生产环境的 WSGI 服务器是很不安全的。

  - 在本地创建`wsgi.py`文件，在文件中粘贴以下内容，其中第一行的`myproject`是之前flask测试程序的文件名：

   ```python
   from UprrWebApp import server
   if __name__ == "__main__":
       server.run()
   ```

  - 运行以下命令来测试WSGI接入点：`uwsgi --socket 0.0.0.0:5000 --protocol=http -w wsgi:app` ，访问`ip:5000` 即可进入Web应用。

- 当然，如果您使用了类似宝塔面板等管理面板，您可以直接使用Python项目管理器插件来自动部署，本处不再赘述。

## 推荐系统模型说明

- 该模型以日志文件的形式将学生的在线活动作为输入。首先，将csv文件转换为可用的特征矩阵；接下来，模型采用这些特征并将余弦相似度与其他用户进行比较，并找到相似的用户；最后，模型输出前 N 个推荐的有序列表。该模型使用 Flask
  框架构建的简单 API 的形式呈现；
- 在Home（推荐系统）页面，输入学生的ID号和推荐课程数，即可得到对于该学生的课程推荐和本次推荐的相关数据；
- 要想查看本次推荐的原始数据，请访问 `tmp` 文件夹；
- 推荐系统页面上还包含关于该模型的详细介绍。

## 用户画像模型说明

- 该模型将csv数据文件中的课程特性（features）采集下来，与相关学生进行匹配。然后以每位学生为单位将所有相关特性集合起来，使用`jieba.analyse.tfidf`函数进行分词处理，并生成每个学生的WordCloud，展示在
  User Portrait 前端页面上；
- 另外，User Portrait 页面还包含课程特性集合，以及学生特征频率列表；
- 需要了解更多信息可参考 [WordCloud 官方文档](https://amueller.github.io/word_cloud/) 。

## 搜索模块说明

搜索模块的加入是为了辅助上面两个模块，如果你想得到某学生的详细选课信息，或了解某个课程的所有学生信息，您可以使用该模块。这样，您在观察学生画像或分析推荐列表时可以更有参考性。

## 使用的数据集

- 部分原始数据来源于[MoocData在线数据平台](http://moocdata.cn/) ，该平台包含大量的在线课程用户数据。
- 本项目使用的数据经过重新整理，主要包括用户ID、课程ID以及学生在平台上的交互信息。其中；
- 数据集中的院校信息经过重新处理，使之包含了更多院校；
- 数据集中的用户ID从1开始升序排列；
- 本数据集为虚构，不代表院校或课程的真实情况。



## 文件夹和模块说明

- `cfaGenerator`  基于协同过滤算法的推荐系统的核心代码，包含生成推荐列表的主要模型；
- `data`  包含本项目所需要的数据集；
- `fonts`  包括前端界面所用到的部分字体；
- `PreProcessing` 包括数据处理所需要用到的函数，其中`getCourses.py`可将csv文件中的课程信息提取出来作为搜索模块的课程选项（存储于`coursesOption.txt`文件中）；
- `static`  包括前端界面所用到的css文件，用户画像文件以及背景图像；
- `tmp`  包括两个用于存储推荐系统相关数据的文本文件；

## 主要作者

本项目由[@youdi1012](https://github.com/youdi1012) 担任前端工程负责人，[@CAPCOMIN](https://github.com/CAPCOMIN) 担任后端工程负责人兼Web应用负责人，[@konn-submarine-bu](https://github.com/konn-submarine-bu)担任数据处理负责人兼项目负责人。
