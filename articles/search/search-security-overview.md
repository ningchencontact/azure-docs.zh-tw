---
title: "在 Azure 搜尋服務中保護資料和作業 | Microsoft Docs"
description: "「Azure 搜尋服務」安全性所根據的是 SOC 2 合規性、加密、驗證，以及透過「Azure 搜尋服務」篩選條件中的使用者和群組安全性識別碼來進行的身分識別存取。"
services: search
documentationcenter: 
author: HeidiSteen
manager: cgronlun
editor: 
ms.assetid: 
ms.service: search
ms.devlang: 
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 01/19/2018
ms.author: heidist
ms.openlocfilehash: c3aa4883e33b1f3494f8502fe7f8b12f7d64a72f
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2018
---
# <a name="security-and-controlled-access-in-azure-search"></a>Azure 搜尋服務中的安全性和受控存取

「Azure 搜尋服務」[符合 SOC 2 規範](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide?command=Download&downloadType=Document&downloadId=93292f19-f43e-4c4e-8615-c38ab953cf95&docTab=4ce99610-c9c0-11e7-8c2c-f908a777fa4d_SOC%20%2F%20SSAE%2016%20Reports)，具備跨實體安全性、加密傳輸、加密儲存體及全平台軟體防護措施的完整安全性架構。 在操作方面，「Azure 搜尋服務」只接受經過驗證的要求。 您可以視需要在內容上新增個別使用者存取控制。 本文涵蓋每一層的安全性，但主要著重於資料和作業如何在「Azure 搜尋服務」中受到保護。

![安全性階層的區塊圖](media/search-security-overview/azsearch-security-diagram.png)

## <a name="physical-security"></a>實體安全性

