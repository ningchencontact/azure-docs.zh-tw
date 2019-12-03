---
title: 關於 Azure Migrate
description: 了解 Azure Migrate 服務的伺服器評估與移轉。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 11/19/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: a64e7366281f15c94d6551c1f7be27f461737634
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185799"
---
# <a name="about-azure-migrate"></a>關於 Azure Migrate

此文章提供 Azure Migrate 的快速概觀。

Azure Migrate 可協助您將企業從內部部署環境遷移至 Azure。 Azure Migrate 能提供集中式的中樞，以追蹤針對 Azure 的內部部署基礎結構、應用程式與資料的探索、評量及移轉。  Azure Migrate 提供：

- **整合式移轉平台**：單一入口網站，用來啟動、執行及追蹤您遷移至 Azure 的旅途。
- **各種工具**：此中樞會提供 Azure Migrate 工具評量和移轉，並與其他 Azure 服務以及其他工具和獨立軟體廠商 (ISV) 供應項目整合。
- **工作負載**：Azure Migrate 會提供下列各項的評量和移轉：
    - **伺服器**：使用 Azure Migrate Server 評量、Azure Migrate 伺服器移轉及其他工具，評定伺服器並將其遷移至 Azure VM。
    - **資料庫**：利用 Microsoft 和 ISV 工具來評定內部部署資料庫，並將其遷移至 Azure SQL DB 或 Azure SQL 受控執行個體。
    - **Web 應用程式**：使用 Azure App Service Assistant 來評量內部部署 Web 應用程式，並將其遷移至 Azure App Service。
    - **虛擬桌面**：使用 ISV 工具來評定內部部署虛擬桌面基礎結構 (VDI)，並將其遷移至 Azure 中的 Windows 虛擬桌面。
    - **Data**：使用 Azure 資料箱系列產品，以快速且符合成本效益的方式將大量資料遷移至 Azure。

## <a name="azure-migrate-versions"></a>Azure Migrate 版本

Azure Migrate 服務目前有兩個版本：

- **目前的版本**：使用此版本來建立 Azure Migrate 專案、探索內部部署電腦，以及協調評量與移轉。 [深入了解](whats-new.md)此版本的新功能。
- **先前版本**：如果您使用舊版 Azure Migrate (僅支援內部部署 VMware VM 的評量)，則您現在應該使用目前的版本。 您已無法使用舊版來建立 Azure Migrate 專案，且我們建議您不要執行新的探索。 若要存取現有專案，請在 Azure 入口網站 > [所有服務]  中搜尋 **Azure Migrate**。 在 Azure Migrate 儀表板上，會有可以存取舊 Azure Migrate 專案的通知與連結。



## <a name="isv-integration"></a>ISV 整合

除了原生 Azure 工具之外，Azure Migrate 還與數個 ISV 供應項目整合。 

