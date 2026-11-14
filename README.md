# 无妄塔罗 · WU WANG TAROT

> 不妄言，不妄断。只把此刻看清。

一个**纯本地**的塔罗占卜网页：78 张牌、五种牌阵、两种抽牌方式。没有后端、没有账号、没有任何请求离开浏览器 —— 打开 `index.html` 就能用。

- 单文件应用，零构建、零依赖
- 牌面、解读、历史记录全部存在你自己的设备里
- 竖屏 / 宽屏两套布局，随窗口流式缩放

## 功能

| 页面 | 说明 |
|---|---|
| 首页 | 品牌 + 主张 + 五种牌阵（选完直接「下一步 · 抽牌」） |
| 抽牌 | 可在此写下问题（也可只在心里默念）；① 随机抽取 ② 按数字选择（78 张牌背，每行 6 张） |
| 结果 | 牌面 + 解读 + 逐张牌义 + 「复制给 AI」提示 |
| 历史记录 | 带牌面缩略图，可查看 / 复制 / 删除 / 清空，支持导出导入 JSON 备份 |
| 占卜须知 | 三条原文，弹层形式，`Esc` / 点遮罩 / ✕ 均可关闭 |

**五种牌阵**

| 牌阵 | 张数 | 结构 |
|---|---|---|
| 一张牌 | 1 | 单张看核心 |
| 只看三张 | 3 | 不设位置，读三张之间的关系 |
| 过去·现在·未来 | 3 | 时间流 |
| 二选一 | 5 | 现状 + A 发展/结果 + B 发展/结果 |
| 我以为·实际上 | 6 | 三张预设 + 三张实际 |

**流程只有两步**：首页选牌阵 → 抽牌 → 结果。

## 快速开始

直接双击 `index.html` 即可（Chrome / Edge / Safari / Firefox）。

想在本地起个服务（更接近线上环境）：

```bash
# 任选一种
npx serve .
python -m http.server 8000
```

然后访问 <http://localhost:8000>。

## 部署到 GitHub Pages

1. 新建一个仓库，把本目录的文件推到 `main` 分支（不要包含 `backup/`）。
2. 仓库 → **Settings → Pages** → Source 选 **Deploy from a branch** → Branch 选 `main`、目录选 `/ (root)` → Save。
3. 一两分钟后访问 `https://<你的用户名>.github.io/<仓库名>/`。

仓库里已经有 `.nojekyll`，GitHub Pages 会按纯静态文件提供服务，不会跑 Jekyll。所有资源都用相对路径（`favicon.svg`、`cards/*.webp`），部署在子路径下也能正常工作。

## 目录结构

```
.
├─ index.html          单文件应用（HTML + CSS + JS 全在里面）
├─ favicon.svg         站点图标
├─ cards/              78 张牌面（webp，1:1.72）
│  └─ _文件名对照表.txt 卡片 id ↔ 中文名 ↔ 文件名
├─ LICENSE             MIT + 牌面与字体的授权说明
├─ .nojekyll           GitHub Pages 用
└─ .gitignore
```

## 自定义

### 换牌面

按卡片 id 命名后覆盖 `cards/` 里的文件即可，`cards/_文件名对照表.txt` 里是全部 78 个文件名。

> id 规则：大阿尔卡纳 `major-00`…`major-21`；小阿尔卡纳 `花色-01`…`花色-10`，之后是 `page / knight / queen / king`。

- 建议比例 **1 : 1.72**（竖版），600×1032 起，`webp` 最省体积
- 逆位会自动把图片旋转 180°，不需要另存倒置版
- 缺图不会报错，会自动回退成占位底色
- 想暂时关闭图片：把 `index.html` 里的 `const USE_IMAGES = true;` 改成 `false`

### 改牌义

`index.html` 顶部的两张表：

```js
const MAJOR = [
  ["愚者", "THE FOOL", "正位牌意", "逆位牌意"],
  // ...
];
const SUITS = [
  ["wands", "权杖", "WANDS", [["正位", "逆位"], /* 14 项 */]],
  // ...
];
```

### 改牌阵

`SPREADS` 数组。`count` 是张数，`slots` 是每张牌的位置语义（`label` 位置名、`g` 分组），结果页与抽牌页都按它渲染。

### 改配色与字号

`:root` 里的 CSS 变量：

```css
--paper:#F4F1EA;  /* 纸色 */
--ink:#191715;    /* 墨色 */
--acc:#8A3324;    /* 朱红 */
--serif:"Noto Serif SC", ...;
--px:clamp(20px,3.4vw,64px);  /* 页面左右留白 */
```

### 改须知文案

`NOTES` 数组（三条：编号 / 标题 / 正文）。

## 实现说明

- 无框架、无构建：`index.html` 里是一段不到 900 行的原生 JS，状态集中在一个 `S` 对象里，`render()` 按视图拼 HTML。
- 抽牌：普通 `Math.random` 洗牌；「按数字选择」是在洗好的牌堆里取第 n 张。
- 记录存在 `localStorage`（键名 `wuwang:history`，条数不限），导出为 JSON。
- 字体走 Google Fonts（Noto Serif SC / Noto Sans SC / Inter），离线时回退到系统宋体/黑体，排版不会塌。
- 预览用参数：`?demo=spread|draw|number|result|ab|expect|single|notes|history|confirm|reset`，正常使用用不到。

## 隐私

- 不联网抓取任何数据，不加载第三方脚本或统计
- 问题与结果只写入本机 `localStorage`
- 「保存并复制」是把文本放进你系统的剪贴板，不会上传

## License

代码以 [MIT](LICENSE) 发布。牌面为 Rider–Waite–Smith（1909），已进入公有领域，详见 [LICENSE](LICENSE) 末尾说明。
