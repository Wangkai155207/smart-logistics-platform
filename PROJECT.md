# 内贸物流平台 - 项目总览文档

> 本文档与当前系统保持一致，涵盖功能、架构、部署、配置与运维。  
> 集仓储管理、物流追踪、承运商考核、报价系统、数据分析于一体的综合性物流管理系统。

**版本：** v0.1  
**文档更新：** 与线上部署一致（Nginx、Selenium 截图、Flask 报价/仓库、飞书回调等）

---

## 目录

- [一、系统概述](#一系统概述)
- [二、核心功能模块](#二核心功能模块)
- [三、技术架构与目录结构](#三技术架构与目录结构)
- [四、服务与端口](#四服务与端口)
- [五、生产环境部署（Linux + 宝塔）](#五生产环境部署linux--宝塔)
- [六、本地开发与发布](#六本地开发与发布)
- [七、飞书集成](#七飞书集成)
- [八、数据与数据库](#八数据与数据库)
- [九、运维与故障排查](#九运维与故障排查)
- [十、常见问题](#十常见问题)

---

## 一、系统概述

**内贸物流平台（zd01）** 覆盖阳逻仓、观澜仓、东莞仓三大仓库全链路业务，提供统一入口的数据看板、库存/出入库、TMS 订单、承运商 KPI、延迟审核、报价查询、日报推送与飞书机器人等能力。

### 主要特性

- 多仓库管理：统一管理三仓库存、出入库
- TMS 订单追踪：自动同步、全链路查询
- 数据可视化：数据看板、图表、趋势
- 承运商 KPI：自动计算、等级与报表
- 延迟审核：提货/送达/回单延迟原因与豁免
- 每日 9:15 日报：自动截图看板并推送飞书（四份：总览 + 三仓）
- 飞书机器人：报价查询、仓库查询、帮助等（需公网回调）
- 报价系统：价格查询、仓库查询、Excel 价格本上传（Flask）

### 访问地址

| 环境     | 地址 |
|----------|------|
| 生产内网 | http://10.20.120.22/ |
| 生产公网 | http://221.233.151.202/（需 IT 放行 80 端口） |
| 本地开发 | http://127.0.0.1/ 或 Caddy/nginx 代理 |

---

## 二、核心功能模块

| 模块           | 说明 |
|----------------|------|
| 概览           | 首页快捷入口、在线用户、系统状态 |
| 数据看板       | 多仓数据可视化、地图、趋势、环比；日报截图来源 |
| 物流管理       | 阳逻/观澜/东莞 库存、出库、入库、Excel 导入导出 |
| TMS 订单       | 订单查询、筛选、同步 |
| 承运商 KPI     | 月度 KPI、得分等级、导出 |
| 延迟审核       | 提货/送达/回单延迟、原因填写、合格/不合格 |
| 报价系统       | 价格查询（Flask iframe）、仓库查询（Flask iframe）、更新价格本（Excel 上传） |
| 专车运输询价   | 询价订单、承运商管理（Flask:5002） |
| 阳逻仓装卸管理 | 装卸作业（Flask:5001） |
| 监控查询       | 海康监控代理入口 |
| 系统管理       | 用户、报表接收人、WMS/TMS Cookie、仓库配置 |

---

## 三、技术架构与目录结构

### 技术栈

| 层级     | 技术选型 | 说明 |
|----------|----------|------|
| 前端     | Vue 3 + TypeScript + Element Plus | 暗色主题、响应式 |
| 主后端   | FastAPI + SQLAlchemy + SQLite | 用户、看板、订单、KPI、飞书、定时任务 |
| 报价/仓库 | Flask (5000) | 价格查询、仓库查询、价格本导入；iframe 嵌入 |
| 装卸     | Flask (5001) | 阳逻仓装卸 |
| 专车询价 | Flask (5002) | 专车运输询价 |
| 反向代理 | Nginx（生产）/ Caddy（本地） | 统一入口、路由转发 |
| 定时任务 | APScheduler | 同步、日报、需求汇总等 |
| 日报截图 | Selenium + Chromium | 无头浏览器截看板并合成 GIF（生产环境 CentOS 7 兼容） |
| 通知     | 飞书 Open API | 日报推送、机器人回复、失败通知 |

### 项目目录结构

```
zd01/
├── server/                      # 主后端（FastAPI）
│   ├── app/
│   │   ├── api/                 # 接口路由
│   │   ├── core/                # 配置、安全
│   │   ├── crud/                # 数据库操作
│   │   ├── models/              # 数据模型
│   │   ├── schemas/             # 请求/响应模型
│   │   └── services/            # 业务逻辑（scheduler、feishu、dashboard_screenshot 等）
│   ├── data/                    # SQLite 数据目录
│   │   └── app.db               # 主库（用户、订单、库存、KPI、报表接收人等）
│   ├── .env                     # 环境变量（不提交）
│   ├── warehouse_config.json    # 仓库容量等配置
│   ├── requirements.txt
│   └── screenshot_worker.py     # 日报截图独立进程（调 services/dashboard_screenshot）
├── web/                         # 前端（Vue 3）
│   ├── src/
│   │   ├── views/               # 页面组件
│   │   ├── layouts/             # 布局
│   │   ├── router/              # 路由
│   │   └── services/            # HTTP 等
│   └── dist/                    # 构建产物（部署到服务器）
├── deploy/                      # 部署与脚本
│   ├── linux/                   # 生产环境
│   │   ├── nginx_bt.conf        # Nginx 站点配置（宝塔）
│   │   ├── zd01-backend.service # systemd 主后端
│   │   ├── zd01-flask.service   # systemd Flask:5000
│   │   ├── fix_all.sh           # 一键修复（Nginx/防火墙/服务）
│   │   ├── restart_all.sh
│   │   ├── status.sh
│   │   └── DEPLOY_GUIDE.md
│   ├── rescue.bat               # Windows 一键远程修复
│   ├── deploy.bat               # 发布到服务器（scp + 重启）
│   ├── restart_all.bat          # 本地重启所有服务
│   └── Caddyfile                # 本地 Caddy 代理
├── README.md                    # 产品说明与使用
└── PROJECT.md                   # 本文档（项目总览，与系统一致）
```

生产服务器上还有独立部署的 **Flask 应用**（报价/仓库/多维表等），路径一般为：`/www/wwwroot/zd01/flask_app/`，与 zd01 主仓同机。

---

## 四、服务与端口

| 服务        | 端口  | 说明 |
|-------------|-------|------|
| Nginx       | 80    | 统一入口；转发到各后端及静态 |
| FastAPI     | 8000  | 主后端（WMS 业务、飞书、定时任务） |
| Flask       | 5000  | 价格查询、仓库查询、价格本、多维表 |
| Flask       | 5001  | 阳逻仓装卸 |
| Flask       | 5002  | 专车运输询价 |
| 监控代理目标 | 172.19.1.80 | 仅 Nginx 反向代理，非本机 |

### Nginx 路由与后端对应关系（生产）

| 请求路径           | 转发到           |
|--------------------|------------------|
| `/`                | 静态 Vue (dist)  |
| `/api/*`           | FastAPI:8000（除下表） |
| `/api/pricing/*`   | Flask:5000       |
| `/api/warehouse/*` | Flask:5000       |
| `/feishu/*`        | FastAPI:8000     |
| `/quote`           | Flask:5000       |
| `/warehouse`       | Flask:5000       |
| `/yangluo-api/*`   | Flask:5001       |
| `/tq-api/*`        | Flask:5002       |
| `/tq-quote/`       | Flask:5002       |
| `/webhook/`        | Flask:5002       |
| `/monitor-proxy/*` | 172.19.1.80     |

---

## 五、生产环境部署（Linux + 宝塔）

### 服务器信息

- 内网 IP：10.20.120.22  
- 公网 IP：221.233.151.202  
- 系统：CentOS 7，宝塔面板  
- 项目路径：/www/wwwroot/zd01/  
- 宝塔：https://10.20.120.22:25474/

### 关键组件

- **Web 与 API**：Nginx 80 → Vue 静态 + FastAPI/Flask 反向代理（见上表）
- **日报截图**：Selenium + Chromium（EPEL）+ ChromeDriver；中文字体（文泉驿）、emoji 字体（Noto Emoji）已装
- **Flask 报价/仓库**：/quote、/warehouse 及 /api/pricing/*、/api/warehouse/* 已取消页面与接口的 @login_required，通过 Nginx 转发到 Flask:5000

### 一键修复（网站/日报异常时）

- 本机执行：`deploy\rescue.bat`（上传脚本 + 代码 + 执行 fix_all.sh）
- 或 SSH 到服务器执行：`bash /www/wwwroot/zd01/deploy/linux/fix_all.sh`

### 公网与飞书机器人

- 飞书回调需要公网能访问到 80 端口。若 221.233.151.202:80 未通，需 IT 将 **公网 80 端口转发到内网 10.20.120.22:80**，且 HTTP 直通（不拦截、不强制跳转）。
- 配置好后，在飞书开放平台将事件回调与卡片回调改为：
  - 事件：`http://221.233.151.202/feishu/event`
  - 卡片：`http://221.233.151.202/feishu/card_action`

---

## 六、本地开发与发布

### 本地启动

- 后端：`deploy\start_backend.bat` 或 `cd server && .venv\Scripts\activate && uvicorn app.main:app --reload --port 8000`
- 前端：`cd web && npm run dev`
- 代理：使用 `deploy\Caddyfile` 或 `deploy\nginx.conf` 将 80 转发到前端与 8000

### 发布到生产

- 快速发布后端：`deploy\deploy.bat` 或 `deploy\deploy.bat quick`
- 全量（后端+前端）：`deploy\deploy.bat all`
- 前端构建：`cd web && npm run build`，再将 `web/dist` 上传到服务器 `/www/wwwroot/zd01/web/dist`

---

## 七、飞书集成

- **日报**：每日 9:15 定时任务截数据看板（Selenium 多帧 GIF），上传飞书图片并推送卡片到配置的群/人；接收人可在系统管理-报表接收人中配置。
- **机器人**：接收事件与卡片回调（/feishu/event、/feishu/card_action），支持帮助、报价查询、仓库查询等；需公网 80 可达，并在开放平台配置上述回调地址与权限。

---

## 八、数据与数据库

- **主业务数据**：存于 **服务器** 的 SQLite：`/www/wwwroot/zd01/server/data/app.db`（用户、库存、订单、KPI、报表接收人等）。日常使用请访问 http://10.20.120.22/，数据即只在服务器上增长。
- **Flask 报价/仓库**：使用 flask_app 目录下的 SQLite/多维表等，与主库分离。
- **宝塔 MySQL**：当前 WMS 主系统未使用宝塔 MySQL；若需独占库，可单独新建库（如 zd01_wms）供将来扩展使用。

---

## 九、运维与故障排查

| 场景           | 建议操作 |
|----------------|----------|
| 网站打不开     | 检查 Nginx、fix_all.sh、restart_all.sh；看 /www/wwwlogs/zd01.*.log |
| 日报不发送/截图异常 | 检查 zd01-backend、Chromium/ChromeDriver、字体；看 screenshot_worker_*.log |
| 价格/仓库进不去 | 确认 iframe 使用相对路径 /quote、/warehouse；Nginx 已配置 /api/pricing/、/api/warehouse/ 转 Flask:5000 |
| 更新价格本失败 | 确认 Flask:5000 运行、Nginx 转发 /api/pricing/、client_max_body_size 足够；Flask 端已去掉 pricing 接口的 @login_required |
| 飞书机器人不回复 | 确认公网 80 已转发到内网 80，飞书开放平台回调地址为公网 http://221.233.151.202/feishu/... |

---

## 十、常见问题

**Q: 数据会不会都在我电脑上越存越大？**  
A: 正式环境数据在服务器的 app.db；日常用 http://10.20.120.22/ 即可，数据在服务器。本地仅开发时可能产生本地库，可不用或改为连接服务器库。

**Q: 价格查询/仓库查询显示错页或 404？**  
A: 前端 iframe 必须用相对路径 `/quote`、`/warehouse`；Nginx 需把 /quote、/warehouse 及 /api/pricing/、/api/warehouse/ 转到 Flask:5000，且 Flask 已去掉对应页面与接口的登录限制。

**Q: 飞书机器人能发日报但不回消息？**  
A: 发日报是服务器主动调飞书 API（出站）；回消息需要飞书回调到你的服务器（入站），必须公网 80 可达，请让 IT 做端口转发并配置回调地址。

---

本文档与当前系统一致，涵盖架构、部署、路由、数据与运维要点。功能细节与产品说明见 `README.md`。