Microsoft 資料中心提供業界頂尖的實體安全性，符合一組廣泛的標準與法規規範。 若要深入了解，請前往[全球資料中心](https://www.microsoft.com/cloud-platform/global-datacenters)頁面，或觀看有關資料中心安全性的短片。

> [!VIDEO https://www.youtube.com/embed/r1cyTL8JqRg]

## <a name="encrypted-transmission-and-storage"></a>加密的傳輸和儲存體

加密會延伸至整個索引編製管線：從連線到傳輸，再到儲存在「Azure 搜尋服務」中已編製索引的資料。

| 安全性階層 | 說明 |
|----------------|-------------|
| 傳輸中加密 | 「Azure 搜尋服務」會在 HTTPS 連接埠 443 上進行接聽。 在整個平台上，連至 Azure 服務的連線都會受到加密。 |
| 待用加密 | 加密會完全在索引編製程序內進行，對索引編製完成時間或索引大小沒有任何重大的影響。 它會自動針對所有索引編製程序進行，包括針對未完全加密 (建立時間在 2018 年 1 月以前) 之索引的增量更新進行。<br><br>就內部而言，加密會根據 [Azure 儲存體服務加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) \(機器翻譯\)，使用的是 256 位元的 [AES 加密](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) \(英文\)。|
| [SOC 2 合規性](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/aicpasoc2report.html) \(英文\) | 在所有提供「Azure 搜尋服務」的資料中心內，所有搜尋服務都完全符合 AICPA SOC 2 規範。 若要檢視完整的報告，請移至 [Azure - 和 Azure Government SOC 2 II 型報告](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide?command=Download&downloadType=Document&downloadId=93292f19-f43e-4c4e-8615-c38ab953cf95&docTab=4ce99610-c9c0-11e7-8c2c-f908a777fa4d_SOC%20%2F%20SSAE%2016%20Reports) \(英文\)。 |

加密是在「Azure 搜尋服務」內部進行的，由 Microsoft 在內部管理憑證和加密金鑰，並且全面套用。 您無法在入口網站中或透過程式設計方式，開啟或關閉加密、管理或替代自己的金鑰，或是檢視加密設定。 

在 2018 年 1 月 24 日已宣布推出待用加密功能，並且適用於所有區域中的所有服務層，包括共用 (免費) 服務。 若要達到完整加密的目的，針對在該日期之前建立的索引，您必須先捨棄再重新建置，才能進行加密。 否則，系統只會加密 1 月 24 日之後新增的新資料。

## <a name="azure-wide-logical-security"></a>整個 Azure 的邏輯安全性

整個 Azure Stack 有數個可用的安全性機制，因此會自動提供給您建立的「Azure 搜尋服務」資源使用。

+ [可防止刪除的訂用帳戶或資源層級鎖定](../azure-resource-manager/resource-group-lock-resources.md)
+ [可控制對資訊和系統管理作業之存取的角色型存取控制 (RBAC)](../active-directory/role-based-access-control-what-is.md)

所有 Azure 服務都支援角色型存取控制 (RBAC)，可在所有服務以一致的方式設定存取層級。 例如，限制只有「擁有者」和「參與者」角色才能檢視敏感性資料 (例如管理金鑰)，而讓所有角色的成員都可以檢視服務狀態。 RBAC 提供的角色包括「擁有者」、「參與者」及「讀者」。 根據預設，所有服務管理員都是擁有者角色的成員。

## <a name="service-access-and-authentication"></a>服務存取與驗證

雖然「Azure 搜尋服務」繼承 Azure 平台的安全性防護措施，但它也提供自己的金鑰型驗證。 金鑰的類型 (管理或查詢) 會決定存取層級。 提交有效的金鑰可證明要求源自受信任的實體。 

針對每個要求都必須進行驗證，其中每個要求會由強制性金鑰、作業及物件所組成。 當兩個權限層級 (完整和唯讀) 加上內容鏈結在一起時，即足以在服務作業上提供全面的安全性。 

|Key|說明|限制|  
|---------|-----------------|------------|  
|Admin|授與所有作業的完整權限，包括能夠管理服務、建立和刪除索引、索引子及資料來源。<br /><br /> 當服務建立時，在入口網站中會產生兩個管理 **API 金鑰** (稱為「主要」和「次要」金鑰)，而且您可以視需要個別重新產生這些金鑰。 擁有兩個金鑰可讓您在變換一個金鑰時，使用第二個金鑰來繼續存取服務。<br /><br /> 指定管理金鑰時，只能在 HTTP 要求標頭中指定。 您無法將管理 API 金鑰放在 URL 中。|每個服務的上限為 2 個|  
|查詢|授與索引和文件的唯讀存取權，且通常會分派給發出搜尋要求的用戶端應用程式。<br /><br /> 查詢金鑰是視需要建立的。 您可以在入口網站中手動建立這些金鑰，或是透過[管理 REST API](https://docs.microsoft.com/rest/api/searchmanagement/) \(英文\) 以程式設計方式建立這些金鑰。<br /><br /> 您可以在 HTTP 要求標頭中指定查詢金鑰，以進行查詢、建議或查閱作業。 或者，您也可以在 URL 上將查詢金鑰當作參數來傳遞。 視您用戶端應用程式制定要求的方式而定，將金鑰當作查詢參數來傳遞可能會較為簡單：<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2016-09-01&api-key=A8DA81E03F809FE166ADDB183E9ED84D`|每個服務 50 個|  

 管理金鑰或查詢金鑰在外觀上並無差別。 兩種金鑰都是由 32 個隨機產生的英數字元所組成。 如果您忘記在應用程式中指定的是哪種類型的金鑰，您可以[在入口網站中查看金鑰值](https://portal.azure.com)，或使用 [REST API](https://docs.microsoft.com/rest/api/searchmanagement/) 來傳回值和金鑰類型。  

> [!NOTE]  
>  在要求 URI 中傳遞敏感性資料 (例如 `api-key`) 被視為不佳的安全性做法。 基於這個理由，「Azure 搜尋服務」在查詢字串中只接受以查詢金鑰作為 `api-key` ，而且除非您應該公開提供索引內容，否則應避免採用此做法。 一般規則是建議將 `api-key` 當作要求標頭來傳遞。  

### <a name="how-to-find-the-access-keys-for-your-service"></a>如何找出服務的存取金鑰

您可以在入口網站中或透過[管理 REST API](https://docs.microsoft.com/rest/api/searchmanagement/) \(英文\) 取得存取金鑰。 如需詳細資訊，請參閱[管理金鑰](search-manage.md#manage-api-keys)。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 列出您訂用帳戶的[搜尋服務](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。
3. 選取服務，然後在服務頁面上尋找 [設定] >[金鑰]，以檢視管理和查詢金鑰。

![入口網站頁面 > [設定] > [金鑰] 區段](media/search-security-overview/settings-keys.png)

## <a name="index-access"></a>索引存取權

在「Azure 搜尋服務」中，個別索引是無法保護的物件。 取而代之的是，會在服務層搭配作業的內容來決定對索引的存取權 (讀取或寫入存取權)。

就使用者存取來說，您可以將應用程式中的查詢要求建構成使用查詢金鑰來進行連線 (這可讓所有查詢都變成唯讀)，並且包含應用程式所使用的特定索引。 在查詢要求中，並沒有聯結索引或同時存取多個索引的概念，所以所有要求都會依據定義以單一索引為目標。 因此，查詢要求本身的結構 (一個金鑰加上單一目標索引) 即可定義安全性界限。

系統管理員和開發人員對索引的存取權並無差別：兩者都需要有寫入存取權，才能建立、刪除及更新服務所管理的物件。 任何人只要擁有您服務的管理金鑰，便可讀取、修改或刪除該相同服務中的任何索引。 在防止發生意外或惡意刪除索引的情況方面，您內部對程式碼資產實施的來源控制，將會是反轉不需要之索引刪除或修改的補救措施。 「Azure 搜尋服務」在叢集內具有容錯移轉功能來確保可用性，但它不會儲存或執行用來建立或載入索引的專屬程式碼。

以需要索引層級之安全性界限的多組織用戶管理解決方案來說，這類解決方案通常會包含可供客戶用來處理索引隔離的中間層。 如需有關多租用戶使用案例的詳細資訊，請參閱[多租用戶 SaaS 應用程式與 Azure 搜尋服務的設計模式](search-modeling-multitenant-saas-applications.md)。

## <a name="admin-access-from-client-apps"></a>來自用戶端應用程式的管理存取

Azure 搜尋服務管理 REST API 為 Azure 資源管理員的擴充功能並且共享相依性。 因此，Active Directory 是 Azure 搜尋服務管理的先決條件。 所有來自用戶端程式碼的系統管理要求都必須先經過 Azure Active Directory 驗證，然後才會傳送給 Resource Manager。

對「Azure 搜尋服務」端點發出的資料要求 (例如建立索引 (Azure 搜尋服務 REST API) 或搜尋文件 (Azure 搜尋服務 REST API)) 會在要求標頭中使用 API 金鑰。

如果您的應用程式程式碼除了處理搜尋索引上的資料作業之外，也會處理服務系統管理作業，請在您的程式碼中實作兩個驗證方法：「Azure 搜尋服務」原生的存取金鑰，以及 Resource Manager 所需的 Active Directory 驗證方法。 

如需有關在「Azure 搜尋服務」中建構要求的資訊，請參閱 [Azure 搜尋服務 REST API](https://docs.microsoft.com/rest/api/searchservice/) \(英文\)。 如需有關 Resource Manager 之驗證需求的詳細資訊，請參閱[使用 Resource Manager 驗證 API 來存取訂用帳戶](../azure-resource-manager/resource-manager-api-authentication.md)。

## <a name="user-access-to-index-content"></a>使用者對索引內容的存取權

實作個別使用者對索引內容的存取權時，會透過您查詢上的安全性篩選來實作，這會傳回與指定之安全性身分識別關聯的文件。 身分識別型存取控制並不使用預先定義的角色和角色指派，而是以篩選的形式來實作，可根據身分識別縮減文件和內容的搜尋結果。 下表說明兩個針對未經授權的內容縮減搜尋結果的方法。

| 方法 | 說明 |
|----------|-------------|
|[根據身分識別篩選進行安全性範圍縮減](search-security-trimming-for-azure-search.md)  | 記載實作使用者身分識別存取控制的基本工作流程。 其中涵蓋在索引中新增安全性識別碼，然後說明如何針對該欄位進行篩選來縮減所禁止內容的結果。 |
|[根據 Azure Active Directory 身分識別進行安全性範圍縮減](search-security-trimming-for-azure-search-with-aad.md)  | 本文是上一篇文章的延伸，提供從 Azure Active Directory (AAD) (Azure 雲端平台上的其中一項[免費服務](https://azure.microsoft.com/free/)) 擷取身分識別的步驟。 |

## <a name="table-permissioned-operations"></a>表格：許可的作業

下表摘要說明「Azure 搜尋服務」中允許的作業，以及哪個金鑰可開啟對特定作業的存取權。

| 作業 | 權限 |
|-----------|-------------------------|
| 建立服務 | Azure 訂用帳戶持有者|
| 調整服務規模 | 管理金鑰、資源的 RBAC 擁有者或參與者  |
| 刪除服務 | 管理金鑰、資源的 RBAC 擁有者或參與者 |
| 在服務上建立、修改、刪除物件： <br>索引和元件組件 (包括分析器定義、評分設定檔、CORS 選項)、索引子、資料來源、同義字、建議工具。 | 管理金鑰、資源的 RBAC 擁有者或參與者  |
| 查詢索引 | 管理或查詢金鑰 (RBAC 不適用) |
| 查詢系統資訊，例如傳回統計資料、計數及物件清單。 | 管理金鑰、資源的 RBAC (擁有者、參與者、讀者) |
| 管理管理金鑰 | 管理金鑰、資源的 RBAC 擁有者或參與者。 |
| 管理查詢金鑰 |  管理金鑰、資源的 RBAC 擁有者或參與者。 RBAC 讀者可以檢視查詢金鑰。 |


## <a name="see-also"></a>另請參閱

+ [開始使用 .NET (使用管理金鑰來建立索引以進行示範)](search-create-index-dotnet.md)
+ [開始使用 REST (使用管理金鑰來建立索引以進行示範)](search-create-index-rest-api.md)
+ [使用 Azure 搜尋服務篩選來進行身分識別型存取控制](search-security-trimming-for-azure-search.md)
+ [使用 Azure 搜尋服務篩選來進行 Active Directory 身分識別型存取控制](search-security-trimming-for-azure-search-with-aad.md)
+ [Azure 搜尋服務中的篩選條件](search-filters.md)