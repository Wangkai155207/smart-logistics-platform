# 内贸物流平台 (http://10.20.120.22/) — 涉及代码文件清单

以下为该系统涉及的全部**源代码与配置**文件（不含依赖、构建产物、虚拟环境）。

---

## 一、后端 FastAPI（server/）

### 入口与根目录
| 文件 | 说明 |
|------|------|
| `server/requirements.txt` | Python 依赖 |
| `server/warehouse_config.json` | 仓库容量等配置 |
| `server/screenshot_worker.py` | 日报截图独立进程（调 dashboard_screenshot 并上传飞书） |
| `server/.env` | 环境变量（敏感，打包时可选排除） |

### app/ — 主应用
| 文件 | 说明 |
|------|------|
| `server/app/main.py` | FastAPI 入口、路由挂载 |
| `server/app/core/config.py` | 配置加载 |
| `server/app/core/auth.py` | 认证逻辑 |
| `server/app/core/security.py` | 密码、JWT |
| `server/app/db/session.py` | 数据库连接 |
| `server/app/api/auth.py` | 登录/登出 |
| `server/app/api/users.py` | 用户管理 |
| `server/app/api/dashboard.py` | 数据看板 API |
| `server/app/api/wms.py` | 库存/出入库 API |
| `server/app/api/wms_cookie.py` | WMS Cookie 管理 |
| `server/app/api/tms.py` | TMS 订单 API |
| `server/app/api/delay_review.py` | 延迟审核 API |
| `server/app/api/carrier_kpi.py` | 承运商 KPI API |
| `server/app/api/data_management.py` | 数据管理（导入导出等） |
| `server/app/api/report_recipient.py` | 报表接收人 API |
| `server/app/models/user.py` | 用户模型 |
| `server/app/models/inventory.py` | 库存模型 |
| `server/app/models/inbound.py` | 入库模型 |
| `server/app/models/outbound.py` | 出库模型 |
| `server/app/models/order.py` | 订单模型 |
| `server/app/models/wms_cookie.py` | WMS Cookie 模型 |
| `server/app/models/tms_cookie.py` | TMS Cookie 模型 |
| `server/app/models/delay_review.py` | 延迟审核模型 |
| `server/app/models/report_recipient.py` | 报表接收人模型 |
| `server/app/models/requirement.py` | 需求模型 |
| `server/app/crud/users.py` | 用户 CRUD |
| `server/app/crud/inventory.py` | 库存 CRUD |
| `server/app/crud/inbound.py` | 入库 CRUD |
| `server/app/crud/outbound.py` | 出库 CRUD |
| `server/app/crud/order.py` | 订单 CRUD |
| `server/app/crud/wms_cookie.py` | WMS Cookie CRUD |
| `server/app/crud/report_recipient.py` | 报表接收人 CRUD |
| `server/app/crud/requirement.py` | 需求 CRUD |
| `server/app/schemas/auth.py` | 认证相关 Schema |
| `server/app/schemas/user.py` | 用户 Schema |
| `server/app/schemas/wms_cookie.py` | WMS Cookie Schema |
| `server/app/schemas/report_recipient.py` | 报表接收人 Schema |
| `server/app/services/scheduler.py` | 定时任务（同步、日报、需求汇总等） |
| `server/app/services/dashboard_screenshot.py` | 看板截图（Selenium 多帧 GIF） |
| `server/app/services/dashboard_report.py` | 日报汇总与发送逻辑 |
| `server/app/services/feishu_bot.py` | 飞书机器人事件与卡片回调 |
| `server/app/services/wms_auth.py` | WMS 登录与 Cookie |
| `server/app/services/wms_client.py` | WMS 接口调用 |
| `server/app/services/tms_client.py` | TMS 接口调用 |
| `server/app/services/tms_auth.py` | TMS 登录 |
| `server/app/services/excel_service.py` | Excel 导入导出 |
| `server/app/services/requirement_service.py` | 需求业务 |
| `server/app/services/requirement_test_service.py` | 需求测试 |

说明：`server/app/` 下若有 `__init__.py` 等，一并计入「后端」；迁移/测试脚本（如 `migrate_orders.py`、`check_db.py`）属辅助，可按需打包。

---

## 二、前端 Vue（web/）

### 根配置
| 文件 | 说明 |
|------|------|
| `web/package.json` | 依赖与脚本 |
| `web/vite.config.ts` | Vite 配置 |
| `web/tsconfig.json` | TypeScript 配置 |
| `web/tsconfig.app.json` | 应用 TS 配置 |
| `web/tsconfig.node.json` | Node 脚本 TS 配置 |
| `web/index.html` | 入口 HTML |

