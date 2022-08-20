# c++ knowledge

## C++11 引入User-defined literals

在最近使用的两个github高赞库，**fmt**和**nlohmann/json**中，都看到了类似的用法。

- **fmt**中展示了`using namespace std::literals::chrono_literals`的用法，如使用`24h`或者`30min`后，可以转化成对应的时间长度，注意的是引入的c++版本是不同的：
```bash
d: C++20
h: C++14
min/ms: C++14
ns: C++14
s: C++14
us: C++14
y: C++14
```

- 用户把字符串转化成json对象，可以带上类似关键字`_json`，实现了`std::string`到`json`的转化，类似的写法如

```c++
nlohmann::json = R"(
    {
    "pi": 3.141,
    "happy": true
    }
)"_json;
```

## 一些库的使用

### fmt

开始使用**fmt**时，编译报`undefined reference to fmt::v8::vprint`错误，尝试调整了C++编译的版本，还是报错，后来找到了两种解决方法

* 编译的时候链接fmt，但系统里没有这个动态库
* 在整个函数的开头声明**#define FMT_HEADER_ONLY**，解决了问题。考虑到使用的日志库**spdlog**也用了**fmt**，编译的标准也是C++11，解决的方法应该不是很麻烦。果然**spdlog**中也是声明了仅使用头文件

### 利用**spdlog**写入日志文件

跑了本地demo是可以写入文件的，但是利用**xtest**测试时，就无法写入，也是想了好久，上网一查，还是太年轻。

**spdlog**是有一个`global register`的，用来管理日志对象，**spdlog**中每一个日志对象称为一个`sinks`，用来向目标写入日志。所以一个程序可以有多个`sinks`，向终端、文件等写入，每个`sinks`可以设置写入日志的格式和等级等。

每个日志对象是自动注册的，但是也可以设置默认的日志对象，这样所有的日志均写入同一个对象。

但对于**写入文件**注意的是，如果程序提前终止，默认无法写入文件的，因为此时的日志内容在**缓冲区**，所以需要`flush_on`，**spdlog**也可以支持定时刷新缓冲区

```c++
    // 绑定多个sinks的日志对象
    std::vector<spdlog::sink_ptr> sinks;
    sinks.push_back(std::make_shared<spdlog::sinks::stdout_sink_st>());
    sinks.push_back(std::make_shared<spdlog::sinks::daily_file_sink_st>("logfile", 23, 59));
    auto combined_logger = std::make_shared<spdlog::logger>("name", begin(sinks), end(sinks));
    //register it if you need to access it globally
    spdlog::register_logger(combined_logger);

    spdlog::set_default_logger(file_logger);
    spdlog::drop("logger_name");
    spdlog::drop_all();

    // 设置输出缓存内容的日志等级
    spdlog::flush_on(spdlog::level::info);
```
