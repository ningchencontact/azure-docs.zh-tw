區域備援儲存體 (ZRS) (預覽) 的設計目的為簡化高可用性應用程式的開發。 ZRS 提供儲存體物件在指定一年中至少 99.9999999999% (12 個 9) 的耐久性。 ZRS 會以同步方式將您的資料複寫到多個可用性區域。 針對不接受停機時間的情況 (例如交易式應用程式)，請考慮使用 ZRS。

ZRS 讓客戶即使在單一區域無法使用或無法修復時，仍然能夠讀取和寫入資料。 插入和更新資料會以同步方式進行，一致性極強。   

目前在下列區域提供 ZRS 預覽，其他區域也會陸續推出：

- 美國東部 2 
- 美國中部 
- 法國中部 (此區域目前為預覽狀態。 請參閱[具有 Azure 可用性區域的 Microsoft Azure 預覽版現在已在法國開放使用](https://azure.microsoft.com/blog/microsoft-azure-preview-with-azure-availability-zones-now-open-in-france)，以要求使用。)