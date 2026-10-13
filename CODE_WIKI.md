# litemall Code Wiki

> 又一个小商场系统：Spring Boot 后端 + Vue 管理员前端 + 微信小程序用户前端 + Vue 用户移动端
>
> 当前版本：V 1.7.0（2020-02-15）｜开源协议：MIT

---

## 目录

- [1. 项目概述](#1-项目概述)
  - [1.1 项目简介](#11-项目简介)
  - [1.2 技术栈](#12-技术栈)
  - [1.3 功能矩阵](#13-功能矩阵)
- [2. 项目整体架构](#2-项目整体架构)
  - [2.1 模块划分](#21-模块划分)
  - [2.2 模块依赖关系](#22-模块依赖关系)
  - [2.3 部署架构](#23-部署架构)
  - [2.4 请求链路](#24-请求链路)
- [3. 后端模块详解](#3-后端模块详解)
  - [3.1 litemall（父工程）](#31-litemall父工程)
  - [3.2 litemall-core（基础核心模块）](#32-litemall-core基础核心模块)
  - [3.3 litemall-db（数据访问模块）](#33-litemall-db数据访问模块)
  - [3.4 litemall-wx-api（小商城 API）](#34-litemall-wx-api小商城-api)
  - [3.5 litemall-admin-api（管理后台 API）](#35-litemall-admin-api管理后台-api)
  - [3.6 litemall-all / litemall-all-war（聚合部署模块）](#36-litemall-all--litemall-all-war聚合部署模块)
- [4. 前端模块详解](#4-前端模块详解)
  - [4.1 litemall-admin（管理后台前端）](#41-litemall-admin管理后台前端)
  - [4.2 litemall-vue（轻商城移动端）](#42-litemall-vue轻商城移动端)
  - [4.3 litemall-wx（微信小程序）](#43-litemall-wx微信小程序)
  - [4.4 renard-wx（真实小商场小程序）](#44-renard-wx真实小商场小程序)
- [5. 关键类与函数说明](#5-关键类与函数说明)
  - [5.1 核心工具与配置类](#51-核心工具与配置类)
  - [5.2 认证与权限机制](#52-认证与权限机制)
  - [5.3 数据访问层关键类](#53-数据访问层关键类)
  - [5.4 业务服务层关键类](#54-业务服务层关键类)
- [6. 数据库设计](#6-数据库设计)
- [7. 错误码与响应规范](#7-错误码与响应规范)
- [8. 项目运行方式](#8-项目运行方式)
  - [8.1 开发环境要求](#81-开发环境要求)
  - [8.2 数据库初始化](#82-数据库初始化)
  - [8.3 后端服务启动](#83-后端服务启动)
  - [8.4 管理后台前端启动](#84-管理后台前端启动)
  - [8.5 小程序前端启动](#85-小程序前端启动)
  - [8.6 轻商城前端启动](#86-轻商城前端启动)
  - [8.7 传统服务器部署](#87-传统服务器部署)
  - [8.8 Docker 部署](#88-docker-部署)
- [9. 配置体系](#9-配置体系)

---

## 1. 项目概述

### 1.1 项目简介

litemall 是一个开源的小商场系统，采用前后端分离架构，后端基于 Spring Boot 2.1.5，前端包含三个独立客户端：

- **管理后台前端**（`litemall-admin`）：基于 Vue + Element UI，面向商城运营人员
- **微信小程序前端**（`litemall-wx` / `renard-wx`）：面向 C 端用户
- **轻商城移动端**（`litemall-vue`）：基于 Vue + Vant 的 H5 移动端

后端服务对外暴露两套 API：
- `/wx/**`：小商城用户端接口（微信小程序、轻商城共用）
- `/admin/**`：管理后台接口

### 1.2 技术栈

| 层级 | 技术 |
|------|------|
| 后端框架 | Spring Boot 2.1.5.RELEASE / Java 1.8 |
| ORM | MyBatis 1.3.2 + MyBatis Generator（自动生成 domain/mapper/xml） |
| 分页 | PageHelper 1.2.5 |
| 数据库 | MySQL 5.7 / 连接池 Druid 1.1.10 |
| 管理后台鉴权 | Apache Shiro 1.4.0（Session 鉴权 + RBAC 权限） |
| 小商城鉴权 | JWT（`com.auth0:java-jwt:3.4.1`，HS256，2 小时过期） |
| 参数校验 | Hibernate Validator 6.1.0 |
| 微信能力 | weixin-java-pay / weixin-java-miniapp 3.3.0 |
| 对象存储 | 本地 / 阿里云 OSS / 腾讯云 COS / 七牛云 |
| 通知 | 邮件（spring-boot-starter-mail）、短信（阿里云/腾讯云） |
| API 文档 | Swagger2 2.9.2 + swagger-bootstrap-ui 1.9.6 |
| 管理后台前端 | Vue 2.6.10 + Element UI 2.13.2（基于 vue-element-admin 4.3.0） |
| 轻商城前端 | Vue 2.5 + Vant 2.0 |
| 小程序 | 原生微信小程序 + vant-weapp 组件库 |

### 1.3 功能矩阵

**小商城功能**：首页、专题列表/详情、分类列表/详情、品牌列表/详情、新品首发、人气推荐、优惠券、团购、搜索、商品详情/评价/分享、购物车、下单、订单列表/详情/售后、地址、收藏、足迹、意见反馈、客服。

**管理平台功能**：会员管理、商城管理、商品管理、推广管理、系统管理（管理员/角色/权限）、配置管理、统计报表、通知管理、日志管理、售后管理。

---

## 2. 项目整体架构

### 2.1 模块划分

项目采用 Maven 多模块结构，共 6 个后端 Maven 子模块 + 4 个独立前端工程：

```
litemall/
├── pom.xml                       # 父 POM（聚合 + 依赖版本管理）
├── litemall-core/                # 基础核心模块（工具、存储、通知、配置、任务）
├── litemall-db/                  # 数据访问模块（domain/mapper/service + SQL）
├── litemall-wx-api/              # 小商城 API 后端（/wx/**）
├── litemall-admin-api/           # 管理后台 API 后端（/admin/**）
├── litemall-all/                 # 聚合可执行 jar 模块
├── litemall-all-war/             # 聚合 WAR 部署模块（外置 Tomcat）
├── litemall-admin/               # 管理后台前端（Vue + Element UI）
├── litemall-vue/                 # 轻商城移动端前端（Vue + Vant）
├── litemall-wx/                  # 微信小程序（测试小商城）
├── renard-wx/                    # 微信小程序（真实小商城，独立 UI）
├── deploy/                        # 传统服务器部署脚本与配置
└── docker/                       # Docker 部署（docker-compose）
```

> 说明：后端 6 个模块在父 `pom.xml` 的 `<modules>` 中聚合构建；前端 4 个工程独立构建，其产物（dist）在打包阶段被拷贝进 `litemall-all` 的 `static` 目录，实现单服务托管静态资源。

### 2.2 模块依赖关系

后端模块的 Maven 依赖方向（从底层到上层）：

```
litemall-db   ──依赖──> spring-boot / mybatis / druid / pagehelper
litemall-core ──依赖──> litemall-db + spring-boot-web/mail/json + 各云 SDK + weixin-java
litemall-wx-api    ──依赖──> litemall-core + litemall-db + jwt + swagger
litemall-admin-api ──依赖──> litemall-core + litemall-db + shiro + swagger
litemall-all       ──依赖──> litemall-wx-api + litemall-admin-api + litemall-core + litemall-db
litemall-all-war   ──依赖──> 同 litemall-all（packaging=war，provided tomcat）
```

关键依赖要点：
- `litemall-core` 依赖 `litemall-db`（[litemall-core/pom.xml](file:///workspace/litemall-core/pom.xml#L80-L82)），因为存储/通知等核心服务需要写库（如 `LitemallStorageService`）。
- `litemall-wx-api` 与 `litemall-admin-api` 都依赖 `litemall-core` 与 `litemall-db`，但二者互不依赖，业务上各自独立。
- `litemall-all` 是聚合点：把两个 API 模块 + core + db 打到同一个可执行 jar 中，统一在一个 Spring Boot 进程里暴露 `/wx/**` 和 `/admin/**` 两套接口。

### 2.3 部署架构

```
                    ┌──────────────────────────────┐
                    │   浏览器 / 微信小程序 / 手机  │
                    └──────────────┬───────────────┘
                                   │ HTTP
            ┌──────────────────────▼──────────────────────┐
            │   litemall-all (Spring Boot, port 8080)       │
            │   ┌──────────────┐  ┌──────────────────┐    │
            │   │ /wx/**       │  │ /admin/**         │    │
            │   │ (litemall-wx- │  │ (litemall-admin-  │    │
            │   │  api)         │  │  api)             │    │
            │   └──────────────┘  └──────────────────┘    │
            │   ┌─────────────────────────────────────┐  │
            │   │ static/ (litemall-admin dist)         │  │  ← 管理后台静态资源
            │   │ static/vue/ (litemall-vue dist)      │  │  ← 轻商城静态资源
            │   └─────────────────────────────────────┘  │
            │   litemall-core (存储/通知/任务/配置)        │
            │   litemall-db (MyBatis domain/service)      │
            └──────────────────────┬──────────────────────┘
                                   │ JDBC (Druid)
                          ┌────────▼────────┐
                          │   MySQL 5.7      │
                          └──────────────────┘
```

- 单进程双 API：`litemall-all` 同时承载小商城与管理后台两套 REST 接口。
- 静态资源托管：管理后台前端构建产物被拷贝到 jar 内 `static/`，访问 `http://host:8080/#/login`；轻商城产物拷贝到 `static/vue/`，访问 `http://host:8080/vue/index.html`。
- `litemall-all-war` 提供等价的 WAR 包形态，用于外置 Tomcat 部署（Tomcat 为 `provided` 作用域）。

### 2.4 请求链路

以小商城下单为例：

```
微信小程序(litemall-wx)
  └─ utils/util.js (wx.request, 注入 X-Litemall-Token)
     └─ HTTP POST /wx/order/submit
        └─ WxOrderController.submit(@LoginUser userId, body)
           └─ WxOrderService.submit()  ── 事务
              └─ LitemallOrderService / LitemallOrderGoodsService / LitemallCartService ...
                 └─ LitemallXxxMapper (MyBatis) ── MySQL
```

管理后台请求链路类似，鉴权改用 Shiro Session（请求头 `X-Litemall-Admin-Token` 传递 sessionId）。

---

## 3. 后端模块详解

### 3.1 litemall（父工程）

- 路径：[pom.xml](file:///workspace/pom.xml)
- `groupId=org.linlinjava`，`artifactId=litemall`，`version=0.1.0`，`packaging=pom`
- 继承 `spring-boot-starter-parent:2.1.5.RELEASE`
- 聚合 6 个子模块：`litemall-core`、`litemall-db`、`litemall-wx-api`、`litemall-admin-api`、`litemall-all`、`litemall-all-war`
- 在 `<dependencyManagement>` 中统一锁定所有第三方依赖版本（MyBatis、PageHelper、Shiro、Druid、weixin-java、java-jwt、Swagger、各云 SDK 等）
- 全局依赖 `spring-boot-devtools`（热部署）与测试栈（`spring-boot-starter-test`、PowerMock + Mockito）

### 3.2 litemall-core（基础核心模块）

- 路径：[litemall-core](file:///workspace/litemall-core)
- 定位：被 `wx-api` 与 `admin-api` 共用的基础能力层，不含业务逻辑。
- 依赖：Spring Boot Web/Mail/JSON、Hibernate Validator、阿里云/腾讯云/七牛云 SDK、微信支付与小程序 SDK、`litemall-db`。
- 包结构（`org.linlinjava.litemall.core`）：

| 包 | 职责 | 代表类 |
|----|------|--------|
| `util` | 通用工具 | `ResponseUtil`、`JacksonUtil`、`IpUtil`、`DateTimeUtil`、`BeanUtil`、`HttpUtil`、`CharUtil`、`RegexUtil`、`SystemInfoPrinter`、`bcrypt.BCrypt` / `BCryptPasswordEncoder` |
| `config` | 全局配置 | `CorsConfig`（跨域）、`AsyncConfig`（异步线程池）、`JacksonConfig`、`ValidatorConfiguration`、`WxConfig` + `WxProperties`（微信配置） |
| `storage` | 对象存储 | `Storage`（接口）、`StorageService`（统一入口）、`LocalStorage`/`AliyunStorage`/`TencentStorage`/`QiniuStorage`、`config.StorageAutoConfiguration` + `StorageProperties` |
| `notify` | 通知服务 | `NotifyService`、`NotifyType`（枚举）、`SmsSender`（接口）、`AliyunSmsSender`、`TencentSmsSender`、`SmsResult`、`config.NotifyAutoConfiguration` + `NotifyProperties` |
| `express` | 物流查询 | `ExpressService`、`dao.ExpressInfo`/`Traces`、`config.ExpressProperties` + `ExpressAutoConfiguration` |
| `system` | 系统配置 | `SystemConfig`（内存配置 Map + 静态 getter）、`SystemInistService`（启动时从库加载配置） |
| `task` | 延时任务 | `Task`（实现 `Delayed`）、`TaskService`（基于 `DelayQueue` 的单线程延时任务调度器） |
| `qcode` | 二维码 | `QCodeService`（商品分享图自动生成） |
| `validator` | 排序校验 | `Order`/`Sort` 注解 + `OrderValidator`/`SortValidator` |

关键类说明：

- [ResponseUtil](file:///workspace/litemall-core/src/main/java/org/linlinjava/litemall/core/util/ResponseUtil.java)：统一响应封装，返回 `{errno, errmsg, data}`。提供 `ok()/ok(data)/okList(list)` 及各类失败快捷方法（`badArgument`→401、`unlogin`→501、`serious`→502、`unsupport`→503、`updatedDateExpired`→504、`updatedDataFailed`→505、`unauthz`→506）。错误码规范见 [第 7 节](#7-错误码与响应规范)。
- [SystemConfig](file:///workspace/litemall-core/src/main/java/org/linlinjava/litemall/core/system/SystemConfig.java)：系统业务配置的静态访问器，配置项以常量 key 形式定义（小程序首页数量、运费、订单超时、商城信息等），值缓存在内存 `SYSTEM_CONFIGS` Map 中，由 `SystemInistService` 在启动时从 `litemall_system` 表载入。
- [StorageService](file:///workspace/litemall-core/src/main/java/org/linlinjava/litemall/core/storage/StorageService.java)：文件存储统一入口。`store()` 生成随机 key、调用底层 `Storage` 实现写入、生成访问 URL 并落库 `litemall_storage`。支持 local/aliyun/tencent/qiniu 四种 active 模式切换。
- [NotifyService](file:///workspace/litemall-core/src/main/java/org/linlinjava/litemall/core/notify/NotifyService.java)：通知服务，支持邮件（`notifyMail`，`@Async`）与短信模板（`notifySmsTemplate`/`notifySmsTemplateSync`），短信发送方可在阿里云/腾讯云间切换。
- [TaskService](file:///workspace/litemall-core/src/main/java/org/linlinjava/litemall/core/task/TaskService.java)：基于 `java.util.concurrent.DelayQueue` 的延时任务调度，`@PostConstruct` 启动单线程消费循环，`addTask/removeTask` 对外暴露。用于订单超时自动取消等场景。
- [CorsConfig](file:///workspace/litemall-core/src/main/java/org/linlinjava/litemall/core/config/CorsConfig.java)：全局跨域过滤器，允许所有 Origin/Header/Method，预检缓存 30 天。

### 3.3 litemall-db（数据访问模块）

- 路径：[litemall-db](file:///workspace/litemall-db)
- 定位：数据访问层，基于 MyBatis + MyBatis Generator 自动生成。
- 依赖：`mybatis-spring-boot-starter`、`pagehelper-spring-boot-starter`、`mysql-connector-java`、`druid-spring-boot-starter`；配置 `mybatis-generator-maven-plugin`。
- 包结构（`org.linlinjava.litemall.db`）：

| 包 | 内容 |
|----|------|
| `domain` | 实体类（`Litemall*`）与查询条件类（`Litemall*Example`），以及 `UserVo`/`LitemallSystem` 等补充 POJO |
| `dao` | MyBatis Mapper 接口（`Litemall*Mapper`），对应 XML 在 resources 下 |
| `service` | 业务数据服务（`Litemall*Service`），封装对 Mapper 的调用与 `Example` 查询构造 |

- MyBatis Generator 配置：[generatorConfig.xml](file:///workspace/litemall-db/mybatis-generator/generatorConfig.xml)
  - 运行时 `MyBatis3`，启用 `ToStringPlugin`、`EqualsHashCodePlugin`、`SelectOneByExamplePlugin`、`ExampleEnhancedPlugin` 等插件
  - 生成目标：`domain` 包产实体、`dao` 包产 Mapper 接口与 XML
  - 对 `litemall_order` 等含数组字段（specifications/comments）的表配置了 `typeHandler` 覆盖

- 典型代码模式（QBE / Example 模式）：
  - [LitemallOrderService](file:///workspace/litemall-db/src/main/java/org/linlinjava/litemall/db/service/LitemallOrderService.java)：注入 `LitemallOrderMapper`，使用 `LitemallOrderExample` + `Criteria` 组合查询条件（用户、订单号、时间、状态），并配合 `PageHelper.startPage` 做分页。
  - Mapper 接口（[LitemallOrderMapper](file:///workspace/litemall-db/src/main/java/org/linlinjava/litemall/db/dao/LitemallOrderMapper.java)）提供 `countByExample`、`deleteByExample`、`insert`、`selectByExample`、`selectOneByExample` 等标准方法。

- domain 实体清单（部分）：`LitemallAdmin`、`LitemallUser`、`LitemallGoods`、`LitemallGoodsProduct`、`LitemallGoodsAttribute`、`LitemallGoodsSpecification`、`LitemallOrder`、`LitemallOrderGoods`、`LitemallCart`、`LitemallAddress`、`LitemallBrand`、`LitemallCategory`、`LitemallCoupon`、`LitemallCouponUser`、`LitemallComment`、`LitemallCollect`、`LitemallFootprint`、`LitemallTopic`、`LitemallAd`、`LitemallAftersale`、`LitemallGroupon`/`LitemallGrouponRules`、`LitemallIssue`、`LitemallKeyword`、`LitemallSearchHistory`、`LitemallFeedback`、`LitemallRegion`、`LitemallStorage`、`LitemallNotice`/`LitemallNoticeAdmin`、`LitemallLog`、`LitemallRole`、`LitemallPermission`、`LitemallSystem` 等。每个实体配套一个 `*Example` 查询条件类。

- service 清单（共 35 个，详见 [service 目录](file:///workspace/litemall-db/src/main/java/org/linlinjava/litemall/db/service)），其中几个特殊的：
  - `StatService`：统计报表聚合查询（用户/订单/商品统计）
  - `CouponAssignService` / `CouponVerifyService`：优惠券领取与核销
  - `LitemallSystemConfigService`：系统配置表 CRUD，配合 `SystemConfig` 提供运行时配置

- SQL 资源（[litemall-db/sql](file:///workspace/litemall-db/sql)）：
  - `litemall_schema.sql`：建库建用户
  - `litemall_table.sql`：建表
  - `litemall_data.sql`：测试数据

### 3.4 litemall-wx-api（小商城 API）

- 路径：[litemall-wx-api](file:///workspace/litemall-wx-api)
- 定位：小商城用户端 API 后端，服务于微信小程序与轻商城移动端。
- 依赖：`litemall-core` + `litemall-db` + `weixin-java-pay` + `weixin-java-miniapp` + `java-jwt` + Swagger。
- 配置（[application.yml](file:///workspace/litemall-wx-api/src/main/resources/application.yml)）：激活 `db, core, wx` profile，端口 `8082`，`wx` 包日志 DEBUG。
- 启动类 [Application](file:///workspace/litemall-wx-api/src/main/java/org/linlinjava/litemall/wx/Application.java)：扫描 `db/core/wx` 三个包，`@MapperScan("org.linlinjava.litemall.db.dao")`，启用事务与定时任务。
- 包结构（`org.linlinjava.litemall.wx`）：

| 包 | 职责 |
|----|------|
| `web` | 21 个 REST 控制器，全部以 `Wx` 前缀命名，路径前缀 `/wx/**` |
| `service` | 小商城专属服务（订单流程、token、缓存、团购规则、区域、验证码、用户信息） |
| `annotation` | `@LoginUser` 参数注解 |
| `annotation.support` | `LoginUserHandlerMethodArgumentResolver`（参数解析器） |
| `util` | `JwtHelper`（JWT 生成/校验） |
| `config` | `WxWebMvcConfiguration`（注册参数解析器） |

- 控制器清单（`/wx/**`）：

| 控制器 | 路径前缀 | 主要职责 |
|--------|----------|----------|
| [WxAuthController](file:///workspace/litemall-wx-api/src/main/java/org/linlinjava/litemall/wx/web/WxAuthController.java) | `/wx/auth` | 账号登录、微信登录、注册验证码、账号注册、重置密码、退出 |
| [WxIndexController](file:///workspace/litemall-wx-api/src/main/java/org/linlinjava/litemall/wx/web/WxIndexController.java) | `/wx/index` | 首页数据（banner/分类/新品/人气/品牌/专题） |
| [WxGoodsController](file:///workspace/litemall-wx-api/src/main/java/org/linlinjava/litemall/wx/web/WxGoodsController.java) | `/wx/goods` | 商品列表、详情（含规格/属性/问题/评论/品牌/团购）、分类、数量 |
| [WxCatalogController](file:///workspace/litemall-wx-api/src/main/java/org/linlinjava/litemall/wx/web/WxCatalogController.java) | `/wx/catalog` | 分类目录 |
| [WxBrandController](file:///workspace/litemall-wx-api/src/main/java/org/linlinjava/litemall/wx/web/WxBrandController.java) | `/wx/brand` | 品牌列表/详情 |
| [WxTopicController](file:///workspace/litemall-wx-api/src/main/java/org/linlinjava/litemall/wx/web/WxTopicController.java) | `/wx/topic` | 专题列表/详情/评论 |
| [WxCartController](file:///workspace/litemall-wx-api/src/main/java/org/linlinjava/litemall/wx/web/WxCartController.java) | `/wx/cart` | 购物车增删改查、数量计算 |
| [WxOrderController](file:///workspace/litemall-wx-api/src/main/java/org/linlinjava/litemall/wx/web/WxOrderController.java) | `/wx/order` | 订单列表/详情/提交/取消/预支付/H5支付/微信回调/退款/确认收货/删除 |
| [WxCouponController](file:///workspace/litemall-wx-api/src/main/java/org/linlinjava/litemall/wx/web/WxCouponController.java) | `/wx/coupon` | 优惠券列表/选择 |
| [WxGrouponController](file:///workspace/litemall-wx-api/src/main/java/org/linlinjava/litemall/wx/web/WxGrouponController.java) | `/wx/groupon` | 团购列表/详情/我的团购 |
| [WxAddressController](file:///workspace/litemall-wx-api/src/main/java/org/linlinjava/litemall/wx/web/WxAddressController.java) | `/wx/address` | 收货地址 CRUD |
| [WxCollectController](file:///workspace/litemall-wx-api/src/main/java/org/linlinjava/litemall/wx/web/WxCollectController.java) | `/wx/collect` | 商品收藏 |
| [WxFootprintController](file:///workspace/litemall-wx-api/src/main/java/org/linlinjava/litemall/wx/web/WxFootprintController.java) | `/wx/footprint` | 浏览足迹 |
| [WxCommentController](file:///workspace/litemall-wx-api/src/main/java/org/linlinjava/litemall/wx/web/WxCommentController.java) | `/wx/comment` | 商品评论 |
| [WxFeedbackController](file:///workspace/litemall-wx-api/src/main/java/org/linlinjava/litemall/wx/web/WxFeedbackController.java) | `/wx/feedback` | 意见反馈 |
| [WxIssueController](file:///workspace/litemall-wx-api/src/main/java/org/linlinjava/litemall/wx/web/WxIssueController.java) | `/wx/issue` | 常见问题 |
| [WxSearchController](file:///workspace/litemall-wx-api/src/main/java/org/linlinjava/litemall/wx/web/WxSearchController.java) | `/wx/search` | 搜索历史/热门关键词 |
| [WxAftersaleController](file:///workspace/litemall-wx-api/src/main/java/org/linlinjava/litemall/wx/web/WxAftersaleController.java) | `/wx/aftersale` | 售后申请/列表/详情 |
| [WxStorageController](file:///workspace/litemall-wx-api/src/main/java/org/linlinjava/litemall/wx/web/WxStorageController.java) | `/wx/storage` | 文件上传（image 流） |
| [WxUserController](file:///workspace/litemall-wx-api/src/main/java/org/linlinjava/litemall/wx/web/WxUserController.java) | `/wx/user` | 用户信息/头像更新 |

- 关键服务：
  - [WxOrderService](file:///workspace/litemall-wx-api/src/main/java/org/linlinjava/litemall/wx/service/WxOrderService.java)：订单核心业务，定义订单状态流转与用户可操作状态，处理下单/取消/支付/退款/发货/收货。
  - `UserTokenManager` + [JwtHelper](file:///workspace/litemall-wx-api/src/main/java/org/linlinjava/litemall/wx/util/JwtHelper.java)：用户登录态管理，`generateToken(userId)` 生成 JWT。
  - `CaptchaCodeManager`：注册验证码缓存与校验。
  - `HomeCacheManager`：首页数据缓存。
  - `WxGrouponRuleService`：团购规则与开团逻辑。
  - `GetRegionService`：行政区域数据。
  - `UserInfoService`：用户信息封装。

### 3.5 litemall-admin-api（管理后台 API）

- 路径：[litemall-admin-api](file:///workspace/litemall-admin-api)
- 定位：管理后台 API 后端，服务于 `litemall-admin` 前端。
- 依赖：`litemall-core` + `litemall-db` + `shiro-spring-boot-web-starter` + Swagger。
- 配置（[application.yml](file:///workspace/litemall-admin-api/src/main/resources/application.yml)）：激活 `admin` profile，端口 `8083`，Swagger 可切换。
- 启动类 [Application](file:///workspace/litemall-admin-api/src/main/java/org/linlinjava/litemall/admin/Application.java)：扫描 `db/core/admin`，启用事务与定时任务。
- 包结构（`org.linlinjava.litemall.admin`）：

| 包 | 职责 |
|----|------|
| `web` | 30 个管理端控制器，路径前缀 `/admin/**` |
| `service` | 管理端业务服务（`AdminOrderService`、`AdminGoodsService`、`LogHelper`） |
| `config` | `ShiroConfig`（Shiro 安全配置） |
| `shiro` | `AdminAuthorizingRealm`（认证授权 Realm）、`AdminWebSessionManager`（自定义 Session 管理） |
| `annotation` | `@RequiresPermissionsDesc`（权限描述注解） |
| `util` | `PermissionUtil`（权限元数据扫描）、`Permission`、`AdminResponseCode`（管理端业务错误码 6xx） |

- 控制器清单（`/admin/**`，共 30 个，见 [web 目录](file:///workspace/litemall-admin-api/src/main/java/org/linlinjava/litemall/admin/web)）：

| 控制器 | 职责 |
|--------|------|
| [AdminAuthController](file:///workspace/litemall-admin-api/src/main/java/org/linlinjava/litemall/admin/web/AdminAuthController.java) | 管理员登录/退出/info、401/403 兜底 |
| [AdminIndexController](file:///workspace/litemall-admin-api/src/main/java/org/linlinjava/litemall/admin/web/AdminIndexController.java) | 首页接口（公开） |
| [AdminAdminController](file:///workspace/litemall-admin-api/src/main/java/org/linlinjava/litemall/admin/web/AdminAdminController.java) | 管理员账号 CRUD |
| [AdminRoleController](file:///workspace/litemall-admin-api/src/main/java/org/linlinjava/litemall/admin/web/AdminRoleController.java) | 角色与权限分配 |
| [AdminGoodsController](file:///workspace/litemall-admin-api/src/main/java/org/linlinjava/litemall/admin/web/AdminGoodsController.java) | 商品/规格/属性/参数 CRUD |
| [AdminOrderController](file:///workspace/litemall-admin-api/src/main/java/org/linlinjava/litemall/admin/web/AdminOrderController.java) | 订单查询/详情/退款/发货/删除/回复 |
| [AdminAftersaleController](file:///workspace/litemall-admin-api/src/main/java/org/linlinjava/litemall/admin/web/AdminAftersaleController.java) | 售后管理 |
| [AdminBrandController](file:///workspace/litemall-admin-api/src/main/java/org/linlinjava/litemall/admin/web/AdminBrandController.java) | 品牌管理 |
| [AdminCategoryController](file:///workspace/litemall-admin-api/src/main/java/org/linlinjava/litemall/admin/web/AdminCategoryController.java) | 类目管理 |
| [AdminCouponController](file:///workspace/litemall-admin-api/src/main/java/org/linlinjava/litemall/admin/web/AdminCouponController.java) | 优惠券管理 |
| [AdminGrouponController](file:///workspace/litemall-admin-api/src/main/java/org/linlinjava/litemall/admin/web/AdminGrouponController.java) | 团购规则管理 |
| [AdminTopicController](file:///workspace/litemall-admin-api/src/main/java/org/linlinjava/litemall/admin/web/AdminTopicController.java) | 专题管理 |
| [AdminAdController](file:///workspace/litemall-admin-api/src/main/java/org/linlinjava/litemall/admin/web/AdminAdController.java) | 广告管理 |
| [AdminKeywordController](file:///workspace/litemall-admin-api/src/main/java/org/linlinjava/litemall/admin/web/AdminKeywordController.java) | 关键词管理 |
| [AdminIssueController](file:///workspace/litemall-admin-api/src/main/java/org/linlinjava/litemall/admin/web/AdminIssueController.java) | 常见问题管理 |
| [AdminUserController](file:///workspace/litemall-admin-api/src/main/java/org/linlinjava/litemall/admin/web/AdminUserController.java) | 会员管理 |
| [AdminAddressController](file:///workspace/litemall-admin-api/src/main/java/org/linlinjava/litemall/admin/web/AdminAddressController.java) | 收货地址查询 |
| [AdminCollectController](file:///workspace/litemall-admin-api/src/main/java/org/linlinjava/litemall/admin/web/AdminCollectController.java) | 会员收藏 |
| [AdminFootprintController](file:///workspace/litemall-admin-api/src/main/java/org/linlinjava/litemall/admin/web/AdminFootprintController.java) | 会员足迹 |
| [AdminHistoryController](file:///workspace/litemall-admin-api/src/main/java/org/linlinjava/litemall/admin/web/AdminHistoryController.java) | 搜索历史 |
| [AdminFeedbackController](file:///workspace/litemall-admin-api/src/main/java/org/linlinjava/litemall/admin/web/AdminFeedbackController.java) | 意见反馈 |
| [AdminCommentController](file:///workspace/litemall-admin-api/src/main/java/org/linlinjava/litemall/admin/web/AdminCommentController.java) | 评论管理 |
| [AdminNoticeController](file:///workspace/litemall-admin-api/src/main/java/org/linlinjava/litemall/admin/web/AdminNoticeController.java) | 通知管理 |
| [AdminRegionController](file:///workspace/litemall-admin-api/src/main/java/org/linlinjava/litemall/admin/web/AdminRegionController.java) | 行政区域 |
| [AdminStorageController](file:///workspace/litemall-admin-api/src/main/java/org/linlinjava/litemall/admin/web/AdminStorageController.java) | 文件上传/存储管理 |
| [AdminConfigController](file:///workspace/litemall-admin-api/src/main/java/org/linlinjava/litemall/admin/web/AdminConfigController.java) | 系统配置管理 |
| [AdminStatController](file:///workspace/litemall-admin-api/src/main/java/org/linlinjava/litemall/admin/web/AdminStatController.java) | 统计报表 |
| [AdminDashbordController](file:///workspace/litemall-admin-api/src/main/java/org/linlinjava/litemall/admin/web/AdminDashbordController.java) | 仪表盘数据 |
| [AdminProfileController](file:///workspace/litemall-admin-api/src/main/java/org/linlinjava/litemall/admin/web/AdminProfileController.java) | 当前管理员资料/改密 |
| [AdminLogController](file:///workspace/litemall-admin-api/src/main/java/org/linlinjava/litemall/admin/web/AdminLogController.java) | 操作日志查询 |

- 权限模型：控制器方法使用 Shiro `@RequiresPermissions` 注解声明所需权限，并配套 `@RequiresPermissionsDesc` 提供菜单/按钮级描述。`PermissionUtil` 在运行时扫描 `org.linlinjava.litemall.admin` 包下所有 `@Controller`，收集权限码与对应 API 路径，供前端动态路由与按钮控制。

### 3.6 litemall-all / litemall-all-war（聚合部署模块）

- [litemall-all/pom.xml](file:///workspace/litemall-all/pom.xml)：`packaging=jar`，聚合 `litemall-core` + `litemall-db` + `litemall-wx-api` + `litemall-admin-api`。
  - 配置 `spring-boot-maven-plugin` 的 `repackage`，产出带 `-exec` classifier 的可执行 jar（`litemall-all-0.1.0-exec.jar`）。
  - 配置 `maven-resources-plugin`：在 `validate` 阶段把 `../litemall-admin/dist` 拷贝到 `target/classes/static`，把 `../litemall-vue/dist` 拷贝到 `target/classes/static/vue`，实现前端静态资源打进 jar。
- [litemall-all-war/pom.xml](file:///workspace/litemall-all-war/pom.xml)：`packaging=war`，`finalName=litemall`，依赖 `spring-boot-starter-tomcat`（`provided`），用于外置 Tomcat 部署；同样拷贝前端产物。
- [litemall-all-war/src/main/resources/application.yml](file:///workspace/litemall-all-war/src/main/resources/application.yml)：WAR 模式部署配置。
- 注：两个聚合模块本身无 Java 源码，仅做依赖聚合与资源拷贝；启动时由 `litemall-wx-api` 或 `litemall-admin-api` 的 `Application` 类作为入口（取决于部署形态）。

---

## 4. 前端模块详解

### 4.1 litemall-admin（管理后台前端）

- 路径：[litemall-admin](file:///workspace/litemall-admin)
- 技术栈：基于 [vue-element-admin 4.3.0](https://github.com/PanJiaChen/vue-element-admin)，Vue 2.6.10 + Element UI 2.13.2 + axios + vue-router + vuex + echarts + xlsx。
- 构建命令（[package.json](file:///workspace/litemall-admin/package.json)）：`npm run dev`（开发，端口 9527）、`npm run build:dep`/`build:prod`（构建）、`npm run lint`、`npm run test`。
- 构建配置 [vue.config.js](file:///workspace/litemall-admin/vue.config.js)：输出目录 `dist`，`@` 别名指向 `src`，`lintOnSave` 开启。
- 目录结构：
  - `src/api/`：按业务模块拆分的 API 调用封装（ad/admin/aftersale/brand/category/comment/config/coupon/dashboard/goods/groupon/issue/keyword/log/login/notice/order/profile/region/role/stat/storage/topic/user 等），底层走 [utils/request.js](file:///workspace/litemall-admin/src/utils/request.js)（axios 实例，注入 `X-Litemall-Admin-Token`，统一处理 501-506 业务错误）。
  - `src/router/index.js`：常量路由（login/404/401/redirect/dashboard）+ 异步路由（`user` 会员管理、`mall` 商城管理、`promotion` 推广、`sys` 系统管理、`config` 配置、`stat` 统计），每个路由节点带 `meta.permissions` 对接后端权限码。
  - `src/store/`：Vuex，挂载 `app/permission/tagsView/user` 模块，[getters.js](file:///workspace/litemall-admin/src/store/getters.js) 暴露 `token/avatar/name/roles/perms/permission_routes/addRoutes`。
  - `src/permission.js`：全局路由守卫，基于 token + 用户 perms 动态生成路由（`GenerateRoutes`），`router.addRoutes` 注入，无权限跳 401。
  - `src/utils/auth.js`：基于 `js-cookie` 的 token 读写（键名 `X-Litemall-Admin-Token`）。
  - `src/views/`：页面组件，按业务域分目录（login/dashboard/mall/sys/user/promotion/config/stat 等）。
  - `src/filters/`、`src/icons/`、`src/styles/`、`src/vendor/Export2Excel.js`：过滤器、SVG 图标、样式、Excel 导出。
- 环境变量：`.env.development` / `.env.production` / `.env.deployment` 配置 `VUE_APP_BASE_API`。
- 认证流程：登录调用 `/admin/auth/login` 获取 token（Shiro sessionId）→ 写入 cookie → 路由守卫拉取 `/admin/auth/info` 获取 roles/perms → 动态生成路由 → 跳转 dashboard。

### 4.2 litemall-vue（轻商城移动端）

- 路径：[litemall-vue](file:///workspace/litemall-vue)
- 技术栈：Vue 2.5 + Vant 2.0 + vue-router + vuex + axios。
- 构建命令（[package.json](file:///workspace/litemall-vue/package.json)）：`npm run dev`（开发，端口 6255）、`npm run build`、`npm run lint`。
- 构建配置 [vue.config.js](file:///workspace/litemall-vue/vue.config.js)：输出 `dist`，prefetch/preload 优化，路由懒加载，SCSS 全局变量注入。
- 目录结构：
  - `src/api/api.js`：集中导出所有接口（首页/分类/认证/商品/品牌/购物车/订单等）。
  - `src/router/`：拆分为 `home/items/user/order/login` 子路由，[index.js](file:///workspace/litemall-vue/src/router/index.js) 合并并设全局前置守卫（未登录跳登录、header 状态同步）。
  - `src/store/`：Vuex strict 模式，核心状态 `showHeader`，[mutations.js](file:///workspace/litemall-vue/src/store/mutations.js) + [mutation-types.js](file:///workspace/litemall-vue/src/store/mutation-types.js)。
  - `src/utils/request.js`：axios 封装，从 localStorage 读 Authorization 注入 `X-Litemall-Token`，统一处理登录失效与业务错误。
  - `src/utils/auth.js`：localStorage 读写 token。
  - `src/views/`：移动端页面（home 首页、items 商品、user 用户中心、order 订单、login 登录注册 等）。
  - `src/mixin/scroll-fixed.js`、`src/filter/index.js`：滚动固定 mixin 与全局过滤器。

### 4.3 litemall-wx（微信小程序）

- 路径：[litemall-wx](file:///workspace/litemall-wx)
- 定位：测试小商城小程序，UI/功能较完整，含售后、优惠券、反馈等全部能力。
- 入口 [app.js](file:///workspace/litemall-wx/app.js)：`onShow` 时 `user.checkLogin()` 更新 `globalData.hasLogin`。
- 配置 [app.json](file:///workspace/litemall-wx/app.json)：注册 pages、tabBar、window 样式、网络超时、debug，引入 `vant-weapp` 组件库与 `wxParse`（富文本解析）。
- 关键文件：
  - [config/api.js](file:///workspace/litemall-wx/config/api.js)：API 根地址与接口路径常量（微信登录、商品、购物车、订单、售后、团购、优惠券等）。
  - [utils/util.js](file:///workspace/litemall-wx/utils/util.js)：`wx.request` 封装，注入 `X-Litemall-Token`，处理 501 登录失效跳登录页。
  - [utils/user.js](file:///workspace/litemall-wx/utils/user.js)：微信登录流程、token/userInfo 本地存储、`checkLogin()` 判断登录态。
  - `utils/area.js`、`utils/check.js`：省市区数据、表单校验。
- 页面（`pages/`）：首页、分类、商品详情、购物车、结算、订单列表/详情、用户中心、登录注册、评论、专题、团购、优惠券、售后、地址、收藏、足迹、反馈、搜索、品牌等。
- 组件库 `lib/vant-weapp/`：action-sheet、card、collapse、datetime-picker、dialog、dropdown-menu、image、notice-bar、stepper、swipe-cell、tabbar、toast、transition、tree-select 等组件。

### 4.4 renard-wx（真实小商场小程序）

- 路径：[renard-wx](file:///workspace/renard-wx)
- 定位：独立 UI 风格的真实小商场小程序，页面与接口相对精简。
- 与 `litemall-wx` 的差异：
  - 自有 `components/`：capsule、good、goodList、icon、notice-bar、notify、sharePop 等自定义组件。
  - `config/api.js` 在 `litemall-wx` 基础上调整接口（如新增 `CatalogAll`，未包含部分售后/优惠券/反馈接口）。
  - 页面集合更精简（brand/brandDetail/cart/catalog/category/commentPost/goods/hotGoods/index/newGoods/payResult/search/topic/topicDetail）。
  - tabBar 图标、窗口标题、下拉刷新等配置不同。
- 共享 `lib/wxParse` 富文本解析库。

---

## 5. 关键类与函数说明

### 5.1 核心工具与配置类

| 类 | 路径 | 说明 |
|----|------|------|
| `ResponseUtil` | [core/util/ResponseUtil.java](file:///workspace/litemall-core/src/main/java/org/linlinjava/litemall/core/util/ResponseUtil.java) | 统一响应封装 `{errno,errmsg,data}`，所有控制器返回值均经此封装 |
| `SystemConfig` | [core/system/SystemConfig.java](file:///workspace/litemall-core/src/main/java/org/linlinjava/litemall/core/system/SystemConfig.java) | 系统业务配置静态访问器，配置 key 常量化，值内存缓存，启动时由 `SystemInistService` 载入 |
| `StorageService` | [core/storage/StorageService.java](file:///workspace/litemall-core/src/main/java/org/linlinjava/litemall/core/storage/StorageService.java) | 文件存储统一入口，`store()` 写入底层存储并落库 `litemall_storage` |
| `NotifyService` | [core/notify/NotifyService.java](file:///workspace/litemall-core/src/main/java/org/linlinjava/litemall/core/notify/NotifyService.java) | 邮件/短信通知，`@Async` 异步发送，支持模板 |
| `TaskService` | [core/task/TaskService.java](file:///workspace/litemall-core/src/main/java/org/linlinjava/litemall/core/task/TaskService.java) | `DelayQueue` 延时任务调度，订单超时取消等 |
| `CorsConfig` | [core/config/CorsConfig.java](file:///workspace/litemall-core/src/main/java/org/linlinjava/litemall/core/config/CorsConfig.java) | 全局跨域过滤器 |
| `WxConfig`/`WxProperties` | [core/config](file:///workspace/litemall-core/src/main/java/org/linlinjava/litemall/core/config/WxProperties.java) | 微信小程序/支付配置绑定 |
| `BCryptPasswordEncoder` | [core/util/bcrypt](file:///workspace/litemall-core/src/main/java/org/linlinjava/litemall/core/util/bcrypt/BCryptPasswordEncoder.java) | 管理员密码 BCrypt 加密/校验 |
| `JacksonUtil` | [core/util/JacksonUtil.java](file:///workspace/litemall-core/src/main/java/org/linlinjava/litemall/core/util/JacksonUtil.java) | JSON 解析工具，管理端控制器常以 `@RequestBody String body` + JacksonUtil 解析入参 |
| `IpUtil` | [core/util/IpUtil.java](file:///workspace/litemall-core/src/main/java/org/linlinjava/litemall/core/util/IpUtil.java) | 获取客户端真实 IP |
| `GlobalExceptionHandler` | [core/config/GlobalExceptionHandler.java](file:///workspace/litemall-core/src/main/java/org/linlinjava/litemall/core/config/GlobalExceptionHandler.java) | 全局异常处理，统一转 ResponseUtil 响应 |

### 5.2 认证与权限机制

**小商城端（JWT）**

- [JwtHelper](file:///workspace/litemall-wx-api/src/main/java/org/linlinjava/litemall/wx/util/JwtHelper.java)：HS256 签名，密钥 `X-Litemall-Token`，签发者 `LITEMALL`，token 有效期 2 小时。
  - `createToken(userId)`：生成 token，payload 含 `userId` claim。
  - `verifyTokenAndGetUserId(token)`：校验签名并提取 userId，失败返回 0。
- `UserTokenManager`：token 生成入口，`generateToken(userId)` 委托 `JwtHelper`。
- [@LoginUser](file:///workspace/litemall-wx-api/src/main/java/org/linlinjava/litemall/wx/annotation/LoginUser.java)：参数注解，标注在控制器方法参数 `Integer userId` 上。
- [LoginUserHandlerMethodArgumentResolver](file:///workspace/litemall-wx-api/src/main/java/org/linlinjava/litemall/wx/annotation/support/LoginUserHandlerMethodArgumentResolver.java)：参数解析器，从请求头 `X-Litemall-Token` 读 JWT，解析为 userId 注入；未登录时 userId=0。
- [WxWebMvcConfiguration](file:///workspace/litemall-wx-api/src/main/java/org/linlinjava/litemall/wx/config/WxWebMvcConfiguration.java)：注册上述参数解析器。
- 登录入口 [WxAuthController](file:///workspace/litemall-wx-api/src/main/java/org/linlinjava/litemall/wx/web/WxAuthController.java)：
  - 账号登录：校验用户名/密码 → 更新登录时间与 IP → 生成 JWT token 返回。
  - 微信登录：`WxMaService` 获取 session/openId → 创建/更新用户 → 写 sessionKey → 生成 token。

**管理后台端（Shiro Session + RBAC）**

- [ShiroConfig](file:///workspace/litemall-admin-api/src/main/java/org/linlinjava/litemall/admin/config/ShiroConfig.java)：定义过滤链 `/admin/auth/login|401|index|403` 与 `/admin/index/*` 为 `anon`，其余 `/admin/**` 需 `authc`；登录页 `/admin/auth/401`，未授权页 `/admin/auth/403`。
- [AdminAuthorizingRealm](file:///workspace/litemall-admin-api/src/main/java/org/linlinjava/litemall/admin/shiro/AdminAuthorizingRealm.java)：
  - `doGetAuthenticationInfo`：用 `UsernamePasswordToken` 取账号，`BCryptPasswordEncoder.matches` 校验密码，返回 `SimpleAuthenticationInfo(admin, password, name)`。
  - `doGetAuthorizationInfo`：根据 admin 的 `roleIds` 查询角色集合与权限集合，写入 `SimpleAuthorizationInfo`。
- `AdminWebSessionManager`：自定义 Session 管理，从请求头 `X-Litemall-Admin-Token` 取 sessionId。
- [AdminAuthController](file:///workspace/litemall-admin-api/src/main/java/org/linlinjava/litemall/admin/web/AdminAuthController.java)：
  - `login`：`Subject.login(UsernamePasswordToken)` 触发 Realm 认证，成功后更新登录信息，返回 `{token: sessionId, adminInfo}`。
  - `info`：返回当前管理员 `name/avatar/roles/perms`，`perms` 经 `toApi()` 转换为前端可用的 API 形式权限（通过 `PermissionUtil` 扫描 `@RequiresPermissions` + `@RequiresPermissionsDesc` 生成 perm→api 映射）。
- [@RequiresPermissionsDesc](file:///workspace/litemall-admin-api/src/main/java/org/linlinjava/litemall/admin/annotation/RequiresPermissionsDesc.java)：自定义注解，为接口绑定菜单/按钮级权限描述。
- [PermissionUtil](file:///workspace/litemall-admin-api/src/main/java/org/linlinjava/litemall/admin/util/PermissionUtil.java)：运行时扫描 `org.linlinjava.litemall.admin` 包下 `@Controller`，读取 `@RequiresPermissions` 与 `@RequiresPermissionsDesc`，生成权限描述与 API 路径列表，支撑前端动态路由与按钮权限。

### 5.3 数据访问层关键类

- `Litemall*Mapper`（[dao 目录](file:///workspace/litemall-db/src/main/java/org/linlinjava/litemall/db/dao)）：MyBatis Generator 生成的 Mapper 接口，标准方法 `countByExample/deleteByExample/insert/selectByExample/selectOneByExample/updateByExample` 等。
- `Litemall*Example`（[domain 目录](file:///workspace/litemall-db/src/main/java/org/linlinjava/litemall/db/domain)）：QBE 查询条件对象，通过 `createCriteria()` 链式构造 WHERE 条件。
- `Litemall*Service`（[service 目录](file:///workspace/litemall-db/src/main/java/org/linlinjava/litemall/db/service)）：业务数据服务，注入对应 Mapper，封装 Example 查询与 PageHelper 分页。详见 [3.3 节](#33-litemall-db数据访问模块)。

### 5.4 业务服务层关键类

- [WxOrderService](file:///workspace/litemall-wx-api/src/main/java/org/linlinjava/litemall/wx/service/WxOrderService.java)：小商城订单核心，定义订单状态常量（101 待付款、201 待发货、301 待收货、401 已完成 等）与用户可操作状态集合，处理下单/取消/支付/退款/发货/收货全流程。
- [AdminGoodsService](file:///workspace/litemall-admin-api/src/main/java/org/linlinjava/litemall/admin/service/AdminGoodsService.java)：管理端商品列表查询，注入数据库 service。
- [AdminOrderService](file:///workspace/litemall-admin-api/src/main/java/org/linlinjava/litemall/admin/service/AdminOrderService.java)：管理端订单业务（详情/退款/发货/回复/删除）。
- [LogHelper](file:///workspace/litemall-admin-api/src/main/java/org/linlinjava/litemall/admin/service/LogHelper.java)：管理端操作日志记录（登录成功/失败、通用操作日志）。
- `HomeCacheManager`（[wx/service](file:///workspace/litemall-wx-api/src/main/java/org/linlinjava/litemall/wx/service/HomeCacheManager.java)）：首页数据缓存。
- `CaptchaCodeManager`（[wx/service](file:///workspace/litemall-wx-api/src/main/java/org/linlinjava/litemall/wx/service/CaptchaCodeManager.java)）：注册验证码缓存与超时校验。

---

## 6. 数据库设计

数据库 `litemall`，初始化脚本位于 [litemall-db/sql](file:///workspace/litemall-db/sql)：

1. `litemall_schema.sql` — 建库 `litemall`、建用户 `litemall` / 密码 `litemall123456`、授权。
2. `litemall_table.sql` — 建表（含 `DROP TABLE IF EXISTS` + `CREATE TABLE`，UTF8 编码，InnoDB 引擎）。
3. `litemall_data.sql` — 测试数据（含默认超级管理员 `admin123`/`admin123`）。

主要业务表（与 domain 实体一一对应）：

| 领域 | 表 |
|------|-----|
| 用户与权限 | `litemall_user`、`litemall_admin`、`litemall_role`、`litemall_permission` |
| 商品 | `litemall_goods`、`litemall_goods_product`（规格商品）、`litemall_goods_attribute`、`litemall_goods_specification`、`litemall_brand`、`litemall_category` |
| 订单与购物车 | `litemall_order`、`litemall_order_goods`、`litemall_cart`、`litemall_aftersale` |
| 营销推广 | `litemall_coupon`、`litemall_coupon_user`、`litemall_groupon`、`litemall_groupon_rules`、`litemall_topic`、`litemall_ad`、`litemall_keyword` |
| 用户行为 | `litemall_address`、`litemall_collect`、`litemall_footprint`、`litemall_comment`、`litemall_feedback`、`litemall_search_history` |
| 通用 | `litemall_region`、`litemall_storage`、`litemall_issue`、`litemall_notice`、`litemall_notice_admin`、`litemall_log`、`litemall_system` |

- `litemall_order` 含数组字段（specifications/comments），通过 MyBatis `typeHandler` 序列化为 JSON。
- `litemall_system` 表存储系统业务配置，启动时由 `SystemInistService` 全量载入 `SystemConfig` 内存 Map。

---

## 7. 错误码与响应规范

所有接口统一返回 `{errno, errmsg, data}`（由 [ResponseUtil](file:///workspace/litemall-core/src/main/java/org/linlinjava/litemall/core/util/ResponseUtil.java) 封装）：

| 错误码 | 含义 | ResponseUtil 方法 |
|--------|------|-------------------|
| 0 | 成功 | `ok()` / `ok(data)` / `okList(list)` |
| 401 | 参数错误（缺参数） | `badArgument()` |
| 402 | 参数值错误 | `badArgumentValue()` |
| 501 | 验证失败/未登录 | `unlogin()` |
| 502 | 系统内部错误 | `serious()` |
| 503 | 业务不支持 | `unsupport()` |
| 504 | 更新数据失效（乐观锁） | `updatedDateExpired()` |
| 505 | 更新数据失败 | `updatedDataFailed()` |
| 506 | 无操作权限 | `unauthz()` |
| 6xx | 管理后台业务错误码 | 见 `litemall-admin-api` 的 `AdminResponseCode` |
| 7xx | 小商城业务错误码 | 见 `litemall-wx-api` 的 `WxResponseCode` |

分页响应额外字段：`total`（总数）、`page`（当前页）、`limit`（每页数）、`pages`（总页数）。

---

## 8. 项目运行方式

### 8.1 开发环境要求

- [MySQL](https://dev.mysql.com/downloads/mysql/)（推荐 5.7）
- [JDK 1.8 或以上](http://www.oracle.com/technetwork/java/javase/overview/index.html)
- [Maven](https://maven.apache.org/download.cgi)
- [Node.js](https://nodejs.org/en/download/)
- [微信开发者工具](https://developers.weixin.qq.com/miniprogram/dev/devtools/download.html)（仅小程序前端需要）

### 8.2 数据库初始化

依次导入 SQL（顺序不可颠倒）：

```bash
mysql -h localhost -u root -p < litemall-db/sql/litemall_schema.sql
mysql -h localhost -u root -p litemall < litemall-db/sql/litemall_table.sql
mysql -h localhost -u root -p litemall < litemall-db/sql/litemall_data.sql
```

默认数据库连接配置（[deploy/litemall/application.yml](file:///workspace/deploy/litemall/application.yml)）：
- URL：`jdbc:mysql://localhost:3306/litemall?...&useSSL=false`
- 用户：`litemall` / 密码：`litemall123456`

### 8.3 后端服务启动

```bash
cd litemall
mvn install
mvn clean package
java -Dfile.encoding=UTF-8 -jar litemall-all/target/litemall-all-0.1.0-exec.jar
```

启动后服务监听 `8080`，同时提供 `/wx/**` 与 `/admin/**` 两套接口。验证：

- 小商城接口：`http://localhost:8080/wx/index/index`
- 管理后台接口：`http://localhost:8080/admin/index/index`

> 单独开发某 API 模块时，可分别运行 `litemall-wx-api`（端口 8082）或 `litemall-admin-api`（端口 8083）的 `Application` 主类。

### 8.4 管理后台前端启动

```bash
npm install -g cnpm --registry=https://registry.npm.taobao.org
cd litemall/litemall-admin
cnpm install
cnpm run dev
```

浏览器访问 `http://localhost:9527`，默认管理员 `admin123` / `admin123`。

### 8.5 小程序前端启动

支持两套小程序（`litemall-wx`、`renard-wx`），分别导入测试：

1. 微信开发者工具导入对应项目目录。
2. 项目配置勾选「不校验合法域名、web-view（业务域名）、TLS 版本以及 HTTPS 证书」。
3. 点击「编译」预览；或点击「预览」生成二维码手机扫码（手机需开启调试）。

> 微信登录、微信支付等功能需开发者自行配置 AppID/Secret/商户号后方可完整运行。

### 8.6 轻商城前端启动

```bash
npm install -g cnpm --registry=https://registry.npm.taobao.org
cd litemall/litemall-vue
cnpm install
cnpm run dev
```

浏览器（建议 Chrome 手机模式）访问 `http://localhost:6255`。

### 8.7 传统服务器部署

参考 [deploy/README.md](file:///workspace/deploy/README.md)，部署包结构：

- `bin/`：远程服务器脚本（`deploy.sh` 部署、`reset.sh` 重置数据+部署）
- `db/`：`litemall.sql` 合并脚本（schema+table+data）
- `litemall/`：`litemall.jar` 可执行包 + 外部 `application.yml` 覆盖配置
- `util/`：本地开发机脚本（`package.sh` 打包、`lazy.sh` 一键打包+上传+部署）

打包流程：

```bash
cd litemall
cat ./litemall-db/sql/litemall_schema.sql > ./deploy/db/litemall.sql
cat ./litemall-db/sql/litemall_table.sql >> ./deploy/db/litemall.sql
cat ./litemall-db/sql/litemall_data.sql >> ./deploy/db/litemall.sql
cd ./litemall-admin && cnpm install && cnpm run build:dep && cd ..
mvn clean package
cp -f ./litemall-all/target/litemall-all-*-exec.jar ./deploy/litemall/litemall.jar
```

部署流程（远程服务器已装 MySQL + JDK1.8）：

```bash
# 导入数据库
mysql -h localhost -u $ROOT -p$PASSWORD < litemall.sql
# 启动服务（基于 spring-boot-maven-plugin 的 executable jar，可注册为 service）
sudo service litemall stop
sudo ln -f -s /home/ubuntu/deploy/litemall/litemall.jar /etc/init.d/litemall
sudo service litemall start
```

一键部署（本地执行 `lazy.sh`，需先在脚本中配置远程账号与密钥）：

```bash
cd litemall
./deploy/util/lazy.sh
```

### 8.8 Docker 部署

参考 [docker/docker-compose.yml](file:///workspace/docker/docker-compose.yml)，编排两个服务：

- `mysql57`：MySQL 5.7，端口 3306，挂载 `db/conf.d`、`db/data`、`db/init-sql`，root 密码 `litemall123456`。
- `litemall`：基于 `litemall/Dockerfile` 构建，端口 8080，挂载 `storage`、`logs`、`backup`，时区 `Asia/Shanghai`，依赖 `mysql57`。

---

## 9. 配置体系

主配置文件 [deploy/litemall/application.yml](file:///workspace/deploy/litemall/application.yml)（部署时覆盖 jar 内默认配置）：

| 配置段 | 作用 |
|--------|------|
| `spring.datasource.druid` | Druid 连接池（initial-size 10、max-active 50、validation-query、stat/wall 过滤器） |
| `litemall.wx` | 微信小程序 AppID/Secret、商户号/Key、支付回调 URL、证书路径 |
| `litemall.notify.mail` | 邮件通知（host/username/password/sendfrom/sendto/port，enable 开关） |
| `litemall.notify.sms` | 短信通知（active: tencent/aliyun 切换，sign，template 列表 paySucceed/captcha/ship/refund） |
| `litemall.express` | 快鸟物流查询（appId/appKey，快递公司 vendors 映射） |
| `litemall.storage` | 对象存储（active: local/aliyun/tencent/qiniu 切换，各厂商 endpoint/key/secret/bucket） |
| `swagger.production` | Swagger 是否生产模式（隐藏文档） |

Profile 分层：
- `db` profile：数据源 + PageHelper 配置
- `core` profile：`litemall-core` 的存储/通知/物流/微信等配置
- `wx` profile：小商城专属（`litemall-wx-api` 激活 `db, core, wx`，端口 8082）
- `admin` profile：管理后台专属（`litemall-admin-api` 激活 `admin`，端口 8083）
- 部署态（`litemall-all`）：默认 `active: none`，由外部 `application.yml` 接管，端口 8080

---

> 本 Wiki 基于 litemall V1.7.0 源码自动梳理，覆盖项目架构、模块职责、关键类函数、依赖关系与运行方式。如需更详细的接口文档，可启动服务后访问 Swagger UI（`/swagger-ui.html` 或 `/doc.html`）。
