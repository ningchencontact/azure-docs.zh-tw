---
title: "保護資料和 Azure 搜尋中的作業 |Microsoft 文件"
description: "Azure 搜尋的安全性根據 SOC 2 相容性、 加密、 驗證和身分識別存取透過使用者和群組安全性識別碼，在 Azure 搜尋的篩選條件。"
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
ms.assetid: 
ms.service: search
ms.devlang: 
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 12/14/2017
ms.author: heidist
ms.openlocfilehash: 23616c70a5fd336b743f5acfad2601a6c3e23fc4
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2017
---
# <a name="data-security-and-controlled-access-to-azure-search-operations"></a>資料安全性和控制的存取 Azure 搜尋作業

Azure Search 是[SOC 2 相容](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide?command=Download&downloadType=Document&downloadId=93292f19-f43e-4c4e-8615-c38ab953cf95&docTab=4ce99610-c9c0-11e7-8c2c-f908a777fa4d_SOC%20%2F%20SSAE%2016%20Reports)、 完整的安全性架構跨距實體安全性、 加密的傳輸、 加密存放裝置，以及整個平台的軟體保護措施。 無法看到 Azure 搜尋只接受已驗證的要求。 （選擇性） 您可以新增每個使用者存取內容上的控制項。 這篇文章的安全性，每個圖層，會牽涉到，但主要著重於如何保護 Azure 搜尋中的資料和作業。

![安全性層級的區塊圖](media/search-security-overview/azsearch-security-diagram.png)

雖然 Azure 搜尋會繼承的保護和 Azure 平台的保護措施，服務本身所使用的主要機制是索引鍵為基礎的驗證，其中索引鍵的類型會決定存取層級。 管理金鑰或查詢索引鍵的唯讀存取金鑰。

存取您的服務為基礎的索引鍵 （完整或唯讀），再加上定義的作業範圍的內容所傳達的權限的交叉區段。 每個要求是由必要索引鍵、 作業和物件所組成。 當鏈結在一起，兩個權限等級，再加上內容已足夠來提供服務作業的完整範圍的安全性。 

## <a name="physical-security"></a>實體安全性

