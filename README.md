# Vue 3 Mask Map

這是一個使用 Vue 3、Vite、Tailwind CSS 以及多個插件製作的口罩地圖前端專案。該專案透過第三方 API 顯示各藥局的成人與兒童口罩庫存，並提供地圖顯示功能。

[口罩地圖連結](https://maskmap0.netlify.app/)

![Mask Map首頁](/public/image/mask_map.gif)


## 專案功能

- **口罩查詢**：透過後端 API，顯示藥局的成人口罩與兒童口罩庫存數量。
- **地圖顯示**：使用 Leaflet 套件來展示藥局位置，並提供地圖上的互動功能。
- **HTTP 請求**：使用 `axios` 進行 API 請求，以實現前後端的數據交互。

### 前端插件

- axios 1.7.7
- leaflet 1.9.4
- lodash 4.17.21

### 開發環境

- vue 3.4.29
- vue-router 4.3.3 
- vitejs/plugin-vue 5.0.5 
- vit 5.3.1 
- vite-plugin-vue-devtool 7.3.1 

## 安裝與設定

### 1. 克隆專案，將專案克隆至本地：

```bash
$ git clone git@github.com:chenstephen0501/mask_map.git
$ cd mask_ma
```

### 2. 安裝插件
```bash
$ npm install
```

### 3. 設定環境變數
- VITE_API_BASE_URL=http://your-api-url.com

### 4. 啟動開發伺服器
```bash
$ npm run dev
```