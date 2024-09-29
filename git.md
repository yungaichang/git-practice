# Git 的基本概念
Git 是一套免費開源的分散式版本控制系統（Version Control Stystem），其功能為紀錄檔案的狀態變化，並且可以隨時切換到某個特定版本的狀態，以追蹤文件變更的過程，當中會清楚記錄每個檔案的擁有者、時間與修改異動，是無論個人或團隊在專案開發時管理版本的重要工具。
## 認識物件 (Object)
「物件」是 Git 中一個重要的資料結構，其用途為保存版本庫中的所有檔案與版本紀錄。而「物件」事實上是一個特別的檔案，其產生的過程是利用一個檔案的內容產生一組 SHA1 雜湊值，接著依照這個 SHA1 雜湊值來命名該檔案。

在 Git 中有四種類型的物件：blob, tree, commit 與 tag，底下會分別介紹其內容。
### 1. blob 物件
`Blob` 全名為 Binary Large Object，翻成中文是「二進位大型物件」，是任意大小的資料集合，也是一系列的位元組 (一個位元組係由 8 個位元組成)。任何類型的資料皆可以在 Blob 中，而利用 Blob 所儲存的資料具有不可改寫 (immutable) 的特性。

Blob 這種資料型態的出現最早是應用於資料庫管理中，用來儲存無法存放在常規的資料庫中的大量非結構化物件資料，例如影音、大型文件、圖檔等大型檔案。

在 Git 中 blob 物件是用來儲存檔案內容的最基本單位，僅用來存放原始檔案的實際內容，而不包含其他資訊，例如檔案修改時間、原始檔名、檔案結構等，關於這些其他資訊會被記錄在接下來會介紹的 tree 物件中。前面提及 Blob 為一不可變的物件，因此當某一版本的檔案內容被儲存為 Blob 後，其內容即永遠不會改變；若變更了原始檔案的內容並且提交該變更時，Git 會再生成一個新的 Blob。
### 2. tree 物件

### 3. commit
### 4. tag
## branch
## head
## .git 檔案夾觀察
## About Commit Message

參考資料：
1. [Git 操作簡介](https://www.asustor.com/zh-tw/online/College_topic?topic=245)
2. [第 05 天：了解儲存庫、工作目錄、物件與索引之間的關係](https://github.com/doggy8088/Learn-Git-in-30-days/blob/master/zh-tw/05.md)
3. [第 06 天：解析 Git 資料結構 - 物件結構](https://github.com/doggy8088/Learn-Git-in-30-days/blob/master/zh-tw/06.md)
4. [什麼是 blob](https://hackmd.io/@l-zHCaalQSq59NxFixnqyg/rkvXJlCG5)
5. [[知識篇]WebAPIs - Blob](https://www.yasssssblog.com/2020/10/01/ithome-30-18-blob/) 