**ISV** | **功能**
--- | ---
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) \(英文\) | 移轉伺服器
[Cloudamize](https://www.cloudamize.com/platform) | 評定伺服器
[Corent Technology](https://www.corenttech.com/AzureMigrate/) \(英文\) | 評估和遷移伺服器
[Device 42](https://docs.device42.com/) \(英文\) | 評定伺服器
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | 評定 VDI
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | 移轉伺服器
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) \(英文\) | 評定伺服器
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) \(英文\) | 評定伺服器和資料庫

## <a name="azure-tool-integration"></a>Azure 工具整合

下表摘要說明 Azure Migrate 中整合的其他工具。

**工具** | **詳細資料**
--- | ---
Azure Migrate：伺服器評量 | 評定伺服器
Azure Migrate：伺服器移轉 | 移轉伺服器
Database Migration Assistant (DMA) | 評定資料庫
資料庫移轉服務 (DMS) | 移轉資料庫
Movere | 評定伺服器
Web 應用程式移轉小幫手 | 評定及遷移 Web 應用程式



### <a name="selecting-a-tool"></a>選取工具

識別自己所需的工具，並將它新增至 Azure Migrate 專案。

- 如果您要新增 ISV 工具或 Movere：
    - 根據工具原則來取得授權，或註冊免費試用來開始著手。 這些工具會根據 ISV 或工具授權模型進行授權。
    - 每個工具中都會有連線至 Azure Migrate 的選項。 遵循工具指示與文件來搭配 Azure Migrate 連線至該工具。
- 您可以跨越 Azure 和其他工具，從 Azure Migrate 專案內集中追蹤您的移轉旅途。



## <a name="azure-migrate-server-assessment-tool"></a>Azure Migrate 伺服器評量工具

Azure Migrate：伺服器評量工具能探索並評量內部部署 VMware VM、Hyper-V VM 和實體伺服器，以針對 Azure 進行移轉。 它能協助識別下列項目：

- **Azure 移轉整備程度：** 評量內部部署電腦是否已準備好移轉至 Azure。
- **Azure 大小調整：** Azure VM 在移轉之後的估計大小。
- **Azure 成本估計：** 在 Azure 中執行內部部署伺服器的估計成本。
- **相依性視覺效果：** 跨伺服器的相依性 (如果已啟用相依性視覺效果)，也是將從屬伺服器移至 Azure 的最佳方式。

伺服器評量會使用您以內部部署方式部署，並向伺服器評量註冊的輕量型設備。

- 設備會探索內部部署機器。
- 其會連線到伺服器評量，並將機器中繼資料和效能資料持續傳送至 Azure Migrate。
- 設備探索無須代理程式。 不需要在探索的機器上安裝任何項目。
- 探索之後，您會將探索到的機器收集到群組中。 您通常會將要遷移的機器彙集在一起。
- 您可建立群組的評估。 您接著可以分析該評量，以找出您的移轉策略。

## <a name="azure-migrate-server-migration-tool"></a>Azure Migrate 伺服器移轉工具

Azure Migrate：伺服器移轉工具可協助您將內部部署 VMware VM、Hyper-V VM、實體伺服器、其他虛擬化電腦，以及公用雲端 VM 移轉至 Azure。 您可以先對電腦進行評量再移轉它們，也可以不進行評量便移轉。


## <a name="database-migration-assistant"></a>資料庫移轉小幫手

Azure Migrate 會整合 Microsoft Data Migration Assistant (DMA) 來評量內部部署 SQL Server 資料庫，進而遷移至 Azure SQL DB、Azure SQL 受控執行個體，或是執行 SQL Server 的 Azure VM。 DMA 能提供潛在移轉封鎖問題的相關資訊。 它能識別不支援的功能，以及您在移轉後可以利用的新功能，並協助您識別資料庫移轉的正確路徑。 [深入了解](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)。

## <a name="database-migration-service"></a>Database Migration Service

Azure Migrate 與 Azure 資料庫移轉服務 (DMS) 整合，來將內部部署資料庫移轉至 Azure。 使用 DMS 來將內部部署資料庫移轉至執行 SQL 的 Azure VM、Azure SQL DB 與 Azure SQL 受控執行個體。 [深入了解](https://docs.microsoft.com/azure/dms/dms-overview)。

## <a name="movere"></a>Movere

 
Movere 是一個 SaaS 平台，可在一天內精確呈現整個 IT 環境，藉此提升商業智慧。 隨著組織成長、變更和數位最佳化，此解決方案讓企業有信心無論平台、應用程式或地理位置為何，都能掌握其環境的可見度和控制權。 Movere 已由 Microsoft [收購](https://azure.microsoft.com/blog/microsoft-acquires-movere-to-help-customers-unlock-cloud-innovation-with-seamless-migration-tools/)，不再以獨立供應項目的形式出售。  您可透過 Microsoft 解決方案評量和雲端經濟學方案取得 Movere。 [深入了解](https://www.movere.io) Movere。 如有任何疑問，請將其提交給：movereq@microsoft.com 或連絡您的 Microsoft 代表。

我們鼓勵您同時查看 Azure Migrate，這是我們內建的移轉服務。 Azure Migrate 會提供中央中樞，以簡化您遷移至雲端的工作。 此中樞會針對不同的工作負載 (包括實體和虛擬伺服器、資料庫和應用程式) 提供全方位的支援。 端對端可見度可讓您輕鬆地追蹤探索、評量和移轉的進度。 Azure Migrate 內建 Azure 和合作夥伴 ISV 工具，也提供了廣泛的功能，包括虛擬和實體伺服器探索、以效能為基礎的適當大小調整、成本規劃、匯入型評量和無代理程式應用程式相依性分析。 如果想尋求專家協助您開始使用該服務，Microsoft 有技術熟練的 [Azure 專家受控服務提供者](https://azure.microsoft.com/partners)可引導您完成旅程。 查看 [Azure Migrate 網站](https://azure.microsoft.com/services/azure-migrate/)。 
 

## <a name="web-app-migration-assistant"></a>Web 應用程式移轉小幫手

Azure Migrate 會與 Azure App Service 移轉小幫手整合。 在 Azure Migrate 中樞裡，您可以使用此小幫手來評量內部部署 Web 應用程式，並將其遷移至 Azure，如下所示：

- **在線上評量 Web 應用程式**：使用 Azure App Service 移轉小幫手來評量內部部署網站，以移轉至 Azure App Service。
- **移轉 Web 應用程式**：使用 Azure App Service 移轉小幫手來將 .NET 與 PHP Web 應用程式移轉至 Azure。

[深入了解](https://appmigration.microsoft.com/)小幫手。



## <a name="offline-data-migration"></a>離線資料移轉

您可以使用 Azure 資料箱產品來將大量資料離線移至 Azure。 [深入了解](https://docs.microsoft.com/azure/databox/)

## <a name="next-steps"></a>後續步驟

- 請嘗試進行我們的教學課程來評量 [VMware VM](tutorial-assess-vmware.md) 與 [Hyper-V VM](tutorial-assess-hyper-v.md)。
- [深入了解](https://azure.microsoft.com/pricing/details/azure-migrate/) Azure Migrate 定價。
- 針對 Azure Migrate [檢閱相關常見問題](resources-faq.md)。
