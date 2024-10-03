# Git 的基本概念
Git 是一套免費開源的分散式版本控制系統（Version Control Stystem），當中會清楚記錄每個檔案的擁有者、時間與修改異動。除了紀錄檔案的狀態變化，也可以隨時切換到某個特定版本的狀態，以追蹤文件變更的過程，對無論是個人或團隊而言都是專案開發時重要的管理版本工具。
- [認識物件 (Object)](#認識物件-object)
  * [1. blob 物件](#1-blob-物件)
  * [2. tree 物件](#2-tree-物件)
  * [3. commit 物件](#3-commit-物件)
  * [4. tag 物件](#4-tag-物件)
- [branch](#branch)
- [head](#head)
- [.git 檔案夾觀察](#git-檔案夾觀察)
- [About Commit Message](#about-commit-message)
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
  
透過 tree 物件，我們可以為特定的版本保存一份反了當下更動的 _**快照 (snapshot)**_，其內包含了當次所要提交的所有更動。透過下圖可以理解 tree 物件與 blob 物件的關聯性：  
  
![](https://ithelp.ithome.com.tw/upload/images/20211001/20141010hE96j1i606.png "圖片來源：https://ithelp.ithome.com.tw/articles/10275828")
### 3. commit 物件
commit 物件在 Git 中記錄每次 commit 的資訊，這些資訊包含以下：
* 當次更動的人員、時間、備註訊息 (commit message)
* 特定提交版本所包含的 tree 物件
* 上一層的 commit 物件名稱 (第一次 commit 時不會有此項目)  
  
透過下圖可以理解 commit 物件、tree 物件與 blob 物件的關係：  
  
![](https://git-scm.com/book/zh-tw/v2/images/commit-and-tree.png "圖片來源：https://git-scm.com/book/zh-tw/v2/%E4%BD%BF%E7%94%A8-Git-%E5%88%86%E6%94%AF-%E7%B0%A1%E8%BF%B0%E5%88%86%E6%94%AF")  
  
需要特別注意的是， commit 物件只會指向 tree 物件，而 tree 物件又會指向一個或多個 blob 物件，也能指向其他的 tree 物件。  
  
當再次提交 commit 時，關係會出現下圖的變化：  
  
![](https://git-scm.com/book/zh-tw/v2/images/commits-and-parents.png "圖片來源：https://git-scm.com/book/zh-tw/v2/%E4%BD%BF%E7%94%A8-Git-%E5%88%86%E6%94%AF-%E7%B0%A1%E8%BF%B0%E5%88%86%E6%94%AF")  
  
可以注意到新的 commit 會指向前一個 commit。
### 4. tag 物件
tag 物件通常用來為某個特定版本的 commit 物件標示一個更容易理解的名稱，例如 `v.1.0.1`、`v.1.0.2`。除了指向 commit 物件，tag 物件也可以用來指向 blob 物件、tree 物件，以及其他 tag 物件。而 tag 物件又可以分成兩種類型：
1. 輕量標籤 (lightweight tag)
    1. tag ref 直接指向特定的 Git 物件
    2. 適用於只作為暫時的標籤，而不想儲存額外資訊時
      
2. 註解標籤 (annotated tag)
    1. tag ref 會指向 tag 物件，間接透過此 tag 物件指向一個特定的 Git 物件
    2. 會在 Git 中被儲存成完整的物件，且將被計算校驗碼 (checksum)
    3. 內容包含以下：
        1. 建立標籤者 (tagger) 的名字、電子郵件和建立日期
        2. 標籤訊息 (tagging message)
        3. 指標 (指向某 Git 物件)  
          
可以再次透過圖片理解 git 中不同物件之間的關聯：
  
![](https://raw.githubusercontent.com/yungaichang/origin/2f8a4e50b36b33afcefcd4fe446212e3ca981880/images/git%20tag.png "改編自：https://ithelp.ithome.com.tw/articles/10276087")
## branch
branch (分支) 是 Git 中的重要機制，其目的是用來管理實務開發上的不同版本，例如：修改 BUG 版本、測試版本、線穩定版本等。使用 branch 的好處是每一個 branch 彼此都是獨立執行的，可以在各個 branch 上開發與測試不同功能而不會互相影響，待其完成後再將兩個分支 merge (合併)。

branch 是一個指向某特定 commit 物件的可移動輕量級指標，Git 將 branch 的名稱預設為 `master`，而隨著一次次更動後的 commit，`master` 會不斷自動指向最新的一個 commit 物件（如下圖）。

![](https://git-scm.com/book/zh-tw/v2/images/branch-and-history.png "圖片來源：https://git-scm.com/book/zh-tw/v2/%E4%BD%BF%E7%94%A8-Git-%E5%88%86%E6%94%AF-%E7%B0%A1%E8%BF%B0%E5%88%86%E6%94%AF")

當建立一個新的 branch 時，代表建立了一個新的、可移動的指標，例如建立一個叫做 testing 的 branch，會在目前的 commit 物件上新建一個指標（如下圖）。
![](https://git-scm.com/book/zh-tw/v2/images/two-branches.png "圖片來源：https://git-scm.com/book/zh-tw/v2/%E4%BD%BF%E7%94%A8-Git-%E5%88%86%E6%94%AF-%E7%B0%A1%E8%BF%B0%E5%88%86%E6%94%AF")

不過當有多個 branch 時，Git 是怎麼知道我們在哪一支 barnch 上工作呢？這就跟接下來要介紹的 head 息息相關啦！
## head
`HEAD` 是 Git 中的一個特別指標，它會指向我們當前正在工作的本地 branch，因此通常 HEAD 也可以被看作是 **目前所在分支**，即為當前 branch 的別名。

延續上一張圖片中的例子，當我們建立 testing 這個 branch 時，因為僅「建立」而尚未「切換」，所以此時 HEAD 仍會指向初始的 master branch（如下圖）。

![](https://git-scm.com/book/zh-tw/v2/images/head-to-master.png "圖片來源：https://git-scm.com/book/zh-tw/v2/%E4%BD%BF%E7%94%A8-Git-%E5%88%86%E6%94%AF-%E7%B0%A1%E8%BF%B0%E5%88%86%E6%94%AF")

當我們執行「切換」到 testing 的動作時，HEAD 就會轉為指向 testing branch（如下圖），代表我們當前的工作環境是在 testing 這支 branch 中。

![](https://git-scm.com/book/zh-tw/v2/images/head-to-testing.png "圖片來源：https://git-scm.com/book/zh-tw/v2/%E4%BD%BF%E7%94%A8-Git-%E5%88%86%E6%94%AF-%E7%B0%A1%E8%BF%B0%E5%88%86%E6%94%AF")

此時，若我們執行一次 commit，會發現被 HEAD 指向的 testing branch 會往前移動，反觀 master 這支 branch 則會停留在當初「切換」時的 commit 物件上（如下圖）。

![](https://git-scm.com/book/zh-tw/v2/images/advance-testing.png "圖片來源：https://git-scm.com/book/zh-tw/v2/%E4%BD%BF%E7%94%A8-Git-%E5%88%86%E6%94%AF-%E7%B0%A1%E8%BF%B0%E5%88%86%E6%94%AF")

若我們想要針對 master 這支 branch 的內容做更動，可以再「切換」回 master（如下圖）。

![](https://git-scm.com/book/zh-tw/v2/images/checkout-master.png "圖片來源：https://git-scm.com/book/zh-tw/v2/%E4%BD%BF%E7%94%A8-Git-%E5%88%86%E6%94%AF-%E7%B0%A1%E8%BF%B0%E5%88%86%E6%94%AF")

這個時候我們若做出一些更動並且 commit，便會發現專案出現兩個走向，master 與 testing 中往不同方向前進，兩邊的更動會被隔離在不同的 branch 中，而不會互相影響（如下圖）。接下來便可以在不同的 branch 中反覆切換，並在適當的時點將它們合併在一起。

![](https://git-scm.com/book/zh-tw/v2/images/advance-master.png "圖片來源：https://git-scm.com/book/zh-tw/v2/%E4%BD%BF%E7%94%A8-Git-%E5%88%86%E6%94%AF-%E7%B0%A1%E8%BF%B0%E5%88%86%E6%94%AF")
## .git 檔案夾觀察
.git 檔案夾中儲存了 Git 對專案的所有紀錄與操作的資訊，接下來我們會從 `git init` 開始逐步操作，並且觀察檔案夾的變化的過程。
### 1. git init
為專案建立資料夾 "git-workspace" 後，從該資料夾開啟 cmd，並且執行 `git init`，便會在當前的資料夾下生成一個 .git 資料夾。這個資料夾中包含了 Git 用來追蹤版本控制的所有必要資訊，例如 `HEAD`、`config`、`description`、`hooks/`、`info/`、`objects/`、`refs/`，可以透過執行 `tree .git` 來查看 .git 資料夾內的結構。其中，先前介紹過的 `HEAD` 目前是指向 master 這支預設名稱的 branch。

![](https://github.com/yungaichang/origin/blob/main/images/git%20init%20cmd.png)

### 2. git add & git commit
下一步我們可以在 cmd 執行以下指令，於同一個資料夾中新增一個內容為 "Hello World" 的文字檔：
```
echo Hello World > test.txt
```
執行以下指令將該檔案加入 Git 暫存區：
```
git add test.txt
```

![](https://github.com/yungaichang/origin/blob/main/images/git%20add.png)

可以觀察到在 `git add` 後 `objects` 這個資料夾底下出現了 e7 這個資料夾，這個檔案產生的過程如下：
1. Git 會讀取所執行 `git add` 的檔案，並將其內容壓縮
2. 使用 SHA-1 演算法對檔案內容生成一個用來唯一標識檔案的雜湊值
3. 將這個壓縮過的內容存放為一個 _**blob 物件**_ ，並且根據其雜湊值來命名，存放於 `.git/objects/` 下

接著用以下指令 commit 此次更動，同時附上 commit message：
```
git commit -m "My first commit"
```

![](https://github.com/yungaichang/origin/blob/main/images/git%20commit.png)

當執行 `git commit` 時，可以觀察到 `objects` 這個資料夾底下出現了 d0 與 53 這兩個資料夾，它們分別是：
* 由 Git 生成的 _**tree 物件**_ ：來記錄專案目錄的結構
* 由 Git 生成的 _**commit 物件**_ ：包含了提交的訊息、提交時間、作者資訊以及指向該提交的 tree 物件
而這兩個檔案的名稱也是以 SHA-1 演算法生成的雜湊值來命名。

我們可以執行 `type .git\refs\heads\master` 來查看所 commit 的快照 (snapshot)，便可以看到 commit 物件的雜湊值。

![](https://github.com/yungaichang/origin/blob/main/images/master%20hash.png)
### 3. git branch & git switch
接下來使用以下指令建立一個叫做 testing 的 branch：
```
git branch testing
```
此時若去到 .git\refs\heads 的目錄下，可以看到新增了一個 testing 的檔案，在這個檔案中會記錄這個 branch 所對應的 commit 物件。

接著切換到新建立的 testing branch：
```
git switch testing
```
執行 `type .git\HEAD` 可以發現 HEAD 指標從原先指向的 master 轉而指向 testing。

![](https://github.com/yungaichang/origin/blob/main/images/git%20branch.png)
### 4. git merge
我們在 testing 這個 branch 中重複 2. 的動作，`.git/objects/` 會再次增加新的物件，記錄新的 commit 和檔案內容。

![](https://github.com/yungaichang/origin/blob/main/images/git%20add%20commit%20testing.png)

接著切換回 master branch，並且使用以下指令合併 testing branch：
```
git merge testing
```
此時執行 `type .git\refs\heads\master` 會發現其指向新的 commit 物件。（如下圖）

![](https://github.com/yungaichang/origin/blob/main/images/merge.png)

而在 `.git/objects/` 也可以發現新建立了一個新的合併 commit 物件，用來保存合併的狀態。
### git remote add & git push
最後，使用以下指令設置一個遠端 repo，並且將本地端的 commit push 到遠端：
```
git remote add origin <遠端倉庫URL>
```
```
git push -u origin master
```
此時 `.git` 資料夾內部的結構沒有發現太大變化，不過執行 `type .git\config` 可以發現其中記錄了遠端 repo 的資訊。

![](https://github.com/yungaichang/origin/blob/main/images/push%20to%20repo.png)
## About Commit Message
Commit message 是開發者在 commit 程式碼更動時撰寫的一段訊息，用來描述此次 commit 的變更內容。良好的 commit message 能夠幫助開發者團隊了解每次提交的具體變更，便於日後的追蹤和維護。

寫 Commit message 有一些基本須注意的原則：
* 不要一次 commit 所有更動，不同意義的更動應該獨立 commit，以讓更動與 commit message 相互對應
* 清楚具體地紀錄每一次更動的原因（WHY）與內容（WHAT），以讓日後進行協作和維護的人員更快地了解情況
* 團隊規劃統一的 commit message style，確保格式一致

一般而言，commit meassage 含了以下幾個部分：

1. **主標題（Summary / Subject line）：**
    - 用 type 歸結 commit 的類別，type 僅包含以下類別：
        - feat: feature，新增或修改功能時使用
        - fix: bug fix，修補 bug 時使用
        - docs: documentation，對文件進行修改時使用
        - style: 進行不影響程式執行的格式調整時使用，例如：white-space, formatting, missing semi colons 等
        - refactor: 重構現有程式碼，而不增加新功能或修復 bug 時使用
        - perf: 進行效能優化時使用 (A code change that improves performance)
        - test: 增加測試用例時使用 (when adding missing tests)
        - chore: 對建構程序或輔助工具進行修改時使用 (maintain)
        - revert: 需要撤回之前的 commit 時使用，例如：revert: type(scope): subject (回覆版本：xxxx)
    - 概述此次提交的主要變更
    - 一般建議不超過 50 個字元，且在結尾不加句號
    - 使用祈使句，例如 “Add new feature for user login”
   
2. **詳細描述（Description / Body）：**
    - 用來補充說明此次提交的具體變更內容。
    - 可以多行描述，尤其當變動比較複雜時。
    - 可以包括變更的原因、背景，以及對後續代碼或功能的影響。
    - 詳細描述每一個改動的動機。

3. **註腳（Footer）：**
    - 有時候需要加入相關問題、bug 編號，或者與特定工具的自動化集成（例如關閉 issue 的關鍵字：`Fixes #123`）。
    - 如果這次提交涉及到破壞性變更，應在此註明 `BREAKING CHANGE: 描述變更的細節`。

範例：
```
fix: resolve api 500 error when fetching user data

- Fix issue causing a 500 internal server error on the user profile endpoint
- Add error handling for missing user data

Fixes #105
```

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
10. [3.1 使用 Git 分支 - 簡述分支](https://git-scm.com/book/zh-tw/v2/%e4%bd%bf%e7%94%a8-Git-%e5%88%86%e6%94%af-%e7%b0%a1%e8%bf%b0%e5%88%86%e6%94%af#rdivergent_history)
11. [分支建立(git branch)](https://w3c.hexschool.com/git/a8ee6eee)
12. [Day16｜什麼是 HEAD ?](https://ithelp.ithome.com.tw/articles/10275130)
13. [Git Commit Message 這樣寫會更好，替專案引入規範與範例](https://wadehuanglearning.blogspot.com/2019/05/commit-commit-commit-why-what-commit.html)