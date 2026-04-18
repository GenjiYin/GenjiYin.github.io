# Blog 文章发布流程

## 流程步骤

1. 读取 md 内容，在 md 的路径下转化为网页 html，添加标签、分享按钮
2. 在主页 index.html 添加文章卡片，指向生成的 html
3. 提交并推送

---

## 设计规范

### 整体风格
- 暗色主题，GitHub 风格配色
- 背景色：`#0d1117`，主文字：`#c9d1d9`，次要文字：`#8b949e`
- 强调色（链接/高亮）：`#58a6ff`
- 边框色：`#30363d` / `#21262d`
- 字体：`'Microsoft YaHei', 'Segoe UI', Arial, sans-serif`

### 导航栏
- sticky 顶部，毛玻璃效果（`backdrop-filter: blur(10px)`），高度 60px
- Logo 文字：`XiaoYinXu`，颜色 `#58a6ff`
- 导航链接：首页 / 关于我 / GitHub

### 文章页结构
- 顶部导航栏
- 文章区域（`article-wrap`）：最大宽度 860px，居中
  - 返回首页链接
  - 文章头部：分类标签 + 日期 + 标题 + 副标题 + 分享按钮
  - 正文内容
- 底部 footer：busuanzi 访问量统计

### 分享功能
- 点击"↗ 分享这篇文章"按钮：
  1. 自动复制当前页面 URL 到剪贴板
  2. 弹出分享卡片预览（canvas 绘制，640×360）
  3. 卡片右侧嵌入二维码（手机扫码直达）
  4. 提供"下载图片"按钮
- 依赖：`https://cdn.jsdelivr.net/npm/qrcodejs@1.0.0/qrcode.min.js`
- 卡片内容：作者名、分类标签、标题、摘要、页面 URL、二维码
- **卡片摘要（excerpt）必须与主页文章卡片的 `post-excerpt` 内容保持一致**

---

## 分类标签

| class | 显示文字 | 颜色 |
|---|---|---|
| `cat-ai` | AI 工程 | 橙色系 `#ffa657` |
| `cat-quant` | 量化交易 | 蓝色系 `#58a6ff` |
| `cat-tech` | 技术 | 紫色系 `#bc8cff` |
| `cat-resume` | 关于我 | 青色系 `#64ffda` |
| `cat-talk` | 杂谈 | 玫瑰红系 `#ff7b9c` |

新栏目可按此格式扩展，在 index.html 的 CSS 中添加对应 `cat-xxx` 样式。

---

## 主页文章卡片格式

在 `index.html` 的 `<ul class="post-list">` 中添加：

```html
<li class="post-item">
    <div class="post-meta">
        <span class="post-category cat-ai">AI 工程</span>
        <span class="post-date">2026-04</span>
    </div>
    <a href="./栏目/文章目录/" class="post-title">文章标题</a>
    <p class="post-excerpt">一两句话的摘要，说明文章核心内容。</p>
</li>
```

新文章插入到列表顶部（最新在前）。

---

## 依赖

- **qrcodejs**：分享卡片二维码生成，CDN 引入
- **busuanzi**：页面访问量统计，每篇文章页 footer 引入
  ```html
  <script async src="//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>
  本页访问量 <span id="busuanzi_value_page_pv"></span> 次
  ```
