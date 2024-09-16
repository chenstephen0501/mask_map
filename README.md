# Vue 3 Mask Map

這是一個使用 Vue 3、Vite、Tailwind CSS 以及多個插件的 Todo List 前端專案。它連接到 Django REST Framework 提供的後端 API。

![Mask Map首頁](/public/image/maks_map.png)

[專案演示影片](https://youtu.be/wToDe6N1KfU)

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

### 2. 安裝依賴
```bash
$ npm install
```

### 3. 設定環境變數
- VITE_API_BASE_URL=http://your-backend-api-url.com

### 4. 啟動開發伺服器
```bash
$ npm run dev
```