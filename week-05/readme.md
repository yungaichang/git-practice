## 1. 我的網址
My website: https://www.yungaide.me/
## 2. 購買網域的平台
在 namecheap 上使用 github education 的優惠取得一年免費的網域
## 3. DNS 的 A record 是什麼？
A record 中的 "A" 代表 「位址（Adrress）」，其用途主要是儲存網域的 IPv4 位址，是最基礎的 DNS record 類型。而若網站有 IPv6 位址，則會改為使用 AAAA record。

大部分網站僅會有一個 A record，不過也可以有多個，在此狀況下會採用循環配置資源附載平衡技術，以將 request 的流量分配到多個託管相同內容的其中一個 IP 位址。

使用 A record 的時機通常在有 IP 位址查閱的需求時，他可以將網域名稱對應到期 IPv4 位址，如此一來，使用者不需要去記得網站實際的 IP 位址，其 Web 瀏覽器會自行向 DNS 解析程式傳送查詢，而能夠直接透過這樣的轉換連接並載入網站。

以下舉一個簡單的例子：

| 網域名稱       | TTL  | 類型 | 記錄類型 | IPv4 位址   |
| -------------- | ---- | ---- | -------- | ----------- |
| example.com.   | 3600 | IN   | A        | 192.0.2.1   |

- `example.com.` 是網域名稱，表示這個 A record 適用於 `example.com` 網站。結尾的「.」符號在 DNS record 中表示是完整的網域名稱（fully qualified domain name, FQDN）。
- `3600` 是 TTL（Time To Live），表示該記錄在 DNS 緩存中的存活時間，單位為秒。因此，這裡的 3600 秒，即 1 小時。
- `IN` 表示該記錄屬於 Internet 類型（Internet class），在 A record 中幾乎總是 `IN`。
- `A`：這表示該記錄是一個 A record ，它會解析為一個 IPv4 位址。
- `192.0.2.1`：這是將要解析的 IPv4 位址，當用戶訪問 `example.com` 時，DNS 會將該網域名稱解析為這個 IP 位址。

## 4. DNS 的 NS record 是什麼？
NS record 中的 "NS" 代表「名稱伺服器（Name Server）」，其主要功能是指示網際網路應如何找到一個網域的 DNS record，包括 A record（IPv4 位址）、CNAME record（別名記錄）、MX record（郵件交換器記錄）等。而 NS Record 的作用是告訴 DNS 查詢者應該向哪一個名稱伺服器請求該網域的其他 DNS record，透過正確設定的 NS record，使用者便可載入網站與應用程式。

一個網域通常會設置多個 NS record，其中包含該網域的主要 NS 與次要 NS。次要 NS 是主 NS 的備份，儲存主 NS 的 DNS record 副本。這麼一來，當一個 NS 故障或不可用時，DNS 伺服器便可以轉往另一個 NS 進行查詢，從而提高網域解析的可靠性和穩定性。此外，當主 NS 的記錄更新時，次要 NS 也會自動同步更新。

在以下兩種情況中會需要更新或修改 NS record：
1. 當需要修改網域的主要 NS 或次要 NS 時
2. 當希望子網域使用與主網域不同的 NS 時

舉例：
假設 `example.com` 網域有兩個名稱伺服器，主要 NS 為 `ns1.exampledns.com`，次要 NS 為 `ns2.exampledns.com`

| 網域名稱       | TTL   | 類型 | 記錄類型 | 名稱伺服器                 |
| -------------- | ----- | ---- | -------- | --------------------------- |
| example.com.   | 86400 | IN   | NS       | ns1.exampledns.com.          |
| example.com.   | 86400 | IN   | NS       | ns2.exampledns.com.          |

- `example.com.`是網域名稱，表示該 NS Record 適用於 `example.com` 網域。
- `86400` 是 TTL（Time To Live），表示該記錄在 DNS 緩存中的存活時間，單位為秒。此處為 86400 秒，即 24 小時。
- `IN` 表示 Internet 類型，NS record 幾乎總是使用 `IN`。
- `NS` 表示該記錄是 NS record，指向 NS。
- `ns1.exampledns.com.` 和 `ns2.exampledns.com.` 是兩個不同的 NS 地址。當用戶查詢 `example.com` 的 DNS record 時，DNS 系統會根據 NS record 轉向這些名稱伺服器進行進一步解析。
## 5. Domain Name vs FQDN vs URL 這三者分別為何？
Domain Name、FQDN（Fully Qualified Domain Name，完全合格網域名稱）以及 URL（Uniform Resource Locator，統一資源定位符）代表不同層次 (由簡易到完整) 的網際網路定位方式，其定義與範例以下表表示：

| 項目                  | 定義                      | 範例                   |
| --------------------- | ------------------------ | ---------------------- |
| Domain Name       | 領域名稱，用來識別一個網站或服務的名稱，可以對應到一個或多個 IP 位址；不涉及各訂的主機或協定| `example.com`|
| FQDN (Fully Qualified Domain Name) | 完整的網域名稱，包含**主機名稱（hostname, 如 `www`）**、 **領域名稱 (domain name)**和**頂級域（Top-level Domain, TLD, 如 `.com`）**，且結尾有「.」，其代表網際網路上唯一的資源位置| `www.example.com.`|
| URL (Uniform Resource Locator)| 統一資源定位器，表示網際網路上標準的資源的位址（Address），包含**領域名稱 (domain name)**、**協定類型（如 `https` 或 `https`）**、**FQDN**、**路徑**、**可選的查詢參數**等| `https://www.example.com/index.html`|
## 6. 為什麼應該要為網站加上憑證？而不是直接用 http 就好？
以 http 開頭的網址其網站所有的資料皆是以未加密的明碼來傳輸，這代表網路上的任何人都可以攔截並讀取使用者與網站之間的通訊，這對於會涉及個人資料與機密資訊的網站而言非常不安全。

而加上 SSL (Secure Sockets Layer) 憑證後，以 https 開頭的網址其網站所有的資料都會經過加密後才傳送，這麼一來便可以保護使用者個人資料與機密資訊，即便攻擊者成功攔截資料，由於加密的存在，攔截者也無法解讀信息。這樣的機制大幅提高使用者資料的安全性，特別是在公共 Wi-Fi 環境中，HTTPS 變得更加不可或缺。

目前市面上加密和保護網路通訊協議的所有規格都是 TLS (Transport Layer Security)，是 SSL 更新後更安全的版本，但習慣上仍會將安全性憑證稱為 SSL (Secure Sockets Layer)。

---

參考資料：
1. [DNS 記錄](https://www.cloudflare.com/zh-tw/learning/dns/dns-records/dns-a-record/)
2. [什麼是 DNS A 記錄？](https://www.cloudflare.com/zh-tw/learning/dns/dns-records/dns-a-record/)
3. [什麼是 DNS NS 記錄？](https://www.cloudflare.com/zh-tw/learning/dns/dns-records/dns-ns-record/)
4. [第八章、領域名稱伺服器 DNS 服務](https://linux.vbird.org/linux_server/rocky9/0210dns.php)
5. [統一資源定位符](https://zh.wikipedia.org/zh-tw/%E7%BB%9F%E4%B8%80%E8%B5%84%E6%BA%90%E5%AE%9A%E4%BD%8D%E7%AC%A6)
6. [為什麼要使用 HTTPS？](https://www.cloudflare.com/zh-tw/learning/ssl/why-use-https/)
7. [HTTPS 、 SSL 是什麼 ？ SSL憑證 對網站來說重要嗎？](https://www.design-hu.com/web-news/ssl.html)