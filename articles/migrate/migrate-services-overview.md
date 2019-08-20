---
title: 關於 Azure Migrate | Microsoft Docs
description: 提供 Azure Migrate 服務的概觀。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 07/11/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 03918976935f9071ba2a7951e29195118943a8ef
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/08/2019
ms.locfileid: "68845828"
---
# <a name="about-azure-migrate"></a>關於 Azure Migrate

此文章提供 Azure Migrate 的快速概觀。

Azure Migrate 可協助您移轉至 Azure。 Azure Migrate 能提供集中式的中樞，以追蹤針對 Azure 的內部部署基礎結構、應用程式與資料的探索、評量及移轉。 該中樞能提供 Azure 工具以進行評量和移轉，也提供協力廠商獨立軟體廠商 (ISV) 供應項目。 它提供：

- **整合式移轉平台**：使用單一入口網站來啟動、執行及追蹤您移轉至 Azure 的旅途。
- **各種工具**：Azure Migrate 提供原生工具，並與其他 Azure 服務及 ISV 工具整合。 根據您的組織需求，選取正確的評量及移轉工具。 
- **Azure Migrate 伺服器評量**：使用伺服器評量工具來評量內部部署 VMware VM 和 Hyper-V VM 以針對 Azure 進行移轉。
- **Azure Migrate 伺服器移轉**：使用伺服器移轉工具來將內部部署 VMware VM、Hyper-V VM、雲端 VM，以及實體伺服器移轉至 Azure。
- **Azure Migrate 資料庫評量**：評量內部部署資料庫以移轉至 Azure。
- **Azure Migrate 資料庫移轉**：將內部部署資料庫移轉至 Azure。


## <a name="azure-migrate-versions"></a>Azure Migrate 版本

Azure Migrate 服務有兩個版本：

- **目前的版本**：使用此版本來建立 Azure Migrate 專案、探索內部部署電腦，以及協調評量與移轉。 [深入了解](whats-new.md)此版本的新功能。
- **先前版本**：如果您使用的是舊版 Azure Migrate (僅支援內部部署 VMware VM 的評量)，您現在應該改為使用目前的版本。 您已無法使用舊版來建立 Azure Migrate 專案，且我們建議您不要執行新的探索。 若要存取現有專案，請在 Azure 入口網站 > [所有服務]  中搜尋 **Azure Migrate**。 在 Azure Migrate 儀表板上，會有可以存取舊 Azure Migrate 專案的通知與連結。

## <a name="isv-integration"></a>ISV 整合

除了原生 Azure 工具之外，Azure Migrate 還與數個 ISV 供應項目整合。 您需識別自己所需的工具，並將它新增至 Azure Migrate 專案。 您可以跨越 Azure 和 ISV 工具，從 Azure Migrate 專案內集中追蹤您的移轉旅途。

**ISV** | **功能**
--- | ---
[Cloudamize](https://www.cloudamize.com/platform) | 評估
[Device 42](https://docs.device42.com/) \(英文\) | 評估
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) \(英文\) | 評估
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) \(英文\) | 評估
[Corent Technology](https://www.corenttech.com/AzureMigrate/) \(英文\) | 評估和移轉
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) \(英文\) | 移轉

### <a name="selecting-an-isv-tool"></a>選取 ISV 工具

在將 ISV 工具新增至 Azure Migrate 專案之後，請根據 ISV 原則來取得授權，或是註冊免費試用來開始使用該工具。 每個工具中都會有連線至 Azure Migrate 的選項。 遵循工具指示與文件來搭配 Azure Migrate 連線至該工具。 ISV 工具會根據 ISV 授權模型進行授權。

## <a name="azure-migrate-server-assessment"></a>Azure Migrate 伺服器評量

Azure Migrate 伺服器評量能探索並評量內部部署 VMware VM 和 Hyper-V VM，以針對 Azure 進行移轉。 它能協助識別下列項目：

- **Azure 移轉整備程度：** 評量內部部署電腦是否已準備好移轉至 Azure。
- **Azure 大小調整：** 估計 Azure VM 在移轉之後的大小。
- **Azure 成本估計：** 估計在 Azure 中執行內部部署伺服器的成本。
- **相依性視覺效果：** 識別跨伺服器相依性，以及將相依伺服器移轉至 Azure 的最佳方法。 

伺服器評量會使用您以內部部署方式部署，並向伺服器評量註冊的輕量型設備。

- 該設備會探索內部部署電腦，連線至伺服器評量，然後將中繼資料和與效能相關的資料持續傳送至 Azure Migrate。
- 探索為無代理程式。 不需要在被探索的 VM 上安裝任何項目。
- 探索到電腦之後，您通常會將想要一起移轉的 VM 分組在一起。
- 您可建立群組的評估。 您接著可以分析該評量，以找出您的移轉策略。

## <a name="azure-migrate-server-migration"></a>Azure Migrate 伺服器移轉

Azure Migrate 伺服器移轉可協助您將內部部署 VMware VM、Hyper-V VM、實體伺服器、其他虛擬化電腦，以及公用雲端 VM 移轉至 Azure。 您可以先對電腦進行評量再移轉它們，也可以不進行評量便移轉。 

## <a name="azure-migrate-database-assessment"></a>Azure Migrate 資料庫評量

Azure Migrate 會與 Data Migration Assistant (DMA) 整合來評量內部部署 SQL Server 資料庫，以移轉至 Azure SQL DB、Azure SQL 受控執行個體，或是執行 SQL Server 的 Azure VM。 DMA 能提供潛在移轉封鎖問題的相關資訊。 它能識別不支援的功能，以及您在移轉後可以利用的新功能，並協助您識別資料庫移轉的正確路徑。 [深入了解](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)。


## <a name="azure-migrate-database-migration"></a>Azure Migrate 資料庫移轉

Azure Migrate 與 Azure 資料庫移轉服務 (DMS) 整合，來將內部部署資料庫移轉至 Azure。 使用 DMS 來將內部部署資料庫移轉至執行 SQL 的 Azure VM、Azure SQL DB 與 Azure SQL 受控執行個體。 [深入了解](https://docs.microsoft.com/azure/dms/dms-overview)。

## <a name="web-app-assessment-and-migration"></a>Web 應用程式評量及移轉

從 Azure Migrate 中樞，您可以評量內部部署 Web 應用程式，並將其移轉至 Azure。

- **在線上評量 Web 應用程式**：使用 Azure App Service 移轉小幫手來評量內部部署網站，以移轉至 Azure App Service。
- **移轉 Web 應用程式**：使用 Azure App Service 移轉小幫手來將 .NET 與 PHP Web 應用程式移轉至 Azure。

[深入了解。](https://appmigration.microsoft.com/)

## <a name="offline-data-migration"></a>離線資料移轉

您可以使用資料箱離線產品系列來將大量資料移至 Azure。 [深入了解](https://docs.microsoft.com/azure/databox/)

## <a name="next-steps"></a>後續步驟

- 請嘗試進行我們的教學課程來評量 [VMware VM](tutorial-assess-vmware.md) 與 [Hyper-V VM](tutorial-assess-hyper-v.md)。
- [深入了解](https://azure.microsoft.com/pricing/details/azure-migrate/) Azure Migrate 定價。
- 針對 Azure Migrate [檢閱相關常見問題](resources-faq.md)。
