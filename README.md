# ✈️ 一、工具概述

springboot-exp-knif4j 主要用于Swagger接口分析 ，批量测试接口是否存在未授权。

日常渗透过程中，经常会碰到Spring Boot搭建的微服务，当发现接口文档泄露时，手工测试API接口未授权工作量较大，于是参考了工具 [springboot-exp]:https://github.com/lijiejie/swagger-exp 的原理，开发了一款基 于Knife4j 的 Swagger 接口自动化测试未授权工具，较其他自动化测试工具而言，本工具增加了参数支持，优化了用户体验，改进了Swagger测试界面，并且支持自定义设置全局请求头参数。

**当前工具版本号：V1.1--2024/05/01**

# 📝 二、功能支持

- [ ] 支持本地开启HTTP服务打开Chrome，供分析接口使用。

	- 接口测试页面为 `Knife4j`页面，支持自定义设置全局请求头参数。（集 Swagger2 和 OpenAPI3 为一体，兼容 OpenAPI 2.0、3.0）

	- 支持自动获取 `api-json` 的 API 集保存到本地 Knife4j 页面进行测试

	- 支持单独将 `api-json` 的 API 集放到本地 Knif4j 页面进行测试

- [ ] 支持自动测试接口是否存在未授权的可能。

	- 支持自动化 GET / POST 请求 api-doc 的所有接口，返回 Response Code / Content-Type / Content-Length

	- 支持自动填充参数值去遍历所有API接口，用于分析接口是否可以未授权访问利用

- [ ] 支持自动化分析接口是否存在敏感参数。

	- 分析接口是否存在敏感参数，例如url参数，容易引入外网的SSRF漏洞

- [ ] 检测 API认证绕过漏洞

	- 当存在HTTP认证绕过漏洞时，本地服务器拦截API文档，修改path，以便直接在Swagger UI中进行测试

- [ ] 支持扫描结果生成

	- `result_ApiScanSummary.txt`	接口测试结果汇总（是一直累加的）
- `result_RequestDeny.txt`	自动化测试时，请求被拒绝的接口
	- `result_sensitive.txt`	存在敏感参数的接口
- `result_UnAuthorized.txt`	可能存在未授权的接口

# 🛸 三、 工具界面：

## 0x01 控制台输出界面

自动化 GET / POST 请求，疑似未授权的接口会标红显示，存在敏感参数的接口会标绿显示，扫描完成结果分类输出到文本。

![image-20240428025116659](/image/image-20240428025116659.png)

扫描完成本地自动开启 HTTP 服务进行监听，并打开禁用 CORS 的 Chrome，打开 Knife4j 界面，方便手工分析接口

![image-20240428030229189](/image/image-20240428030229189.png)

##  0x02 Knife4j 界面

Knife4j 界面功能的使用参考官方文档：https://doc.xiaominfo.com

**注意：（ Knife4j 界面里一定要在`个性化设置`手动勾选 HOST，再刷新页面，才能进行正常手工在页面进行测试）**

![image-20240428031931404](/image/image-20240428031931404.png)

1、支持自定义设置全局请求头参数。

![image-20240428032119132](/image/image-20240428032119132.png)

2、自动列出所有分组下接口数量，接口文档界面可更直观地预览接口参数描述

![image-20240427225134294](/image/image-20240427225134294.png)

3、可直接在 Knife4j 界面发起请求，调试接口。相比于原生Swagger-ui界面更符合用户使用习惯。

![image-20240427225223789](/image/image-20240427225223789.png)

# 🚨 四、工具使用注意

- [ ] 一定要在`个性化设置`手动勾选 HOST，再刷新页面
- [ ] 部分 HTTPS 网站测试时报错` Network Error`，可在`个性化设置` HOST 处加上协议号，如 https://example.com
- [ ] 如果在控制台 `Ctrl+C` 卡住，关闭 chrome 浏览器则可以结束本地 HTTP 服务
- [ ]  -u 指定 URL 时，路径末尾不要带多余的符号（如 #/）

