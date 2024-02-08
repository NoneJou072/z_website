---
title: 打包自己的 Python 包并上传 PyPi
description: 介绍如何打包自己的 python 包，并上传到 PyPi 上去。
toc: true
authors:
  - Haoran Zhou
tags:
categories:
series:
date: '2023-09-07T13:11:22+08:00'
lastmod: '2023-09-07T13:11:22+08:00'
featuredImage:
draft: false
---

## 1. 注册 PyPi 账户并验证
1.为了上传到 PyPi，我们需要在 [pypi](https://pypi.org/) 网站中注册一个账户，注册完会给你的邮箱发送邮件，要点开邮件中的链接来验证身份。  

之后，会跳转到二次身份验证页面(2FA)。根据指引，我们生成了几行 Account recovery codes，记得先保存下来，再点击继续，把刚才保存的安全码中的一行输入进去，即可验证成功。  

接着，我们开始进行账户的二次验证。pypi 提供了两个途径，分别是 Add 2FA with authentication application 和 Add 2FA with security device。

我们选择 with authentication application 的方式。去谷歌商店中下载 Microsoft Authenticator，打开 app 后，选择底部的`已验证ID-扫描QR码`，扫描 Pypi 网页中出现的 QR 码。之后切换到 Authenticator 界面，点开 PyPi，会出现一次性密码代码，将该代码输入到 PyPi 网页中 QR 码右侧的输入栏，然后点击验证即可。

## 2. 调整本地项目的文件结构
要打包的项目应该符合如下文件结构：
```
/packaging_tutorial
  /example_pkg
    __init__.py
  setup.py
  LICENSE
  README.md
```
其中，
* `setup.py` 是setuptools的构建脚本。它告诉setuptools你的包（例如名称和版本）以及要包含的代码文件。
* `LICENSE` 文件中包含着我们的许可证，用于告诉用户安装你的软件包可以使用您的软件包的条款。有关选择许可证的帮助，请访问 [https://choosealicense.com/](https://choosealicense.com/)。选择许可证后，可以将其内容拷贝到 LICENSE 文本文件中。
* `README.md` 可以放入一些对项目的介绍。

## 3. 配置 setup.py
`setup.py`是 setuptools 的构建脚本。它告诉 setuptools 你的包（例如名称和版本）以及要包含的代码文件。下面是一个`setup.py`内容示例：
``` python
import setuptools

with open("README.md", "r") as fh:
  long_description = fh.read()

setuptools.setup(
  name="example-pkg-your-username",
  version="0.0.1",
  author="Example Author",
  author_email="author@example.com",
  description="A small example package",
  long_description=long_description,
  long_description_content_type="text/markdown",
  url="https://github.com/pypa/sampleproject",
  packages=setuptools.find_packages(),
  classifiers=[
  "Programming Language :: Python :: 3",
  "License :: OSI Approved :: MIT License",
  "Operating System :: OS Independent",
  ],
)
```
对于`setup()`中参数的介绍如下：

* `name` - 包的分发名称。只要包含字母，数字_和，就可以是任何名称-。它也不能在pypi.org上使用。请务必使用您的用户名更新此内容，因为这可确保您在上传程序包时不会遇到任何名称冲突。
* `version` - 包的版本。
* `author` - 包的作者。
* `author_email` 包的作者邮箱。
* `description` - 包的一个简短的总结。
* `long_description` - 包的详细说明。这显示在Python Package Index的包详细信息包中。在这种情况下，加载长描述README.md是一种常见模式。
* `long_description_content_type` - 告诉索引什么类型的标记用于长描述。在这种情况下，它是Markdown。
* `url` - 是项目主页的URL。在许多项目中是一个指向GitHub，GitLab，Bitbucket或类似代码托管服务的链接。
* `packages` - 应包含在分发包中的所有导入包的列表。我们可以使用 `find_packages()` 自动发现所有包和子包。在示例中，包列表为 `example_pkg`，因为它是唯一存在的包。
* `classifiers` - 关于包的其他元数据。在示例中，该软件包仅与 Python 3 兼容，根据MIT许可证进行许可，且与操作系统无关。有关分类器的完整列表，参阅 https://pypi.org/classifiers/。

除了这里提到的还有很多。有关详细信息可以参阅 [打包和分发项目](https://packaging.python.org/en/latest/guides/distributing-packages-using-setuptools/)。

## 4. 使用 MANIFEST.in 打包静态资源
在项目目录下新建 `MANIFEST.in` 文件，里面的内容参考如下示例：
```
include *.txt  #包含根目录下的所有txt文件
recursive-include examples *.txt *.py  #包含所有位置的examples文件夹下的txt与py文件
prune examples/sample?/build  #排除所有位置examples/sample?/build

global-include *  # 包含安装包下的所有文件
```

## 5. 生成分发档案
下一步是为包生成分发包。这些是上传到包索引的档案，可以通过pip安装。

首先确保安装了最新版本的 setuptools 和 wheel ：
```
python3 -m pip install --user --upgrade setuptools wheel
```

现在从与`setup.py`位于的同一目录运行此命令：
```
python3 setup.py sdist bdist_wheel
```

完成后，会在生成 dist 文件夹，并包含两个文件：
```
dist/
  example_pkg_your_username-0.0.1-py3-none-any.whl
  example_pkg_your_username-0.0.1.tar.gz
```

## 6. 上传 PYPI

接下来我们开始将项目包上传到 PYPI，首先进入 PyPi 账户设置中的 Create API token 界面，根据指引创建新的 API token，记得将生成的 token 复制下来。然后继续按照教程配置 token，具体地：  
新建或修改`$HOME/.pypirc`文件
(windows 中在 `users`` 目录下新建 `.pypirc`)
文件内容的模板如下：
```
[pypi]
  username = __token__
  password = pypi-AgEIcHlwaS5vcmcCJGYzYjQzM2NjLWFiYTMtNGM1Zi05ZWU1LTQ2MTUzOGUyNzA3NwACKlszLCJmMzkyMGY4YS05ZTVkLTQyMmEtOGE2MS1lM2IyYjJhZTNlMjgiXQAABiDQYlxiUUsdywZ2RtK__n0x0lgobwbai2ocPdFTRVG_ig
```

回到项目命令行，输入
```
sudo apt install twine
twine upload dist/*
```

输入 PyPI 的用户名和密码。命令完成后，应该看到与此类似的输出：
```
Uploading distributions to https://test.pypi.org/legacy/
Enter your username: [your username]
Enter your password:
Uploading example_pkg_your_username-0.0.1-py3-none-any.whl
100%|█████████████████████| 4.65k/4.65k [00:01<00:00, 2.88kB/s]
Uploading example_pkg_your_username-0.0.1.tar.gz
100%|█████████████████████| 4.25k/4.25k [00:01<00:00, 3.05kB/s]
```

现在，我们的包已经上传到 PyPi 上了，可以进入到 projects 界面查看我们的项目。