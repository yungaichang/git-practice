# Git 的基本概念
Git 是一套免費開源的分散式版本控制系統（Version Control Stystem），當中會清楚記錄每個檔案的擁有者、時間與修改異動。除了紀錄檔案的狀態變化，也可以隨時切換到某個特定版本的狀態，以追蹤文件變更的過程，對無論是個人或團隊而言都是專案開發時重要的管理版本工具。
## 認識物件 (Object)
「物件」是 Git 中一個重要的資料結構，其用途為保存版本庫中的所有檔案與版本紀錄。而「物件」事實上是一個特別的檔案，其產生的過程是利用一個檔案的內容產生一組 SHA1 雜湊值，接著依照這個 SHA1 雜湊值來命名該檔案。

在 Git 中有四種類型的物件：blob, tree, commit 與 tag，底下會分別介紹其內容。
### 1. blob 物件
`blob` 全名為 Binary Large Object，翻成中文是「二進位大型物件」，是任意大小的資料集合，也是一系列的位元組 (一個位元組係由 8 個位元組成)。任何類型的資料皆可以在 blob 中，而利用 blob 所儲存的資料具有不可改寫 (immutable) 的特性。

blob 這種資料型態的出現最早是應用於資料庫管理中，用來儲存無法存放在常規的資料庫中的大量非結構化物件資料，例如影音、大型文件、圖檔等大型檔案。

在 Git 中 blob 物件是用來儲存檔案內容的最基本單位，僅用來存放原始檔案的實際內容，而不包含其他資訊，例如檔案修改時間、檔案名稱、檔案結構等，關於這些其他資訊會被記錄在接下來會介紹的 tree 物件中。前面提及 blob 為一不可變的物件，因此當某一版本的檔案內容被儲存為 blob 後，其內容即永遠不會改變；若變更了原始檔案的內容並且提交該變更時，Git 會再生成一個新的 blob。
### 2. tree 物件
tree 物件在 Git 中用來儲存目錄以及檔案資訊，當中會記錄以下內容：
* 目錄名稱
* 特定目錄下的檔案名稱
* 對應的 blob 物件名稱
* 檔案連結 (symbolic link)
* 檔案權限
* 其他的 tree 物件  
透過 tree 物件，我們可以為特定的版本保存一份反了當下更動的快照 (snapshot)，其內包含了當次所要提交的所有更動。透過下圖可以理解 tree 物件與 blob 物件的關聯性：  
![](https://ithelp.ithome.com.tw/upload/images/20211001/20141010hE96j1i606.png "圖片來源：https://ithelp.ithome.com.tw/articles/10275828")
### 3. commit 物件
commit 物件在 Git 中記錄每次 commit 的資訊，這些資訊包含以下：
* 當次更動的人員、時間、備註訊息 (commit message)
* 特定提交版本所包含的 tree 物件
* 上一層的 commit 物件名稱 (第一次 commit 時不會有此項目)  
透過下圖可以理解 commit 物件、tree 物件與 blob 物件的關係：  
![](https://ithelp.ithome.com.tw/upload/images/20211001/20141010ItUnDWpDWy.png "圖片來源：https://ithelp.ithome.com.tw/articles/10276087")  
需要特別注意的是， commit 物件只會指向 tree 物件，而 tree 物件又會指向一個或多個 blob 物件，也能指向其他的 tree 物件。  
當再次提交 commit 時，關係會出現下圖的變化：  
![](https://ithelp.ithome.com.tw/upload/images/20211001/201410107UhogoDZPE.png "圖片來源：https://ithelp.ithome.com.tw/articles/10276087")  
可以注意到新的 commit 會指向前一個 commit。
### 4. tag 物件
tag 物件通常用來為某個特定版本的 commit 物件標示一個更容易理解的名稱，例如`v.1.0.1`、`v.1.0.2`。除了指向 commit 物件，tag 物件也可以用來指向 blob 物件、tree 物件，以及其他 tag 物件。而 tag 物件又可以分成兩種類型：
1. 輕量標籤 (lightweight tag)
    * tag ref 直接指向特定的 Git 物件
    * 適用於只作為暫時的標籤，而不想儲存額外資訊時
2. 註解標籤 (annotated tag)
    * tag ref 會指向 tag 物件，間接透過此 tag 物件指向一個特定的 Git 物件
    * 會在 Git 中被儲存成完整的物件，且將被計算校驗碼 (checksum)
    * 內容包含以下：
        (1) 建立標籤者 (tagger) 的名字、電子郵件和建立日期
        (2) 標籤訊息 (tagging message)
        (3) 指標 (指向某 Git 物件)  
可以再次透過圖片理解 git 中不同物件之間的關聯：
![](https://raw.githubusercontent.com/yungaichang/origin/2f8a4e50b36b33afcefcd4fe446212e3ca981880/images/git%20tag.png "改編自https://ithelp.ithome.com.tw/articles/10276087")
## branch
## head
## .git 檔案夾觀察
## About Commit Message

參考資料：
1. [Git 操作簡介](https://www.asustor.com/zh-tw/online/College_topic?topic=245)
2. [第 05 天：了解儲存庫、工作目錄、物件與索引之間的關係](https://github.com/doggy8088/Learn-Git-in-30-days/blob/master/zh-tw/05.md)
3. [第 06 天：解析 Git 資料結構 - 物件結構](https://github.com/doggy8088/Learn-Git-in-30-days/blob/master/zh-tw/06.md)
4. [什麼是 blob](https://hackmd.io/@l-zHCaalQSq59NxFixnqyg/rkvXJlCG5)
5. [[知識篇]WebAPIs - blob](https://www.yasssssblog.com/2020/10/01/ithome-30-18-blob/)
6. [Git解密——Tree对象和Commit对象](https://morningspace.github.io/tech/inside-git-2/)
7. [Day17｜【Git】存在 .git 目錄裡的東西 - Blob 物件與 Tree 物件（上）](https://ithelp.ithome.com.tw/articles/10275828)
8. [Day18｜【Git】存在 .git 目錄裡的東西 - Commit 與 Tag（下）](https://ithelp.ithome.com.tw/articles/10276087)
9. [深入 Git：Git 物件儲存 - tag 物件](https://titangene.github.io/article/git-tag-object.html)