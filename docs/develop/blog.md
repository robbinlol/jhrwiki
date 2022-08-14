利用Github配合Mkdocs搭建个人博客的建议来自[OneFlow的一篇公众号](https://mp.weixin.qq.com/s/4ENpHo8ROx1ph57JLmvFJQ)，之前苦于前端知识的匮乏以及租服务器的麻烦，这件事一直没有施行，通过这个方法很轻松的搭建起了个人博客。

博客的搭建方法主要参考了

* 公众号中提及的[钟珊珊的博客](https://zhongshsh.github.io/BlogInOF)
* [Material for MkDocs的官方文档](https://squidfunk.github.io/mkdocs-material/getting-started/)
* [Mkdocs官方文档](https://www.mkdocs.org/getting-started/)

## 本地搭建
安装Mkdocs
```python
pip install mkdocs
```
新建Github项目，此处需要注意的是网上教程在新建显目时设立的项目名为**username.github.io**，之后可以通过该连接直接访问。而通过Mkdocs这种方法，新建的项目名即为正常项目名，将来访问时将通过**username.github.io/项目名**来访问。

1. 本地新建同github项目名的Mkdocs项目，例如`mkdocs new my_blog`，将其中的文件夹拷贝进clone下来的github项目中

2. 按照官方文档进行文件层级的划分，在本地调试达到满意效果后即可上传部署

3. 新建`gh-pages`分支

    ```bash
    git checkout -b gh-pages
    ```

    此时应处于gh-pages分支中，使用如下指令编译项目

    ```bash
    mkdocs build
    ```

4. 删除site之外的文件，并将site内的内容拷贝到当前目录

    ```bash
    cp -ra site/*
    ```

5. git push到相应的分支

## 自动部署
github Actions是一种自动化操作，博客每次更新要手动编译并push比较麻烦，下面的脚本实现了自动部署。

在项目的Actions中新建一个workflow，并将如下命令复制其中
```yml
# This is a basic workflow to help you get started with Actions

name: public site

# Controls when the workflow will run
on:
  # Triggers the workflow on push or pull request events but only for the main branch
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:
  # This workflow contains a single job called "build"
  build:
    # The type of runner that the job will run on
    runs-on: ubuntu-latest

    # Steps represent a sequence of tasks that will be executed as part of the job
    steps:
      # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
      - uses: actions/checkout@v3

      - name: Install mkdocs
        run: |
          python -m pip install mkdocs==1.2.2
          python -m pip install mkdocs-material==7.1.11
          python -m pip install mkdocs-material-extensions==1.0.1
          python -m pip install mike==1.0.1

      - name: Create html
        run: mkdocs build

      - name: Public
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./site
```