![image-20240428031931404](/image/image-20240428031931404.png)



# 🐉 五、工具使用

**注意：（ Knife4j 界面里一定要在`个性化设置`手动勾选 HOST，再刷新页面，才能进行正常测试）**

### 1、本工具的参数如下：

```
➜ python swagger-exp-knife4j.py

  ____                                     _____
/ ___|_      ____ _  __ _  __ _  ___ _ __| ____|_  ___ __
\___ \ \ /\ / / _` |/ _` |/ _` |/ _ \ '__|  _| \ \/ / '_ \
 ___) \ V  V / (_| | (_| | (_| |  __/ |  | |___ >  <| |_) |
|____/ \_/\_/ \__,_|\__, |\__, |\___|_|  |_____/_/\_\ .__/
                    |___/ |___/                     |_|

 _  __      _  __      _  _   _     +-------+
| |/ /_ __ (_)/ _| ___| || | (_)    + v1.1  +
| ' /| '_ \| | |_ / _ \ || |_| |    +-------+
| . \| | | | |  _|  __/__   _| |
|_|\_\_| |_|_|_|  \___|  |_|_/ |
                           |__/


参数：
    -u  --url       指定 Swagger 相关URL
    -c  --chrome    本地打开chrome禁用CORS，打开 Knife4j 界面

用法:
    只打开Knife4j 进行分析：             python3 swagger-exp-knife4j.py -c
    扫描所有API集，分析接口是否存在未授权：  python3 swagger-exp-knife4j.py -u http://example.com/swagger-resources
    扫描一个API集，分析接口是否存在未授权：  python3 swagger-exp-knife4j.py -u http://example.com/v2/api-docs
    扫描一个API集，爬取api-doc打开Chrome：python3 swagger-exp-knife4j.py -u http://example.com/swagger-ui.html

注意：
    1、Knife4j 界面里一定要在 '个性化设置' 手动勾选 HOST 刷新页面,才能进行正常测试。
    2、部分 HTTPS 网站测试时报错Network Error，可在个性化设置 HOST 处加上协议号，如 https://example.com

```





### 2、扫描 swagger-ui.html

扫描一个API集，爬取目标 `api-doc `内容，保存到本地存为 `api-docs.json`，打开 Knife4j 界面

```
python3 swagger-exp-knife4j.py -u http://example.com/swagger-ui.html
```

![image-20240428031331211](/image/image-20240428031331211.png)

### 2、只打开 Chrome 生成页面，不做任何扫描

只打开禁用 CORS 的 chrome， 把本地 `api-docs.json` 文件渲染到 Knife4j 界面进行测试。（所以当网站接口内容无法爬取到时，可以手动把网站 /api-docs 覆盖掉`api-docs.json`，然后 ` -c` 打开 Chrome）

```
python3 swagger-exp-knife4j.py -c
```

![image-20240427221626545](/image/image-20240427221626545.png)

### 4、扫描 /api-docs

扫描一个API集，分析接口是否存在未授权，疑似存在未授权的接口保存在 `result_UnAuthorized.txt`，打开 Knife4j 界面

```
python3 swagger-exp-knife4j.py -u http://example.com/v2/api-docs
```

### 3、扫描 /swagger-resources

扫描所有API集，并分析接口是否存在未授权，疑似存在未授权的接口保存在 `result_UnAuthorized.txt`，打开 Knife4j 界面

```
python3 swagger-exp-knife4j.py -u http://example.com/swagger-resources
```

# 🖐 六、免责声明



如果您下载使用即表明您信任本工具，在使用时造成的损失和伤害，我们不承担任何责任。使用本工具的过程中存在任何非法行为需自行承担相应后果，我们将不承担任何法律及连带责任。您的下载、安装、使用等行为即视为您已阅读并同意上述协议的约束