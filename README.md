# 记一笔

一个简洁的本地记账 PWA。纯前端单文件实现，数据全部保存在浏览器 `localStorage` 里，不上传任何服务器。

**在线使用：https://zeliayu.github.io/jybook/**

手机浏览器打开上面的地址，选择「添加到主屏幕」，即可像 App 一样使用。

## 功能

### 首页
- 按月查看收支概览，右上角可在 **支出 / 收入** 之间切换
- 主金额、笔数、日均、最大单笔随类型联动
- 底部汇总本月收入、本月支出和结余（结余为正显示绿色，为负显示红色）
- 按分类展示占比条，选中哪个类型就展示哪个类型的分类分布
- 最近 5 条记录（含收入），点击可查看详情

### 记账
- 支出 / 收入类型切换，分类网格跟着切换
- 金额、分类、备注、日期（默认今天）
- 保存后自动清空金额和备注，分类保留在常用状态

### 记录
- 按月分组，按日期倒序，同一天内按记录时间倒序
- 每天显示当日支出小计
- 点击记录可打开详情：修改备注、删除记录

### 统计
- 支持 **本月 / 本周 / 全部** 三个时间范围（当前版本统计的是支出）
- SVG 环形图 + 分类图例，下方是分类明细条

### 分类管理
- 支出分类和收入分类分开维护
- 新建、编辑、删除分类，支持从预设 emoji 里选图标
- 删除已被使用的分类会提示该分类下的记录数；记录会保留，但显示为「已删除分类」

### 数据备份
- **导出数据**：把全部分类和记录存成 `记一笔备份_YYYYMMDD.json` 下载到本地
- **导入数据**：选择备份文件恢复，导入会覆盖当前全部数据（有二次确认）

> 换手机或清除浏览器数据前请先导出备份。

## 技术说明

- 单个 `index.html`，无构建、无依赖、无外部请求
- 数据格式：`localStorage['jybook_data']` → `{ categories: [...], records: [...] }`
- 记录字段：`id`、`type`（`expense` / `income`）、`catId`、`amount`、`note`、`date`（`YYYY-MM-DD`）、`ts`
- 日期统一按本地时区手动解析，避免 `new Date('YYYY-MM-DD')` 的 UTC 偏移
- 手机端用 `visualViewport` 量出输入法高度写入 CSS 变量 `--kb`，底部留白和弹窗据此上移，避免备注栏被键盘挡住
- Service Worker (`sw.js`) 做离线缓存，改动上线后需同步修改 `CACHE_NAME` 版本号

## 文件

| 文件 | 说明 |
| --- | --- |
| `index.html` | 应用本体（HTML + CSS + JS 全在一个文件） |
| `code.html` | 与 `index.html` 内容一致，是 `manifest.json` 里 PWA 的 `start_url`，改完记得同步 |
| `manifest.json` | PWA 配置（图标、名称、启动页） |
| `sw.js` | Service Worker，离线缓存 |
| `icon-192.png` / `icon-512.png` | 应用图标 |

## 本地运行

直接双击 `index.html` 即可使用。

Service Worker 只在 `https:` 或 `localhost` 下注册，所以想测试离线/PWA 安装，需要起一个本地服务：

```bash
python -m http.server 8000
# 然后访问 http://localhost:8000
```

在手机上安装：用浏览器打开上面「在线使用」的地址，选择「添加到主屏幕」。

## 部署

线上地址：**https://zeliayu.github.io/jybook/**（GitHub Pages，从本仓库的 `main` 分支根目录发布）

把整个目录作为静态站点托管即可（GitHub Pages、Vercel、Netlify 等均可，无需任何环境变量或后端）。

注意：应用运行在 HTTPS 或 `localhost` 下才能启用 PWA 离线能力。

推送后生效：改动推到 `main` 分支，等 Pages 重新构建后刷新页面。因为是 PWA，浏览器可能缓存旧版本——如果看不到更新，改一下 `sw.js` 里的 `CACHE_NAME` 版本号（如 `jybook-v6` → `jybook-v7`）再推，或者在浏览器里强制刷新。
