# 名詞解釋
## blob
`Blob` 全名為 Binary Large Object，翻成中文是「二進位大型物件」，是任意大小的資料集合，也是一系列的位元組 (一個位元組係由 8 個位元組成)。任何類型的資料皆可以在 Blob 中，而利用 Blob 所儲存的資料具有不可改寫 (immutable) 的特性。

Blob 這種資料型態的出現最早是應用於資料庫管理中，用來儲存無法存放在常規的資料庫中的大量非結構化物件資料，例如影音、大型文件、圖檔等大型檔案。

另一個與 Blob 相關的概念是 Blob Storage，是專門用來儲存二進位大型物件檔案所使用的雲端儲存服務。這項服務會將大量資料儲存在資料湖泊 (data lake) 的非階層式儲存區域中，其具有可擴展、雲端原生、允許用多種程式語言存取、分層定價的優點。

Blob Storage 的每個儲存帳戶下有獨立的 URL 謬空間，命名空間下有容器 (container)，資料便是存放在容器內。
![This is an alt text.](https://i.imgur.com/P16DSy7.png "圖片來源: https://hackmd.io/@l-zHCaalQSq59NxFixnqyg/rkvXJlCG5")

Blob Storage 的功能有以下幾點：
* 直接提供圖檔或文件給瀏覽器
* 儲存檔案供分散式存取
* 串流影音
* 寫入記錄檔
* 儲存備份和還原、災害復原和封存資料
## tree

## commit
## branch
## head
# .git 檔案夾觀察
# About Commit Message

參考資料：
1. [什麼是 blob](https://hackmd.io/@l-zHCaalQSq59NxFixnqyg/rkvXJlCG5)
2. [Storage Service (1): BLOB Storage](https://ithelp.ithome.com.tw/articles/10077453?sc=rss.iron)
3. [[知識篇]WebAPIs - Blob](https://www.yasssssblog.com/2020/10/01/ithome-30-18-blob/)
4. [Blob 儲存體是什麼？](https://www.cloudflare.com/zh-tw/learning/cloud/what-is-blob-storage/)