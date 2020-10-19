# 乐心运动刷步数

**开源项目仅供学习交流，由本项目衍生出的商业项目或用于商业用途与开发者[biuaxia](https://github.com/biuaxia)无关，望知悉**

> 当前版本号(Current version): `1.0.3-RELEASE`

CodeRepository: 

- <https://github.com/biuaxia/lexin>
- <https://gitee.com/biuaxia/lexin.git>

Demo: <http://129.28.192.85:8080/step?u={U}&p={P}&s={S}>

- `{U}`: 手机
- `{P}`: 密码
- `{S}`: 步数（需要刷的）

## 更新日志

仅从 `1.0.1-RELEASE` 开始记录，之前的版本更新请自行查看git commit。

| 版本号 | 更新内容 |
| ----- | ------ |
| 1.0.3-RELEASE | 新增定时任务处理，默认使用h2database提供支持 |
| 1.0.2-RELEASE | 1. 新增异常处理（主要用于密码错误或手机不存在等提示） <br/> 2. 修改默认启动端口为80 <br/> 3. 修改 `README.md` 文档的**使用说明#启动** <br/> 4. 新增 `.gitignore` 文件 <br/> 5. 修改日志打印为 `debug` 等级 |

## Todo

下面列出来的内容是规划中的功能，可做可不做，请勿强求！！！

- 添加每日自动刷步(完成于：`2020年10月19日更新`)

如果你有好的点子，请提 `Issues`.

## 如何使用

服务端需要具备：

- JDK8+

简单的安装可以直接执行：

```shell
yum install java-1.8.0-openjdk* -y
java -version
```

执行完可以看到下面类似的内容即可。

```bash
openjdk version "1.8.0_265"
OpenJDK Runtime Environment (build 1.8.0_265-b01)
OpenJDK 64-Bit Server VM (build 25.265-b01, mixed mode)
```

从 [Releases](https://github.com/biuaxia/lexin/releases) 页面获取jar包，执行：

```bash
nohup java -jar lexin-{VERSION}.jar --server.port=8080 &
```

直接启动即可，其中 `{VERSION}`为对应版本号，如 `1.0.0-RELEASE`。

`--server.port=8080` 表示启动端口为8080，可以修改，如 `--server.port=80`。

访问 `http://localhost:8080/step` ，接口请求参数如下：

| key | type | commits |
| --- | ---- | ------- |
| u | String | username, 乐心注册手机 |
| p | String | password, 乐心注册密码 |
| s | String | step, 需要刷取的步数（不要超过98800） |

## 定时任务补充说明

这里添加定时任务只是为了完成自己的TODO。

流程大致如下：

> 调用接口时添加的任务会自动存入定时任务（只需要添加一次（自动去重）加入定时任务）

### 关于Scheduled表达式形式

> @Scheduled(cron="seconds minutes hours day month week")
>
> 或
>
> @Scheduled(cron="seconds minutes hours day month week year")

![scheduled-cron.png](./doc/attachments/scheduled-cron.png)

> 星号(*)：可用在所有字段中，表示对应时间域的每一个时刻，例如，*在分钟字段时，表示“每分钟”；
>
> 问号（?）：该字符只在日期和星期字段中使用，它通常指定为“无意义的值”，相当于占位符；
>
> 减号(-)：表达一个范围，如在小时字段中使用“10-12”，则表示从10到12点，即10,11,12；
>
> 逗号(,)：表达一个列表值，如在星期字段中使用“MON,WED,FRI”，则表示星期一，星期三和星期五；
>
> 斜杠(/)：x/y表达一个等步长序列，x为起始值，y为增量步长值。如在秒数字段中使用0/15，则表示为0,15,30和45秒，而5/15在分钟字段中表示5,20,35,50，你也可以使用*/y，它等同于0/y；

L：该字符只在日期和星期字段中使用，代表“Last”的意思，但它在两个字段中意思不同。L在日期字段中，表示这个月份的最后一天，如一月的31号，非闰年二月的28号；如果L用在星期中，则表示星期六，等同于7。但是，如果L出现在星期字段里，而且在前面有一个数值X，则表示“这个月的最后X天”，例如，6L表示该月的最后星期五；

W：该字符只能出现在日期字段里，是对前导日期的修饰，表示离该日期最近的工作日。例如15W表示离该月15号最近的工作日，如果该月15号是星期六，则匹配14号星期五；如果15日是星期日，则匹配16号星期一；如果15号是星期二，那结果就是15号星期二。但必须注意关联的匹配日期不能够跨月，如你指定1W，如果1号是星期六，结果匹配的是3号星期一，而非上个月最后的那天。W字符串只能指定单一日期，而不能指定日期范围；

LW组合：在日期字段可以组合使用LW，它的意思是当月的最后一个工作日；

井号(#)：该字符只能在星期字段中使用，表示当月某个工作日。如6#3表示当月的第三个星期五(6表示星期五，#3表示当前的第三个)，而4#5表示当月的第五个星期三，假设当月没有第五个星期三，忽略不触发；

C：该字符只在日期和星期字段中使用，代表“Calendar”的意思。它的意思是计划所关联的日期，如果日期没有被关联，则相当于日历中所有日期。例如5C在日期字段中就相当于日历5日以后的第一天。1C在星期字段中相当于星期日后的第一天。

`Cron表达式对特殊字符的大小写不敏感，对代表星期的缩写英文大小写也不敏感。`

## 补充

**注意：** `application.yml` 为乐心接口配置，如果功能失效，请关注本项目的最新进度。
