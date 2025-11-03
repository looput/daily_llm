# CSS 优化说明

## 概述

为了提升低速网络下的加载性能，我们已将公共 CSS 提取到独立文件中，利用浏览器缓存机制避免重复加载。

## 文件结构

```
web/
├── css/
│   ├── common.css         # 公共样式（加载动画、预览模态框等）
│   └── README.md          # 本文档
├── index.html             # 索引页面（引用 common.css）
├── reports/               # 报告文件目录
│   └── [各周报告]
└── _headers               # Cloudflare Pages 缓存配置
```

## 优化效果

### 1. CSS 提取优化
- **之前**: 每个页面重复嵌入相同的 CSS（~6.5KB）
- **之后**: 公共 CSS 独立文件，浏览器缓存后无需重复下载
- **节省**: 访问多个页面时，每页节省约 6.5KB 传输

### 2. 缓存策略（通过 _headers 配置）
- CSS/JS 文件: `Cache-Control: public, max-age=31536000, immutable` (1年)
- 图标文件: `Cache-Control: public, max-age=31536000, immutable` (1年)
- HTML 索引: `Cache-Control: public, max-age=3600, must-revalidate` (1小时)
- 报告页面: `Cache-Control: public, max-age=86400, must-revalidate` (1天)

### 3. 低速网络下的优势
- **首次访问**: 下载一次 CSS，后续页面直接使用缓存
- **2G/3G 网络**: 避免重复传输，显著提升加载速度
- **带宽节省**: 用户浏览 10 个页面节省约 65KB 传输量

## common.css 包含内容

### 基础样式
- 全局样式重置 (*, html, body)
- 字体和颜色定义

### 加载组件
- `.loading-overlay` - 加载遮罩层
- `.loading-spinner` - 旋转动画
- `.loading-text` - 加载文字
- `.loading-progress` - 进度条
- `.loading-progress-bar` - 进度条填充

### 错误提示
- `.iframe-error` - 错误提示容器
- `.iframe-error-icon` - 错误图标
- `.iframe-error-title` - 错误标题
- `.iframe-error-message` - 错误信息
- `.iframe-error-retry` - 重试按钮

### 预览模态框
- `.preview-modal` - 模态框容器
- `.preview-content` - 模态框内容
- `.preview-header` - 模态框头部
- `.preview-actions` - 操作按钮组
- `.preview-btn` - 按钮样式
- `.preview-close` - 关闭按钮
- `.preview-iframe-container` - iframe 容器
- `.preview-iframe` - iframe 样式

### 响应式设计
- 移动端适配（@media max-width: 768px）

## 使用方法

### 在新页面中引用
```html
<head>
    <link rel="stylesheet" href="css/common.css">
    <!-- 或相对路径 -->
    <link rel="stylesheet" href="../css/common.css">
    <link rel="stylesheet" href="../../css/common.css">
</head>
```

### 注意事项
1. 根据页面层级调整相对路径
2. 公共样式只包含多页面共享的部分
3. 页面特定样式仍需在 `<style>` 中定义

## 部署到 Cloudflare Pages

### 自动生效
- `_headers` 文件会被 Cloudflare Pages 自动识别
- 无需额外配置，推送后即生效

### 验证缓存
```bash
curl -I https://your-domain.com/css/common.css
# 检查响应头中的 Cache-Control
```

## 性能监控

### 建议监控指标
- **FCP (First Contentful Paint)**: 首次内容绘制时间
- **LCP (Largest Contentful Paint)**: 最大内容绘制时间
- **TTI (Time to Interactive)**: 可交互时间
- **Cache Hit Rate**: 缓存命中率

### Chrome DevTools 检查
1. 打开 DevTools > Network
2. 刷新页面查看 Size 列
3. 缓存的资源显示 "(from disk cache)" 或 "(from memory cache)"

## 未来优化方向

### 1. 压缩优化
- 使用 CSS minification
- 启用 Brotli/Gzip 压缩

### 2. 关键 CSS 内联
- 将首屏关键样式内联到 HTML
- 其余样式异步加载

### 3. CSS 模块化
- 根据功能进一步拆分 CSS
- 按需加载不同模块

### 4. CDN 优化
- 使用 Cloudflare 的 Auto Minify
- 启用 Early Hints

## 维护指南

### 更新公共样式
1. 编辑 `web/css/common.css`
2. 重新生成 index.html: `python -c "from utils.html_index_generator import HTMLIndexGenerator; generator = HTMLIndexGenerator(); generator.generate_html_index('web/index.html', 'web/reports')"`
3. 测试各页面样式
4. 提交并部署

### 版本管理
考虑在文件名中添加版本号或哈希值：
```html
<link rel="stylesheet" href="css/common.v2.css">
<!-- 或 -->
<link rel="stylesheet" href="css/common.abc123.css">
```

这样可以避免缓存导致的样式更新问题。

