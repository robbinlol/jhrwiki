# Python加载器插件

## 为了实现本地能够模拟线上调试，Python 可以安装`aiges`模块
```python
pip install aiges
pip install --upgrade aiges
```

## 实现一个Python加载器插件
- 快速开始一个Python 项目
```python
python -m aiges create "project"
```
- 目前Python加载器插件只支持非流式操作，最重要的实现逻辑是`wrapperOnceExec`函数

- 为了达到模拟线上访问请求的目的，本地需要额外声明`UserRequest`和`UserResponse`两个类
    * `UserRequest`类，模拟了用户访问请求参数中的`key = value`对
    * `UserResponse`类，模拟了用户响应中的`key = value`对