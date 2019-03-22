---
title: Dynamics 365 for Customer Engagement 應用程式供應項目試用產品索引標籤 - Azure Marketplace | Microsoft Docs
description: 如何在 AppSource Marketplace 上設定 Dynamics 365 for Customer Engagement 應用程式供應項目的產品試用。
services: Azure, Marketplace, AppSource, Cloud Partner Portal, Dynamics 365 for Customer Engagement
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/25/2018
ms.author: pbutlerm
ms.openlocfilehash: 373312b4c7f05fe41c9ca8165b8ff6f1b0e56f1b
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2019
ms.locfileid: "57454964"
---
# <a name="dynamics-365-for-customer-engagement-application-test-drive-tab"></a>Dynamics 365 for Customer Engagement 應用程式試用產品索引標籤

使用 [試用產品] 索引標籤，為您的客戶建立試用體驗。  它可讓客戶以自我引導方式實際試用您供應項目的主要功能與優點，而且是以實際的實作案例來示範的。  在可用的試用選項中，試用產品是獲得高品質潛在客戶及提高那些潛在客戶轉換為付費客戶之轉換率最有效的方式。  如需詳細資訊，請參閱[什麼是試用產品？](../test-drive/what-is-test-drive.md)

Dynamics 365 應用程式的試用產品體驗會自動執行為 Microsoft 裝載的解決方案。  如需詳細資訊，請參閱[裝載的試用產品](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/hosted-test-drive)。

[試用產品] 索引標籤有三個可用區段：**試用產品**、**詳細資料**和**技術設定**。  在您啟用「試用產品」功能後，才會顯示最後兩個區段。  欄位名稱上附加的星號 (*) 表示此為必填欄位。 


## <a name="test-drive-section"></a>試用產品區段

若要啟用這項功能，請選取 [是] 以**啟用試用產品**。


## <a name="details-section"></a>詳細資料區段

您將在 [詳細資料] 區段中提供基本的試用產品資訊。   

![試用產品的詳細資料區段](./media/test-drive-tab-details.png)

下表說明為 Dynamics 365 應用程式設定「試用產品」所需的欄位。

|      欄位                    |    描述                  |
|    ---------                  |  ---------------                |
|      描述              |   說明可在試用產品上完成的動作。 您可以使用基本 HTML 標記來設定這個說明的格式。 例如，&lt;p&gt;、&lt;em&gt;、&lt;ul&gt;、&lt;li&gt;、&lt;ol&gt; 及標題。  |
|  使用者手冊                  |   上傳客戶可用來逐步進行試用產品體驗的使用者手冊。 這份文件必須是 .pdf 檔案。              |
|  試用產品示範影片 (選擇性) |  您可以提供試用產品的影片逐步解說。 客戶可以先觀看這段影片，然後再取得試用產品。 提供影片在 YouTube 或 Vimeo 上的 URL。 如果您選取 [+ 新增影片]，系統將會提示您提供下列資訊：<ul><li>名稱</li><li>URL</li><li>縮圖 (.png 格式，533 x 324 像素)</li></ul>  |
|   |   |


## <a name="technical-configuration-section"></a>技術設定區段

在此區段中，您將提供關於試用產品的技術詳細資料。

![試用產品的詳細資料區段](./media/test-drive-tab-tech-config.png)

其中的欄位具有下列用途：

|      欄位                    |    描述                  |
|    ---------                  |  ---------------                |
| 試用產品的類型            | 選擇 [Microsoft 裝載 (Dynamics 365 for Customer Engagement)]。  |
| 並行試用產品數上限    | 在任何指定的時間點處於作用中狀態的並行試用產品執行個體數目。 當使用者的試用產品為作用中時，每個使用者都會取用 Dynamics 授權，因此您必須確定您至少有這麼多的 Dynamics 授權可供試用產品使用者使用。 建議值為 3-5 個。  |
| 試用產品持續時間 (小時)   | 使用者的試用產品執行個體將處於作用中狀態時數上限。 經過這段期間後，即會從您的租用戶取消佈建執行個體。 建議值為 2-24 小時，視您應用程式的複雜度而定。 如果使用者已用完時間而想要再次評估，可以隨時要求另一次的試用產品。  |
| 執行個體 URL                  | 試用產品一開始瀏覽到的 URL。 這通常是您的應用程式與範例資料安裝所在的 Dynamics 365 執行個體的 URL。  |
| Azure AD 租用戶識別碼            | 您 Dynamics 365 執行個體的 Azure 租用戶 GUID。 若要擷取此值，請登入 Azure 入口網站，並瀏覽至 Azure Active Directory > 選取屬性 > 複製目錄識別碼。  |
| Azure AD App 識別碼               | Azure AD 應用程式的 GUID  |
| Azure AD App 金鑰              | Azure AD 應用程式的祕密，例如：`IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=` |
| Azure AD 租用戶名稱          | 您 Dynamics 365 執行個體的 Azure 租用戶名稱。 請使用 <tenantname.>onmicrosoft.com 的格式，例如：`testdrive.onmicrosoft.com`  |
| 執行個體 Web API URL          | Dynamics 365 執行個體的 Web API URL。 您可以藉由登入 Microsoft Dynamics 365 執行個體，並瀏覽至 [設定] > [自訂] > [開發人員資源] > [執行個體 Web API (複製此 URL)] 來擷取此值。 值範例： `https://testdrive.crm.dynamics.com/api/data/v9.0`  |
| 角色名稱                     | 您為「試用產品」建立的自訂 Dynamics 365 安全性角色的名稱，在使用者加以執行時將會指派給使用者，例如 `testdriveuser`。 |
|  |  |

在提供所有必要的資訊後，請選取 [儲存]。


## <a name="next-steps"></a>後續步驟

接下來，您將在 [店面詳細資料](./cpp-storefront-details-tab.md) 索引標籤中提供行銷和銷售資訊。

