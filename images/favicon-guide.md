# Favicon 製作指南

## 已完成
✅ `favicon.svg` - SVG 格式的 Favicon（沖繩島嶼 + 棕櫚樹 + 太陽）

## 需要生成的尺寸

### 方法 A：使用線上工具（最簡單）

1. 前往 [RealFaviconGenerator](https://realfavicongenerator.net/)
2. 上傳 `favicon.svg`
3. 生成所有需要的尺寸：
   - `favicon.ico` (16x16, 32x32, 48x48 多尺寸合一)
   - `favicon-16x16.png`
   - `favicon-32x32.png`
   - `apple-touch-icon.png` (180x180)
   - `android-chrome-192x192.png`
   - `android-chrome-512x512.png`

### 方法 B：使用 Inkscape/Illustrator

1. 開啟 `favicon.svg`
2. 匯出為 PNG，依照以下尺寸：
   - 16×16px
   - 32×32px
   - 180×180px (Apple Touch Icon)
   - 192×192px (Android)
   - 512×512px (Android)

### 方法 C：使用命令列工具（需安裝 ImageMagick）

```bash
# 安裝 ImageMagick (Mac)
brew install imagemagick

# 轉換 SVG 為各種尺寸
convert favicon.svg -resize 16x16 favicon-16x16.png
convert favicon.svg -resize 32x32 favicon-32x32.png
convert favicon.svg -resize 180x180 apple-touch-icon.png
convert favicon.svg -resize 192x192 android-chrome-192x192.png
convert favicon.svg -resize 512x512 android-chrome-512x512.png

# 生成 .ico 檔案（包含多尺寸）
convert favicon-16x16.png favicon-32x32.png favicon.ico
```

## HTML 整合

在所有 HTML 頁面的 `<head>` 中添加：

```html
<!-- Favicon -->
<link rel="icon" type="image/svg+xml" href="images/favicon.svg">
<link rel="icon" type="image/png" sizes="32x32" href="images/favicon-32x32.png">
<link rel="icon" type="image/png" sizes="16x16" href="images/favicon-16x16.png">
<link rel="apple-touch-icon" sizes="180x180" href="images/apple-touch-icon.png">
<link rel="manifest" href="site.webmanifest">
```

## site.webmanifest 內容

創建 `site.webmanifest` 於根目錄：

```json
{
    "name": "卡在國際通",
    "short_name": "沖繩旅遊",
    "icons": [
        {
            "src": "/images/android-chrome-192x192.png",
            "sizes": "192x192",
            "type": "image/png"
        },
        {
            "src": "/images/android-chrome-512x512.png",
            "sizes": "512x512",
            "type": "image/png"
        }
    ],
    "theme_color": "#4ECDC4",
    "background_color": "#FAF3E0",
    "display": "standalone"
}
```

## 設計說明

### Favicon 設計元素
- **背景**：海洋藍綠色漸層（#4ECDC4 → #44A08D）
- **波浪**：白色半透明線條表現海浪
- **島嶼**：綠色橢圓形代表沖繩島
- **棕櫚樹**：綠色棕櫚樹象徵熱帶度假風情
- **太陽**：黃色太陽代表沖繩陽光

### 符合設計系統
- 使用 S51 Hand Drawn 風格的配色
- 簡潔的手繪線條風格
- 高辨識度，即使在小尺寸也清晰可見

## 測試

Favicon 生成後，在各裝置測試：
- [ ] Chrome (Desktop)
- [ ] Safari (Desktop)
- [ ] Firefox (Desktop)
- [ ] Safari (iOS)
- [ ] Chrome (Android)
- [ ] 書籤列顯示
- [ ] 分頁標籤顯示