Microsoft 資料中心內提供領先業界的實體安全性，並與標準出口法規之限制的廣泛 portfolio 相容。 若要進一步了解，請前往[全球資料中心](https://www.microsoft.com/cloud-platform/global-datacenters)頁面上，或觀賞短片，資料中心的安全性。

> [!VIDEO https://www.youtube.com/embed/r1cyTL8JqRg]

## <a name="encrypted-transmission-and-storage"></a>加密的傳輸和儲存體

Azure 搜尋會接聽 HTTPS 連接埠 443。 跨平台，Azure 服務的連線都會經過加密。 

用於索引和其他建構後端存放區的情況下，Azure 搜尋會利用這些平台的加密功能。 完整[AICPA SOC 2 相容性](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/aicpasoc2report.html)可供所有搜尋服務 （新的和現有），提供 Azure 搜尋的所有資料中心內。 若要檢視完整的報告，請移至[Azure-和 Azure 政府 SOC 2 類型 II 報告](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide?command=Download&downloadType=Document&downloadId=93292f19-f43e-4c4e-8615-c38ab953cf95&docTab=4ce99610-c9c0-11e7-8c2c-f908a777fa4d_SOC%20%2F%20SSAE%2016%20Reports)。

加密是透明的請使用加密金鑰就內部而言，管理並全域套用。 您無法針對特定的搜尋服務或索引，將它關閉，也不直接管理金鑰或提供您自己。 

## <a name="azure-wide-logical-security"></a>Azure 全邏輯安全性

數種安全性機制，而提供跨 Azure 堆疊，因此會自動提供給您所建立的 Azure 搜尋資源。

+ [訂用帳戶或將防止刪除的資源層級的鎖定](../azure-resource-manager/resource-group-lock-resources.md)
+ [角色型存取控制 (RBAC) 來控制存取資訊和管理作業](../active-directory/role-based-access-control-what-is.md)

所有 Azure 服務支援以角色為基礎的存取控制 (RBAC) 一致地設定所有服務的存取層級。 例如，檢視機密資料，例如管理金鑰，僅限於 「 擁有者及參與者角色，而檢視服務狀態是提供給任何角色的成員。 RBAC 提供擁有者、 參與者和讀取器角色。 根據預設，所有服務管理員都是擁有者角色的成員。

## <a name="service-authentication"></a>服務驗證

Azure 搜尋會提供自己的驗證方法。 驗證會在每個要求，而為基礎的存取金鑰會決定作業的範圍。 有效的存取金鑰會被視為證明要求是來自受信任的實體。 

每個服務驗證有兩個層級： 完整權限，僅查詢。 索引鍵的類型判斷存取的層級為作用中。

|Key|說明|限制|  
|---------|-----------------|------------|  
|Admin|授與所有作業，包括管理服務功能的完整權限建立和刪除**索引**，**索引子**，和**資料來源**。<br /><br /> 兩個系統管理員**api 金鑰**，稱為*主要*和*次要*服務建立的而且可以個別隨需重新產生時，會產生在入口網站的金鑰. 必須要有兩個金鑰，可讓您變換時使用一直持續不斷地存取服務的第二個索引鍵的一個索引鍵。<br /><br /> HTTP 要求標頭中只指定系統管理金鑰。 您不能放置管理員**api 金鑰**在 URL 中。|最大值 2，每個服務|  
|查詢|授與給索引和文件的唯讀存取，而且通常會發出搜尋要求的用戶端應用程式散發。<br /><br /> 依需求建立查詢索引鍵。 您可以手動建立關聯的入口網站中，或以程式設計方式透過[管理 REST API](https://docs.microsoft.com/rest/api/searchmanagement/)。<br /><br /> 查詢索引鍵可以指定搜尋、 建議或查閱作業的 HTTP 要求標頭中。 或者，您可以在 URL 上做為參數傳遞查詢索引鍵。 根據如何用戶端應用程式會構成要求，可能更輕鬆地傳遞做為查詢參數的索引鍵：<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2016-09-01&api-key=A8DA81E03F809FE166ADDB183E9ED84D`|每個服務 50|  

 以視覺化的方式，是將管理金鑰或查詢索引鍵之間沒有差別。 這兩個索引鍵是 32 的隨機組成字串產生英數字元。 如果您遺失您的應用程式中指定的索引鍵類型的追蹤，您可以[檢查入口網站中的金鑰值](https://portal.azure.com)或使用[REST API](https://docs.microsoft.com/rest/api/searchmanagement/)来傳回的值和索引鍵的類型。  

> [!NOTE]  
>  它會被視為不佳的安全性作法傳遞的機密資料，例如`api-key`要求 URI 中。 基於這個理由，Azure 搜尋只接受查詢索引鍵為`api-key`在查詢字串，而且您應該避免這樣做，除非您的索引內容是應該公開提供。 一般而言，我們建議傳遞您`api-key`做為要求標頭。  

### <a name="how-to-find-the-access-keys-for-your-service"></a>如何尋找您的服務的存取金鑰

您可以取得便捷鍵在入口網站或透過[管理 REST API](https://docs.microsoft.com/rest/api/searchmanagement/)。 如需詳細資訊，請參閱[管理金鑰](search-manage.md#manage-api-keys)。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 清單[搜尋服務](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)訂用帳戶。
3. 選取的服務，並在 [服務] 頁面上尋找**設定** >**金鑰**若要檢視系統管理和查詢索引鍵。

![入口網站頁面上，設定金鑰 > 一節](media/search-security-overview/settings-keys.png)

## <a name="index-access"></a>索引的存取

在 Azure 搜尋中，個別的索引不是安全性實體物件。 相反地，索引的存取是由決定在服務層 （讀取或寫入權限），以及作業的內容。

當使用者存取時，您也可以使用查詢的索引鍵，這會使任何要求變成唯讀的而且包含應用程式所使用的特定索引進行連接的應用程式中建構查詢要求。 在查詢要求中，沒有聯結索引，或同時存取多個索引，因此所有要求以定義為都目標的單一索引的概念。 因此，查詢要求本身 （索引鍵加上單一目標索引） 的結構會定義安全性界限。

系統管理員和開發人員的存取權索引是無： 兩者都需要寫入權限建立、 刪除及更新此服務所管理的物件。 服務管理金鑰的任何人都可以讀取、 修改或刪除相同的服務中的任何索引。 防止意外或惡意刪除的索引，內部的原始檔控制的程式碼資產是不必要的索引刪除或修改反轉的補救措施。 Azure 搜尋具有容錯移轉叢集中，以確保可用性，但不儲存或執行您專屬的程式碼，用來建立或載入索引。

如需多解決方案需要在索引層級的安全性界限，這類方案通常包含中介層，來處理索引隔離使用哪些客戶。 如需多租用戶的使用案例的詳細資訊，請參閱[設計模式為多租用戶的 SaaS 應用程式與 Azure 搜尋](search-modeling-multitenant-saas-applications.md)。

## <a name="admin-access-from-client-apps"></a>從用戶端應用程式的系統管理員存取權

Azure 搜尋服務管理 REST API 為 Azure 資源管理員的擴充功能並且共享相依性。 因此，Active Directory 是 Azure 搜尋服務管理的先決條件。 用戶端程式碼的所有系統管理要求必須要求抵達資源管理員之前，請使用 Azure Active Directory 進行驗證。

資料要求，對 Azure 搜尋服務的端點，例如 Create Index (Azure 搜尋服務 REST API) 或搜尋文件 (Azure 搜尋服務 REST API)，使用要求標頭中的 api 金鑰。

如果應用程式程式碼會處理服務管理作業，以及搜尋索引或文件上的資料作業，在您的程式碼中實作兩種驗證方法： 原生 Azure 搜尋中，與 Active Directory 驗證的存取金鑰由資源管理員所需的方法。 

如需結構化 Azure 搜尋中的要求資訊，請參閱[Azure 搜尋服務 REST](https://docs.microsoft.com/rest/api/searchservice/)。 如需有關驗證需求的資源管理員的詳細資訊，請參閱[使用資源管理員 驗證應用程式開發介面來存取訂閱](../azure-resource-manager/resource-manager-api-authentication.md)。

## <a name="user-access-to-index-content"></a>使用者存取索引內容

每位使用者存取索引的內容是透過您的查詢，傳回與指定的安全性身分識別相關聯的文件上的安全性篩選器實作。 而不是預先定義的角色和角色指派，身分識別為基礎的存取控制會實作為篩選修剪搜尋文件和身分識別為基礎的內容的結果。 下表描述兩種方法的未經授權的內容調整搜尋結果。

| 方法 | 說明 |
|----------|-------------|
|[識別篩選器為基礎的安全性調整](search-security-trimming-for-azure-search.md)  | 文件的基本工作流程實作使用者身分識別存取控制。 它涵蓋新增的安全性識別元到索引，並再說明 篩選針對該欄位来修剪禁止之內容的結果。 |
|[Azure Active Directory 身分識別為基礎的安全性調整](search-security-trimming-for-azure-search-with-aad.md)  | 本文將前一個發行項，來擷取識別身分從 Azure Active Directory (AAD)，其中提供步驟[釋放服務](https://azure.microsoft.com/free/)在 Azure 雲端平台。 |

## <a name="table-permissioned-operations"></a>Table： 授與權限的作業

下表摘要說明在 Azure 搜尋中允許的作業，以及哪個金鑰解除鎖定特定作業的存取。

| 作業 | 權限 |
|-----------|-------------------------|
| 建立服務 | Azure 訂用帳戶持有者|
| 調整服務的規模 | 管理金鑰，RBAC 擁有者或參與者的資源  |
| 刪除服務 | 管理金鑰，RBAC 擁有者或參與者的資源 |
| 建立、 修改、 刪除服務上的物件： <br>索引和組件的元件 （包括分析師定義、 計分設定檔，CORS 選項）、 索引子、 資料來源、 同義字、 建議工具。 | 管理金鑰，RBAC 擁有者或參與者的資源  |
| 查詢索引 | 系統管理員或查詢索引鍵 (RBAC 不適用) |
| 查詢系統資訊，例如傳回統計資料、 計數和相關的物件清單。 | 管理金鑰，RBAC 資源 （擁有者、 參與者，讀取器） |
| 管理系統管理金鑰 | 管理金鑰，RBAC 擁有者或參與者在資源上。 |
| 管理查詢索引鍵 |  管理金鑰，RBAC 擁有者或參與者在資源上。 RBAC 讀取器可以檢視查詢索引鍵。 |


## <a name="see-also"></a>請參閱

+ [取得已啟動 （將示範如何使用管理金鑰建立的索引） 的.NET](search-create-index-dotnet.md)
+ [取得已啟動 （將示範如何使用管理金鑰建立的索引） 的其餘部分](search-create-index-rest-api.md)
+ [使用 Azure 搜尋篩選器的身分識別為基礎的存取控制](search-security-trimming-for-azure-search.md)
+ [使用 Azure 搜尋篩選器的 active Directory 身分識別為基礎的存取控制](search-security-trimming-for-azure-search-with-aad.md)
+ [Azure 搜尋服務中的篩選條件](search-filters.md)