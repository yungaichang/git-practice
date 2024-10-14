## 1. instance 的 public IP
[My instance's public IP](http://54.242.70.220/)：http://54.242.70.220/
## 2. 什麼是 instance type?
`Instance type` 是雲端環境中可以決定在執行 instance 時使用的主機電腦 (host computer) 的硬體，不同的 instance type 係由不同的 CPU、記憶體 (memory) 、儲存體 (storage) 與聯網 (networking) 容量組合而成，讓使用者得以根據其應用或軟體上的需求，彈性選擇最合適的資源組合。AWS EC2 的 instance type 針對不同的需求，可以細分為以下六種類型：  
|                           |        適用情況        |         用途         |         例子         |
|:-------------------------:|-----------------------|----------------------|-----------------------|
|1. 一般用途 (General Purpose)|當使用 CPU、記憶體與網路資源的工作負載為相似比例時|Web Server 、測試環境、程式碼儲存庫 (repository)|M8g、M7a、M4|
|2. 運算優化 (Compute optimized)|需要大量計算資源的工作負載|批次處理工作負載、媒體轉碼、高效能 Web Server 、高效能運算 (HPC)、科學建模、專用遊戲 Server 與服務引擎、機器學習推論|C8g、C7i-flex、C6gn|
|3. 記憶體優化 (Memory optimized)|需要大量記憶體來處理大數據的工作負載|記憶體快取、即時大數據分析、大型資料庫|R8g、U7i、X2gd|
|4. 加速運算 (Accelerated computing)|需要使用硬體加速器或協同處理器執行函數 (例如，浮點數計算、圖形處理或資料模式比對)來處理特定計算的工作負載|生成式 AI 應用程式、深度學習、3D 模擬、數位分身|P5、G6e、Trn1|
|5. 儲存優化 (Storage optimized)|需要對本地儲存上的大數據進行高 I/O 的工作量|提供每秒數萬次低延遲隨機的 I/O 操作 (IOPS) --> 資料倉儲、搜尋引擎、大規模資料分析|l4g、D3、H1|
|6. 高效能運算 (High-performance computing)|需要大規模執行高效能運算 (HPC) 的工作負載|大型的複雜模擬、深度學習|HPC7g、Hpc7a、Hpc6id|
## 3. 什麼是 Nginx？有哪些用途與特性？
`Nginx` (念法為 engine x) 是一個非同步框架的高效能 web server，其用途包含以下幾點：  
### 1. 反向代理 (Reverse Proxy)：  
特點為可以僅透過 Nginx 反向代理就向 Application Server 發送 Request，Client 無須知道 Application Server 的真實位置，Application Server 亦無需知道 Request 是哪一個 Client 所發送的，僅須回傳 Response (如下圖)。  
  
![](https://github.com/yungaichang/git-practice/blob/main/week-04/images/load_balance.png "圖片來源：https://www.explainthis.io/zh-hant/swe/why-nginx")  
### 2. Http Cache：  
Nginx 利用 http 快取的機制來提高 Server 的效能，其流程如下：  
    1. 當 Client 發送 Request 時，Nginx 會基於該請求的資訊生成一組 **雜湊鍵 (Hash Key)**。  
    (步驟 1 → 2)  
    2. Nginx 接著檢查這個 **Hash Key** 是否已存在於記憶體中：  
        * **若雜湊鍵不存在**：Nginx 會向 **Application Server** 請求檔案的路徑，然後再從檔案系統中取得對應的檔案。 
        (步驟 3 → 4 → 5)  
        * **若雜湊鍵存在**：Nginx 會直接從檔案系統中擷取該檔案，無需再次向上游 Server 請求。  
        (步驟 3 → 5)  
    3. 最後，Nginx 將取得的檔案回傳給 Client，完成這次請求的 Response。  
    (步驟 6)  
![](https://github.com/yungaichang/git-practice/blob/main/week-04/images/http_cache.png "圖片來源：https://www.explainthis.io/zh-hant/swe/why-nginx")
### 3. 負載平衡器 (Load Balance)：  
當遇到流量大的狀況時，需要同時開多個 Application Server 才能應付，然而 Nginx 有個特點是能夠自動將 Client 的 Request 分送給不同的 Application Server，其中分送的演算法較為常見的包含 Round Robin、Least Connections 、Least Time 、IP Hash 等，使用者可以自行設計。  
  
![](https://github.com/yungaichang/git-practice/blob/main/week-04/images/reverse_proxy.png "圖片來源：https://www.explainthis.io/zh-hant/swe/why-nginx")  
  
與較為傳統的 web server Apache 相比，Nginx 有以下特性：
1. 高效能  
事件驅動架構使 Nginx 特別擅長處理大量同時連線，尤其在傳輸靜態資源時表現出色；而在面對高流量的情況下，Nginx 的效能遠優於使用進程或線程模型的 Apache。  
2. 配置與管理的靈活性佳  
雖然 Nginx 的模組必須在編譯階段加入，無法像 Apache 一樣動態載入，但它的配置文件被認為簡單且直觀，易於管理；相較之下，Apache 的 .htaccess 讓用戶可以針對每個目錄進行靈活配置，但每次請求都需要檢查 .htaccess，可能影響效能。  
3. 記憶體消耗低  
Nginx 占用的資源與內存少，在高併發的情況下仍能保持低記憶體使用量和高效能，使得 Nginx 在處理大量連線時反應迅速，且有效降低 Server 成本。  
4. 反向代理與負載平衡
Nginx 的反向代理和負載平衡功能能以更低的資源消耗應對大量請求，相較於 Apache，能在相同硬體資源下處理更多的流量。  
## 4. pm2 套件是什麼？有什麼用處？
PM2 是一個專門為 Node.js 應用所設計的 **進程管理工具 (Process Manager)**，廣泛用於 Production 環境。它的主要目的是簡化應用程序的管理，幫助開發者更輕鬆地運行和監控 Node.js 應用程序。PM2 可以看作是 Node.js 的 app server，類似於 Uvicorn/Gunicorn 在 Python 中所扮演的角色，負責應用程序的監控、負載管理、以及自動重啟等運行維護工作。  

PM2 套件的主要用處有下列幾點：
* 提升應用穩定性：當應用崩潰時自動重啟，確保服務不中斷
* 優化系統的資源使用：支援 Cluster 模式，在多核心 CPU 上運行多個進程，提升系統的效能和 throughput
* 性能監控：追蹤 CPU 和記憶體的使用狀況，方便監控應用狀態
*  Log 管理：自動整理 Log ，簡化故障排查
* 開機即自動啟動：配置為系統服務，確保 Server 重啟後應用自動運行
* 簡化應用管理：透過簡單指令啟動、停止或重啟應用，減少手動維護工作

PM2 有以下幾個典型的使用場景：
* 網站後端 Server ：管理 Node.js 建置的 Web API 或服務
* 長時間運行的服務：例如聊天應用軟體、即時數據處理服務
* 微服務 (Microservices) 架構：在多進程下同時運行多個微服務或單體應用（Monolithic Application）
* 生產環境監控：透過 PM2 的監控功能，實時監視應用軟體的運行狀況

## 5. 步驟 9 中提到的 `proxy` 是什麼意思？為什麼要透過 Nginx 來 `proxy` 到 Express 開發的 Web Server?
`Proxy（代理）`是一種在 Client 與 Server 之間充當中介的服務，負責轉發 request 和 response。Proxy 依方向和用途可以分為以下兩種：
1. Forward Proxy（轉向代理）：  
![](https://github.com/yungaichang/git-practice/blob/main/week-04/images/forward_proxy.png "圖片來源：https://www.jyt0532.com/2019/11/18/proxy-reverse-proxy/")  
  
Forward Proxy 位於使用者裝置和公共網際網路之間，Client 透過 Proxy Server 訪問其他外部資源，即所有的網頁瀏覽流量都會通過Proxy Server router，而該 Proxy Server 會檢查這些網頁瀏覽並且套用特定的安全使用政策。使用的時點為當用戶想隱藏自己的真實 IP，或者希望突破網路限制。舉例而言，連接到 VPN（Forward Proxy）以訪問互聯網資源、用戶在辦公室內網中，透過 Proxy Server 訪問 Google 或其他被防火牆封鎖的網站等。
2. Reverse Proxy（反向代理）：  
![](https://github.com/yungaichang/git-practice/blob/main/week-04/images/reverse_proxy_1.png "圖片來源：https://www.jyt0532.com/2019/11/18/proxy-reverse-proxy/")  
  
Reverse Proxy 位於一個或多個 Web Server 前方的 Server，用於攔截來自 Client 的 request。當 Client 向網站的原始 Server 傳送 request 時，這些 request 會在網路邊緣被 Reverse Proxy Server 攔截，再由 Reverse Proxy Server 轉發 request 到原始 Server （像是作業中的 Express Web Server）。接著，Reverse Proxy Server 會將原始 Server 的 response 轉發回給 Client。Reverse Proxy 的應用時機為用於需要負載平衡、安全性增強、隱藏原始 Server 的 IP，或集中管理多個後端應用。Nginx 代理請求到內部運行的 Express Server，讓外部用戶訪問 Web 服務即是一個例子。

透過 Nginx 來 Proxy 到 Express 開發的 Web Server 的原因有以下幾點：
* 提高安全性：  
Express 是一個輕量且高效的 Node.js 框架，適合快速開發應用；而 Nginx 作為成熟且高效的 Web Server，可以在前端攔截潛在的惡意請求，提升整體系統的安全性。除此之外，Nginx 可以隱藏內部的 Express Server，避免其直接暴露於網路，從而降低被攻擊的風險。  
* 支援負載平衡與可擴展性：  
Nginx 支援將流量分配至多個後端 Server，實現負載平衡。當應用程式需要垂直擴展時，Nginx 可以有效地將請求均衡分發到多個 Express instance 上，提升應用的可擴展性和穩定性。  
* 支援 HTTPS：  
Nginx 可以處理前端的 HTTPS 請求，並將其轉發為 HTTP 請求給內部的 Express Server 。如此一來，便可以避免在 Express Server 上單獨管理 SSL 憑證的複雜性，並提高了網路傳輸的安全性。  
* 管理多個應用程式的集中入口：  
透過 Nginx，使用者可以根據不同的 URL 路徑或子域名，訪問不同的後端應用。例如：  
    * `/app1` 路徑指向一個 Express Server 
    * `/app2` 路徑指向一個 Flask Server 
這樣的設計使多個應用可以共用同一個公共 IP 和域名，簡化了管理和維護工作。  
* 提高靜態資源的傳輸效率：  
Nginx 對於靜態資源（如 HTML、CSS、JS 文件）的傳輸進行了優化，可以快速回應靜態內容請求。而 Express 專注於處理動態請求，透過 Nginx 分擔靜態資源的負載，可以顯著提升系統的整體性能。  
## 6. 步驟 9 的 Nginx 設定檔
```
server {
    listen 80;

   Server _name 54.242.70.220;

    location / {
        proxy_pass http://localhost:3001;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```
## 7. Security Group 是什麼？用途為何？有什麼設定原則嗎？
`Security Group (安全群組)` 是 AWS 中的一種虛擬防火牆，負責控制進出 EC2 instance 的網路流量。使用者可以透過為每個傳出規則指定目的地、連接埠範圍和通訊協定，定義允許哪些傳入 (Inbound) 和傳出 (Outbound) 流量通過特定的連接埠或通訊協定連接到使用者的 instance。

Security Group 的用途有以下幾點：
1. 網路安全控制：
   - 用來限制外部和內部的網路存取，防止未授權的連接
   - 它確保只有指定的 IP、連接埠、或通訊協定可以連接到 EC2 instance，增強系統的安全性

2. 控制流量方向：
   - **入站 (Inbound) 規則**：控制哪些流量可以進入 EC2 instance。例如，只允許特定的 IP 地址通過 SSH 連接 (Port 22)
   - **出站 (Outbound) 規則**：控制哪些流量可以從 EC2 instance 發出。例如，允許 instance 訪問外部網路上的 API 服務

3. 靈活配置應用需求：
   - Security Group 可以動態配置，支援根據應用需求調整規則，而無需重啟 EC2 instance
   - 它們也可以重複使用，同一個 Security Group 可以應用於多個 instance

Security Group 的設定原則如下：

1. 最小權限原則 (Principle of Least Privilege)：
   - 只開放應用程式所需的最少端口和協議，避免不必要的暴露。例如，僅允許 HTTP (port 80) 或 HTTPS (port 443) 流量

2. 特定來源限制：
   - 如果使用 SSH (port 22) 進行連接，應限制為自己的 IP 地址範圍，而不是 `0.0.0.0/0`，以減少未授權訪問的風險

   例如：
   ``` 
   Inbound Rule: SSH (Port 22) -> Source: 203.0.113.0/32 (僅允許自己的 IP) 
   ```

3. 拒絕所有、允許部分：
   - Security Group 的規則是「**預設拒絕所有流量**」，僅允許使用者明確設定的流量通過

4. 使用組合 Security Groups：
   - 可以建立多個 Security Group，並針對不同功能進行組合。例如：
     - 一個 Security Group 專門負責 HTTP/HTTPS 流量
     - 另一個 Security Group 專門允許 SSH 連接

5. 定期監控與更新：
   - 定期檢查和更新 Security Group 的規則，確保不會有多餘的開放連接埠，並根據需求調整設定

以下舉一些 Security Group 的設定範例：

允許 HTTP 和 SSH 連接的 Security Group：  
| Protocol | Port Range | Source           | 用途                   |
|----------|------------|------------------|------------------------|
| SSH      | 22         | 203.0.113.0/32   | 允許使用者的 IP SSH 連接    |
| HTTP     | 80         | 0.0.0.0/0        | 允許所有 IP 訪問網站   |
| HTTPS    | 443        | 0.0.0.0/0        | 允許所有 IP 訪問 HTTPS |
## 8. 什麼是 sudo? 為什麼有的時候需要加上 sudo，有時候不用？
`sudo` 是 "Super User DO" 的簡寫，其中，Super User 指的是電腦管理員，即代表擁有這台電腦的最高權限，可以對這台電腦中的任何檔案進行讀寫。而 `sudo` 這個字通常會被加在某一個指令的前面，代表該指令是透過 Super User 所執行的，即普通用戶被允許以 root 權限執行命令。

是否需要使用 `sudo`，取決於執行的操作是否涉及系統層級的更改，以下是一些需要使用 `sudo` 的情境：
* 安裝或移除軟體
```
sudo apt install nginx
```
* 修改系統設定文件，例如編輯 /etc 目錄下的 Nginx 配置文件
```
sudo nano /etc/nginx/nginx.conf
```
* 啟動或停止系統服務
```
sudo systemctl start nginx
```
* 改變檔案的讀寫權限或所有者
* 訪問受限的目錄或檔案，某些系統目錄（如 /var, /usr）只能由超 Super User 訪問

而其他的情境如在 `/home/username` 內建立檔案或資料夾、啟動開發環境中的 Local Server、編輯自己目錄下的文件，或安裝使用者級別的 npm 套件等動作則不需要加上 `sudo` 的字樣。

如果不確定特定指令是否需要加上 `sudo`，可以先嘗試不加上此字樣執行，若系統返回 `Permission denied` 或提示需要更高權限時，再加上 `sudo`，但在使用上需謹慎，以避免造成不可挽回的損害。
## 9. Nginx 的 Log 檔案在哪裡？你怎麼找到的？怎麼看 Nginx 的 Log？
Nginx 的 Log 檔案預設存放於 `/var/log/nginx/` 目錄中，其中存放的 Log 檔案又可以分為：
* 訪問 Log  (Access Log)：`/var/log/nginx/access.log` 記錄所有 HTTP 請求及其狀態
* 錯誤 Log  (Error Log)：`/var/log/nginx/error.log` 記錄運行過程中的異常和錯誤事件
若配置中有多個虛擬主機或自訂 Log 路徑，則需檢查 `/etc/nginx/nginx.conf` 或 `/etc/nginx/sites-available/` 內的配置。

上網查詢以及詢問 ChatGPT 後知道要確認 Log 檔案的路徑可以透過以下兩種方式：
1. 檢查全域配置：
   在 `/etc/nginx/nginx.conf` 文件中，可使用以下命令檢索所有與 Log 相關的設置：
   ```bash
   sudo cat /etc/nginx/nginx.conf | grep log
   ```
   例如：
   ```
   access_log /var/log/nginx/access.log;
   error_log /var/log/nginx/error.log;
   ```

2. 檢查虛擬主機配置：
   在 `/etc/nginx/sites-available/` 目錄下的虛擬主機配置中，也可能重新定義 Log 路徑：
   ```bash
   sudo cat /etc/nginx/sites-available/default | grep log
   ```

而欲查看 Nginx 的 Log，可以透過以下幾種方法：

1. 顯示整個 Log 文件內容：
   使用 `cat` 命令可以查看 Log 的全部內容：
   ```bash
   sudo cat /var/log/nginx/access.log
   sudo cat /var/log/nginx/error.log
   ```

2. 查看最新的 Log 記錄：
   使用 `tail` 命令顯示最後幾行 Log ：
   ```bash
   sudo tail -n 20 /var/log/nginx/access.log
   sudo tail -n 20 /var/log/nginx/error.log
   ```
   註：`-n 20` 參數表示顯示最後 20 行。

3. 實時監控 Log：
   使用 `tail -f` 命令持續監控 Log 的更新：
   ```bash
   sudo tail -f /var/log/nginx/access.log
   sudo tail -f /var/log/nginx/error.log
   ```
   註：此命令會不斷輸出新寫入的 Log 條目，適合用於實時監控。

4. 分頁查看較大的文件：
   若 Log 文件過大，可使用 `less` 命令分頁檢視：
   ```bash
   sudo less /var/log/nginx/access.log
   ```
   - 使用 **上下鍵** 移動。
   - 按 **`q`** 鍵退出檢視。

以下是一些 Log 檔案的例子：

- 訪問 Log  (Access Log)：
  ```
  203.0.113.10 - - [14/Oct/2024:10:45:23 +0000] "GET /index.html HTTP/1.1" 200 612 "-" "Mozilla/5.0"
  ```
  解釋：
  - IP：`203.0.113.10`
  - 時間：`14/Oct/2024:10:45:23`
  - HTTP 動作：`GET /index.html`
  - 狀態碼：`200` (成功)

- 錯誤 Log  (Error Log)：
  ```
  2024/10/14 11:00:12 [error] 1234#0: *1 connect() failed (111: Connection refused) while connecting to upstream
  ```
  解釋：
  - 時間：`2024/10/14 11:00:12`
  - 錯誤類型：`Connection refused` (無法連接上游伺服器)
## 10. 在過程中遭遇的問題：
1. 第一次建立好 instance 後透過 public IP 無法成功連線  
--> 後來發現是在建立 instance 時沒有勾選到 Allow HTTP traffic from internet 的選項，而在已經創建好的 instance 設定中沒有找到可以調整這部分的地方，因此後來的解決方法是再創建一個新的 instance，並且勾選 Allow HTTP traffic from internet。
2. 在第 9 步操作 Nginx 設定檔時不知道如何改寫，有嘗試把：
```
server {
    listen 80;

   Server _name 54.242.70.220;

    location / {
        proxy_pass http://localhost:3001;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```
的內容貼上，但在檢查設定檔時會跳出錯誤訊息。  
--> 後來發現底下有 default 的內容，只要把原本的 location / 註解掉，便可以順利完成設定。

(以上問題都是問同學後解惑的，非常感謝這位同學的幫助！)

---

參考資料：
1. [Amazon EC2 執行個體類型](https://aws.amazon.com/tw/ec2/instance-types/)
2. [Amazon EC2 instance types](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-types.html#AvailableInstanceTypes)
3. [首頁 » Blog » Nginx 是什麼？認識 Web Server 與 Nginx入門教學
Web 技術
Nginx 是什麼？認識 Web Server 與 Nginx入門教學](https://tw.alphacamp.co/blog/nginx)
4. [[基礎觀念系列] Web Server & Nginx — (2)](https://medium.com/starbugs/web-server-nginx-2-bc41c6268646)
5. [Nginx 是什麼？有哪些用途？](https://www.explainthis.io/zh-hant/swe/why-nginx)
6. [PM2](https://editor.leonh.space/2023/pm2/)
7. [Ubuntu 虛擬主機部署 Node.js 服務，使用 PM2 服務管理](https://www.casper.tw/development/2023/10/03/unbuntu-and-pm2/)
8. [What is a Forward Proxy?](https://www.checkpoint.com/tw/cyber-hub/network-security/what-is-a-forward-proxy/)
9. [系統設計 - 正向代理跟反向代理](https://www.jyt0532.com/2019/11/18/proxy-reverse-proxy/)
10. [什麼是反向代理？| 代理 Server 說明](https://www.cloudflare.com/zh-tw/learning/cdn/glossary/reverse-proxy/)
11. [【技術分享】Security Group 和 IAM的差別為何？](https://medium.com/@awseducate.cloudambassador/%E6%8A%80%E8%A1%93%E5%88%86%E4%BA%AB-security-group%E5%92%8Ciam%E7%9A%84%E5%B7%AE%E5%88%A5%E7%82%BA%E4%BD%95-942d303565b7)
12. [AWS的Security group設定](https://akuma1.pixnet.net/blog/post/344237021)
13. [三分鐘快速了解 Linux sudo 指令是什麼！](https://yhtechnote.com/linux-sudo/#%E4%BB%80%E9%BA%BC%E6%98%AF_sudo)
14. [如何查看Nginx日志？](https://blog.csdn.net/oMcLin/article/details/110424347)