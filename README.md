# SpringBucks 咖啡訂單快取示範 ⚡

[![Java](https://img.shields.io/badge/Java-21-orange.svg)](https://www.oracle.com/java/)
[![Spring Boot](https://img.shields.io/badge/Spring%20Boot-3.4.5-brightgreen.svg)](https://spring.io/projects/spring-boot)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

## 專案介紹

本專案為「SpringBucks 咖啡訂單快取示範」，以 Spring Boot 3.x 為基礎，展示如何在 RESTful API 與靜態資源中實作快取控制（Cache-Control），並結合 JPA、H2 資料庫、Lombok 與 Joda Money，實現一個簡易的咖啡訂單管理系統。

- 提供咖啡品項查詢、新增、批次匯入與訂單建立 API
- 演示靜態資源快取與 API 快取控制的實務做法
- 適合學習 Spring Boot 快取、JPA 實作、RESTful 設計
- 目標對象：Java/Spring 後端工程師、教學/實驗用專案

> 💡 **為什麼選擇此專案？**
> - 範例完整，涵蓋快取、資料庫、RESTful、Lombok、Joda Money 等主題
> - 程式碼有清楚註解，方便團隊協作與維護
> - 採用台灣常用專業術語，溝通無障礙

### 🎯 專案特色

- 靜態資源快取與 API 快取控制實作
- JPA + H2 記憶體資料庫，快速上手
- Money 欄位型別安全轉換，支援多幣別

## 技術棧

### 核心框架
- **Spring Boot 3.4.5**：Java 主流後端框架，快速建置 RESTful API 與整合快取
- **Spring Data JPA**：ORM 實作，簡化資料存取層

### 開發工具與輔助
- **Lombok**：自動產生 getter/setter/builder，提升開發效率
- **Joda Money**：處理金額與幣別的專業型別
- **H2 Database**：輕量級記憶體資料庫，方便測試

## 專案結構

```text
cache-demo2/
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── geektime/spring/springbucks/waiter/
│   │   │       ├── controller/   # 控制器 (API)
│   │   │       ├── model/        # 實體/資料物件
│   │   │       ├── repository/   # JPA 資料存取
│   │   │       ├── service/      # 商業邏輯
│   │   │       └── support/      # 格式轉換等輔助
│   │   └── resources/
│   │       ├── application.properties
│   │       ├── schema.sql
│   │       ├── data.sql
│   │       └── static/
│   └── test/
├── pom.xml
└── README.md
```

## 快速開始

### 前置需求
- JDK 21（建議使用 Adoptium 或 OpenJDK）
- Maven 3.8 以上

### 安裝與執行

1. **克隆此倉庫：**
    ```bash
    git clone https://github.com/username/cache-demo2.git
    ```
2. **進入專案目錄：**
    ```bash
    cd cache-demo2
    ```
3. **編譯專案：**
    ```bash
    mvn compile
    ```
4. **執行應用程式：**
    ```bash
    mvn spring-boot:run
    ```

## 進階說明

### 環境變數

```properties
# 必要設定（本專案預設使用 H2，不需額外設定）
# spring.datasource.url=jdbc:h2:mem:testdb
# spring.datasource.username=sa
# spring.datasource.password=

# 選用設定
LOG_LEVEL=INFO
```

### 設定檔說明

```properties
# application.properties 主要設定
spring.jpa.hibernate.ddl-auto=none
spring.jpa.properties.hibernate.show_sql=true
spring.jpa.properties.hibernate.format_sql=true
spring.mvc.static-path-pattern=/static/**
spring.web.resources.cache.cachecontrol.max-age=20s
```
- 靜態資源快取 20 秒，API 快取請參考 Controller 註解

#### 靜態資源 Cache-Control 行為說明

- `max-age=20` 代表「最多快取 20 秒」。
- **20 秒內**：瀏覽器會直接從本地快取讀取，不會發送請求到伺服器。
- **超過 20 秒**：下一次瀏覽器請求這個資源時，會重新向伺服器發送請求（通常是 GET）。
    - 伺服器會回傳最新的資源內容和新的 `Cache-Control: max-age=20` header。
    - 瀏覽器會用新的內容覆蓋本地快取，並重新開始 20 秒的快取倒數。
- **如果伺服器內容沒變？**
    - 若有啟用 ETag 或 Last-Modified，瀏覽器會帶著 If-None-Match/If-Modified-Since header 請求，伺服器會回 304 Not Modified，瀏覽器繼續用舊的快取內容。
- **如果伺服器內容有變？**
    - 伺服器會回 200 OK 和新內容，瀏覽器更新快取。

> 這是 HTTP 標準行為，所有現代瀏覽器都會遵守。

- 如果你想讓瀏覽器永遠不要快取，可以設：
```properties
    spring.web.resources.cache.cachecontrol.no-store=true
```

## 參考資源

- [Spring Boot 官方文件](https://docs.spring.io/spring-boot/docs/current/reference/html/)
- [Spring Data JPA 官方文件](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/)
- [Joda Money 官方文件](https://www.joda.org/joda-money/)
- [Lombok 官方文件](https://projectlombok.org/)

## 注意事項與最佳實踐

### ⚠️ 重要提醒

| 項目     | 說明             | 建議做法         |
|----------|------------------|-----------------|
| 安全性   | 密碼與金鑰處理   | 使用環境變數     |
| 效能     | 資料庫連線       | 使用連線池       |
| 註解     | 重要程式區塊     | 請加上清楚註解   |
| 專業用語 | 文件/註解/命名   | 採用台灣常用術語 |

#### 🔒 最佳實踐指南
- 重要邏輯、複雜流程請加上註解，方便團隊維護
- Controller 層建議用 ResponseEntity 控制 HTTP header
- Money 型別建議用 JPA Converter 處理
- 靜態資源快取建議用 application.properties 控制
- 盡量避免硬編碼，設定集中於 properties

## 授權說明

本專案採用 MIT 授權條款，詳見 LICENSE 檔案。

## 關於我們

我們主要專注在敏捷專案管理、物聯網（IoT）應用開發和領域驅動設計（DDD）。喜歡把先進技術和實務經驗結合，打造好用又靈活的軟體解決方案。

## 聯繫我們

- **FB 粉絲頁**：[風清雲談 | Facebook](https://www.facebook.com/profile.php?id=61576838896062)
- **LinkedIn**：[linkedin.com/in/chu-kuo-lung](https://www.linkedin.com/in/chu-kuo-lung)
- **YouTube 頻道**：[雲談風清 - YouTube](https://www.youtube.com/channel/UCXDqLTdCMiCJ1j8xGRfwEig)
- **風清雲談 部落格**：[風清雲談](https://blog.fengqing.tw/)
- **電子郵件**：[fengqing.tw@gmail.com](mailto:fengqing.tw@gmail.com)

**📅 最後更新：[2025-07-16]**  
**👨‍💻 維護者：[風清雲談團隊]** 