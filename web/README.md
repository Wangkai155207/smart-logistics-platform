# zd01-web

WMS内贸物流平台前端项目，基于 Vue 3 + TypeScript + Element Plus 构建。

## 功能模块

### 菜单结构

系统按仓库划分为三个一级菜单，每个仓库下有三个二级功能模块：

```
├── 概览
├── 阳逻仓
│   ├── 库存查询
│   ├── 出库数据
│   └── 入库数据
├── 观澜仓
│   ├── 库存查询
│   ├── 出库数据
│   └── 入库数据
├── 东莞仓
│   ├── 库存查询
│   ├── 出库数据
│   └── 入库数据
├── 账号管理
└── 系统设置
```

### 库存查询

路径：`/{仓库}/inv`（如 `/yangluo/inv`）

库存数据查询与展示页面，支持实时数据和历史快照两种模式：

**数据来源**
- **实时数据**：从 WMS 系统实时查询当前库存，查询结果自动保存到本地数据库
- **历史快照**：查看之前保存的历史库存数据，支持按快照日期筛选

**查询区域（固定在顶部）**
- 包含标题（显示对应仓库名称）和记录总数
- 数据来源切换（实时数据/历史快照）
- 快照日期选择器（仅在历史快照模式下显示）
- 支持按 SKU、库位等条件筛选
- 查询、重置、同步到本地按钮

**库存明细区域（独立滚动）**
- 表格支持横向滚动（左右滑动查看更多字段）
- 表格支持纵向滚动（上下滚动查看更多数据）
- 固定列：仓库名称、货主名称、物料信息固定在左侧，操作列固定在右侧
- 分页功能：支持切换每页显示条数
- 明细查看：双击行或点击"明细"按钮查看完整库存信息

**本地数据存储**
- 每次查询实时数据时自动保存到本地 SQLite 数据库
- 数据按快照日期分组存储，便于后续日库存分析
- 自动去重：相同数据（全部字段一致）不会重复插入

### 出库数据

路径：`/{仓库}/outbound`（如 `/yangluo/outbound`）

出库数据查询页面，支持实时 WMS 数据和本地数据两种模式：

**数据来源**
- **实时 WMS**：从 WMS 系统实时查询出库数据，查询结果自动保存到本地
- **本地数据**：查看已同步到本地的历史出库数据

**查询条件**
- 数据来源切换（实时WMS/本地数据）
- **发货日期范围**：选择开始日期和结束日期筛选
- 出库单号、物料编码筛选
- 同步到本地按钮（需先选择日期范围）

**字段信息**
- 仓库名称、货主、仓库代码
- 出库单号、出库单类型、出库单行号
- 发货时间
- 物料编码、物料名称
- 数量EA、批次、体积
- SAP库存地

**本地数据存储**
- 查询时自动保存到本地 SQLite 数据库
- 可手动点击"同步到本地"批量同步指定日期范围的数据
- 自动去重：相同数据不会重复插入

### 入库数据

路径：`/{仓库}/inbound`（如 `/yangluo/inbound`）

入库数据查询页面，支持实时 WMS 数据和本地数据两种模式：

**数据来源**
- **实时 WMS**：从 WMS 系统实时查询入库数据，查询结果自动保存到本地
- **本地数据**：查看已同步到本地的历史入库数据

**查询条件**
- 数据来源切换（实时WMS/本地数据）
- **收货日期范围**：选择开始日期和结束日期筛选
- 入库单号、物料编码筛选
- 同步到本地按钮（需先选择日期范围）

**字段信息**
- 仓库名称、货主名称、仓库代码、货主代码
- 入库单号、入库单类型、入库单行号
- 收货日期
- 物料编码、物料名称
- 数量EA、批次、体积
- SAP库存地

**本地数据存储**
- 查询时自动保存到本地 SQLite 数据库
- 可手动点击"同步到本地"批量同步指定日期范围的数据
- 自动去重：相同数据不会重复插入

### 系统设置

路径：`/settings`

WMS Cookie 管理页面，支持三个仓库独立配置：
- **阳逻仓** - 独立的 Cookie 配置
- **观澜仓** - 独立的 Cookie 配置（对应 WMS 中的坪山RDC）
- **东莞仓** - 独立的 Cookie 配置

功能：
- **自动登录**：输入 WMS 账号密码，系统自动获取 Cookie（推荐）
- **手动配置**：从浏览器复制 Cookie 粘贴保存
- 查看各仓库 Cookie 状态（正常/异常/未配置）
- 查看 Token 过期时间
- 测试连接
- 批量刷新 Token

## 技术栈

- Vue 3
- TypeScript
- Element Plus
- Vue Router
- Pinia
- Vite

## Recommended IDE Setup

[VS Code](https://code.visualstudio.com/) + [Vue (Official)](https://marketplace.visualstudio.com/items?itemName=Vue.volar) (and disable Vetur).

## Recommended Browser Setup

- Chromium-based browsers (Chrome, Edge, Brave, etc.):
  - [Vue.js devtools](https://chromewebstore.google.com/detail/vuejs-devtools/nhdogjmejiglipccpnnnanhbledajbpd)
  - [Turn on Custom Object Formatter in Chrome DevTools](http://bit.ly/object-formatters)
- Firefox:
  - [Vue.js devtools](https://addons.mozilla.org/en-US/firefox/addon/vue-js-devtools/)
  - [Turn on Custom Object Formatter in Firefox DevTools](https://fxdx.dev/firefox-devtools-custom-object-formatters/)

## Type Support for `.vue` Imports in TS

TypeScript cannot handle type information for `.vue` imports by default, so we replace the `tsc` CLI with `vue-tsc` for type checking. In editors, we need [Volar](https://marketplace.visualstudio.com/items?itemName=Vue.volar) to make the TypeScript language service aware of `.vue` types.

## Customize configuration

See [Vite Configuration Reference](https://vite.dev/config/).

## Project Setup

```sh
npm install
```

### Compile and Hot-Reload for Development

```sh
npm run dev
```

### Type-Check, Compile and Minify for Production

```sh
npm run build
```

### Lint with [ESLint](https://eslint.org/)

```sh
npm run lint
```
