# TikTokAutoSparkWeb Python 后端 README

本项目后端基于 FastAPI + Selenium，实现抖音登录管理、好友消息发送、定时续火花任务等能力。

## 1. 功能概览

- 浏览器初始化与状态检测
- 抖音登录（扫码、手机号、Cookie）
- 好友列表获取、指定好友发消息
- 每日定时任务新增、修改、删除、查询
- 管理员登录鉴权、密码修改、退出登录

## 2. 运行环境

- Python 3.8+
- Microsoft Edge 浏览器（推荐最新版）
- Windows（当前项目在 Windows 环境下使用）

说明：
- 程序使用 webdriver-manager 自动下载 EdgeDriver。
- 首次运行可能会下载驱动，耗时取决于网络。

## 3. 依赖安装

项目根目录已提供依赖文件 [requirements.txt](requirements.txt)。

安装命令：

pip install -r requirements.txt

## 4. 启动方式

后端主文件：
- [main.py](main.py)

在项目根目录执行：

python main.py

启动时会依次提示：
- 是否显示浏览器窗口（回车默认无头模式）
- 监听端口（例如输入 9844）

启动成功后，服务地址示例：
- http://localhost:9844

## 5. 鉴权机制

除管理员登录接口外，大部分接口需要 Bearer Token。

1. 调用管理员登录接口获取 token
- GET /Api/Login/Admin?username=admin&password=123456

2. 在后续请求头中携带：
- Authorization: Bearer <token>

默认管理员账户：
- 用户名：admin
- 密码：123456

## 6. 接口分组

以下为主要接口说明（与 [main.py](main.py) 保持一致）。

### 6.1 系统与初始化

- GET /Home
- GET /Api/Init
- GET /Api/GetInit

### 6.2 登录相关

- POST /Api/login
  - Body 字段：cooke, gzip_flag
- GET /Api/Pnglogin
- GET /Api/GetLogin
- GET /Api/Cookie/Expiry
- GET /Api/login/Init/GetLoginPng
- GET /Api/login/Init/GetCooker?password=xxx
- GET /Api/DieLogin
- GET /Api/LoginPhone?areacode=86&phone=xxxxxxxxxxx
- GET /Api/LoginPhoneInput?code=xxxxxx
- GET /Api/LoginDebug

Cookie 有效期接口说明：
- 路径：GET /Api/Cookie/Expiry
- 作用：返回当前浏览器 Cookie 的统计信息、最早过期时间、剩余秒数/小时数、是否 24 小时内即将过期。
- 注意：该结果是基于浏览器中可读到的 Cookie expiry 字段推算，仅供参考，实际登录状态仍受平台会话与风控策略影响。

### 6.3 好友与消息

- GET /Api/GetFriendsList
- GET /Api/Send?name=好友名&text=消息内容
- GET /Api/GetUsername
- GET /Api/GetScrlk

### 6.4 定时任务

- GET /Time/add?time=22:00&name=好友名&text=可选消息
- GET /Time/del?task_id=任务ID
- GET /Time/edit?name=好友名&new_time=23:00
- GET /Time/getlist

说明：
- time 支持 9:5、09:05、9：05 等形式，后端会格式化为 HH:MM。
- 同一好友默认只允许一个定时任务。

### 6.5 管理员与安全

- GET /Api/Login/Admin?username=admin&password=123456
- GET /Api/GetLastLoginIP
- GET /Api/logout
- GET /Api/ChangePassword?old_password=旧密码&new_password=新密码

## 7. 常见问题

1. 初始化失败，提示驱动不兼容
- 更新 Edge 浏览器后重试。
- 清理 webdriver-manager 缓存后重新启动。

2. 登录后仍显示未登录
- 确认当前页面已完成抖音登录跳转。
- 重新调用 /Api/GetLogin 检查状态。

3. 定时任务未触发
- 先确认 /Api/Init 已完成且调度线程已启动。
- 检查系统时间与任务时间格式。

## 8. 开发建议

- 当前密码与 token 存储在内存中，重启后会重置。
- 生产环境建议将密码、token、任务配置迁移到数据库或持久化存储。
- 建议为关键接口增加操作日志与异常告警。
