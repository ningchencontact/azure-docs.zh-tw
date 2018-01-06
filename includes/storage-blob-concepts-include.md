## <a name="what-is-blob-storage"></a>什麼是 Blob 儲存體？
Azure Blob 儲存體是一項儲存大量非結構化物件資料的服務 (例如文字或二進位資料)，全球任何地方都可透過 HTTP 或 HTTPS 來存取這些資料。 您可以使用 Blob 儲存體向全球公開資料，或私下儲存應用程式資料。

Blob 儲存體的一般用途包括：

* 服務映像或直接至瀏覽器的文件。
* 儲存分散式存取的檔案。
* 視訊和音訊資料流處理。
* 儲存的備份、 還原、 嚴重損壞修復及封存的資料。
* 儲存資料以供分析由內部部署或 Azure 託管服務。

## <a name="blob-service-concepts"></a>Blob 服務概念
Blob 服務包含下列元件：

![Blob 服務架構圖](./media/storage-blob-concepts-include/blob1.png)

* **儲存體帳戶：**一律透過儲存體帳戶來存取 Azure 儲存體。 這個儲存體帳戶可以是**一般用途儲存體帳戶**或**Blob 儲存體帳戶**，這專門用來儲存物件或 blob。 如需詳細資訊，請參閱[關於 Azure 儲存體帳戶](../articles/storage/common/storage-create-storage-account.md)。
* **容器：** 容器提供一組 Blob 的群組。 所有 Blob 都必須放在容器中。 一個帳戶可以包含的容器不限數量。 容器可以儲存無限制的 Blob。 請注意，容器名稱必須是小寫。
* **Blob：** 任何類型和大小的檔案。 Azure 儲存體提供三種類型的 blob： 區塊 blob、 附加 blob 和分頁 blob。
  
    *區塊 Blob* 很適合儲存文字或二進位檔案，例如文件和媒體檔案。 單一區塊 blob 可包含高達 50,000 個區塊，每個區塊最大為 100 MB，大小總計稍高於 4.75 TB (100 MB X 50,000)。 

    *附加 Blob* 和區塊 Blob 相似，但已針對附加作業最佳化，因此適合用於記錄的情況。 單一附加 blob 可包含高達 50,000 個區塊，每個區塊最大為 4 MB，大小總計稍高於 195 GB (4 MB X 50,000)。
  
    *分頁 Blob* 大小可達 1 TB，且在頻繁進行讀寫操作時更有效率。 Azure 虛擬機器會使用分頁 blob 做為作業系統和資料磁碟。
  
    如需有關命名容器和 blob 的詳細資訊，請參閱[命名和參考容器、 blob 及中繼資料](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata)。