### 源码 src/
| 文件 | 说明 |
|------|------|
| `web/src/main.ts` | 前端入口 |
| `web/src/App.vue` | 根组件 |
| `web/src/index.html` | （若存在） |
| `web/src/router/index.ts` | 路由 |
| `web/src/services/http.ts` | HTTP 封装 |
| `web/src/stores/auth.ts` | 登录状态 |
| `web/src/stores/theme.ts` | 主题 |
| `web/src/views/LoginView.vue` | 登录页 |
| `web/src/views/DashboardView.vue` | 概览 |
| `web/src/views/DataBoardView.vue` | 数据看板 |
| `web/src/views/InventoryView.vue` | 库存 |
| `web/src/views/InboundView.vue` | 入库 |
| `web/src/views/OutboundView.vue` | 出库 |
| `web/src/views/TmsOrderView.vue` | TMS 订单 |
| `web/src/views/CarrierKpiView.vue` | 承运商 KPI |
| `web/src/views/DelayReviewView.vue` | 延迟审核 |
| `web/src/views/PriceQueryView.vue` | 价格查询（iframe /quote） |
| `web/src/views/WarehouseQueryView.vue` | 仓库查询（iframe /warehouse） |
| `web/src/views/MonitorView.vue` | 监控入口 |
| `web/src/views/UsersView.vue` | 用户管理 |
| `web/src/views/ReportRecipientsView.vue` | 报表接收人 |
| `web/src/views/SettingsView.vue` | 系统设置 |
| `web/src/layouts/AdminLayout.vue` | 后台布局 |
| `web/src/assets/theme.css` | 主题样式 |
| `web/src/assets/main.css` | 全局样式 |
| `web/src/assets/base.css` | 基础样式 |
| `web/src/components/*.vue` | 各通用组件 |
| `web/public/maps/*.json` | 地图数据（按需打包） |

说明：`web/node_modules`、`web/dist` 不列入源码；打包部署时用 `npm run build` 生成 `dist`。

---

## 三、部署与脚本（deploy/）

| 文件 | 说明 |
|------|------|
| `deploy/linux/nginx_bt.conf` | Nginx 站点配置（宝塔） |
| `deploy/linux/zd01-backend.service` | systemd 主后端 |
| `deploy/linux/zd01-flask.service` | systemd Flask:5000 |
| `deploy/linux/fix_all.sh` | 一键修复（Nginx/防火墙/服务） |
| `deploy/linux/restart_all.sh` | 重启所有服务 |
| `deploy/linux/stop_all.sh` | 停止所有服务 |
| `deploy/linux/status.sh` | 状态查看 |
| `deploy/linux/auto_deploy.sh` | 自动部署（Git 拉取+重启） |
| `deploy/linux/setup.sh` | 首次环境准备 |
| `deploy/linux/verify.sh` | 验证脚本 |
| `deploy/linux/DEPLOY_GUIDE.md` | 部署说明 |
| `deploy/rescue.bat` | Windows 一键远程修复 |
| `deploy/deploy.bat` | 发布到服务器 |
| `deploy/restart_all.bat` | 本地重启所有服务 |
| `deploy/stop_all.bat` | 本地停止 |
| `deploy/start_all.bat` | 本地启动 |
| `deploy/start_backend.bat` | 本地启动后端 |
| `deploy/start_caddy.bat` | 本地 Caddy |
| `deploy/Caddyfile` | 本地 Caddy 配置 |
| `deploy/nginx.conf` | 本地 Nginx 配置（若用） |

---

## 四、项目级文档

| 文件 | 说明 |
|------|------|
| `README.md` | 产品说明与使用 |
| `PROJECT.md` | 项目总览（与系统一致） |
| `FILES_MANIFEST.md` | 本清单 |

---

## 五、打包说明

- **仅源码打包**：只打包上表所列路径，排除 `server/.venv`、`server/data/*.db`、`server/__pycache__`、`web/node_modules`、`web/dist`、`*.log`；`.env` 建议单独备份、勿提交仓库。
- **一键打包**：在项目根目录执行 `deploy\pack_source.bat`，会在当前目录生成 `zd01_source_yyyyMMdd.zip`（仅源码+配置，不含依赖与数据）。

上述文件即 http://10.20.120.22/ 该系统涉及的全部代码与配置；Flask 报价/仓库应用（flask_app）在服务器上单独部署，若需一并归档请单独拷贝该目录。
