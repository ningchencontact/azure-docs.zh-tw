---
title: Azure Data Lake Storage Gen2 儲存體的安全性指南 | Microsoft Docs
description: 詳述許多保護 Azure 儲存體的方法，包括 (但不限於) RBAC 和儲存體服務加密
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 12/04/2018
ms.author: rogarana
ms.component: common
ms.openlocfilehash: d2182942b8d1ce78fd4a72ff387c7a6a1cfead5a
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/06/2018
ms.locfileid: "52976221"
---
# <a name="azure-data-lake-storage-gen2-security-guide"></a>Azure Data Lake Storage Gen2 安全性指南

Azure Data Lake Storage Gen2 預覽版是一組建置在 Azure 儲存體帳戶上的功能。 因此，本文中的任何參考都會用於已啟用階層命名空間 (Data Lake Storage Gen2 功能) 的 Azure 儲存體帳戶。

- 所有寫入至「Azure 儲存體」的資料都會使用[儲存體服務加密 (SSE)](storage-service-encryption.md)來自動加密。 如需詳細資訊，請參閱[宣佈 Azure Blob、檔案、資料表及佇列儲存體的預設加密](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/) \(英文\)。
- Azure Active Directory (Azure AD) 和角色型存取控制 (RBAC) 可支援 Azure 儲存體，以便進行資源管理作業和資料作業，如下所示：
    - 您可以將儲存體帳戶範圍內的 RBAC 角色指派給安全性主體，以及使用 Azure AD 來授權資源管理作業，例如金鑰管理。
    - Azure 儲存體的資料作業預覽版支援 Azure AD 整合。 您可以將訂用帳戶、資源群組、儲存體帳戶或個別檔案系統範圍內的 RBAC 角色，指派給安全性主體或 Azure 資源的受控識別。 如需詳細資訊，請參閱[使用 Azure Active Directory 來驗證 Azure 儲存體的存取權 (預覽)](storage-auth-aad.md)。
- Azure 儲存體中資料物件的委派存取權可以使用 [共用存取簽章](../storage-dotnet-shared-access-signature-part-1.md)來授與。

本文簡要說明這些安全性功能當中，可與「Azure 儲存體」搭配使用的每個安全性功能。 所提供的文章連結將提供每個功能的詳細資料，讓您能夠輕鬆地進一步調查每個主題。

以下是本文所涵蓋的主題：

* [管理平面安全性](#management-plane-security) – 保護儲存體帳戶

  管理平面包含用來管理儲存體帳戶的資源。 本節涵蓋 Azure Resource Manager 部署模型，以及如何使用「角色型存取控制」(RBAC) 來控制對儲存體帳戶的存取。 此外，也說明如何管理儲存體帳戶金鑰，以及重新產生這些金鑰。
* [資料平面安全性](#data-plane-security) – 保護資料的存取

  本節將探討如何在儲存體帳戶 (亦即檔案或目錄) 中，允許使用共用存取簽章和預存存取原則來存取實際的資料物件。 我們將涵蓋服務層級的 SAS 和帳戶層級的 SAS。 我們也會看到如何限制存取特定的 IP 位址 (或 IP 位址範圍)、如何限制用於 HTTPS 的通訊協定，以及如何撤銷共用存取簽章，而不需等到它過期。
* [傳輸中加密](#encryption-in-transit)

本節討論如何在將資料傳輸至啟用 Data Lake Storage Gen2 的儲存體帳戶或從中傳出時提供保護。 我們將討論 HTTPS 的建議用法。

## <a name="management-plane-security"></a>管理平面安全性

管理平面包含會影響儲存體帳戶本身的作業。 例如，您可以建立或刪除儲存體帳戶、取得訂用帳戶中的儲存體帳戶清單、擷取儲存體帳戶金鑰，或重新產生儲存體帳戶金鑰。

本指南著重於 Resource Manager 模型的部署，也就是建立儲存體帳戶與 Data Lake Storage Gen2 功能的方法。 使用 Resource Manager 儲存體帳戶，而不是提供整個訂用帳戶的存取權，您可以使用角色型存取控制 (RBAC)，來控制更限定層級上對管理平面的存取。

### <a name="how-to-secure-your-storage-account-with-role-based-access-control-rbac"></a>如何使用角色型存取控制 (RBAC) 保護儲存體帳戶

讓我們討論何謂 RBAC 及使用方式。 每一個 Azure 訂用帳戶都具有 Azure Active Directory。 您可以為來自該目錄的使用者、群組和應用程式授與存取權，以便在使用資源管理員部署模型的 Azure 訂用帳戶中管理資源。 這種安全性稱為「角色型存取控制」(RBAC)。 若要管理此存取權，請在 Azure 入口網站中使用**存取控制 (IAM)**。

使用資源管理員模型，您可以將儲存體帳戶放置於資源群組中，並使用 Azure Active Directory 來控制該特定儲存體帳戶之管理平面的存取。 例如，您可以為特定使用者提供存取儲存體帳戶金鑰的能力，而其他使用者可以檢視儲存體帳戶的相關資訊，但是無法存取儲存體帳戶金鑰。

#### <a name="granting-access"></a>授與存取權

在正確範圍將適當的 RBAC 角色指派給使用者、群組和應用程式，即可授與存取權。 若要授與整個訂用帳戶的存取權，請指派訂用帳戶層級的角色。 您可以將權限授與資源群組本身，藉以授與資源群組中所有資源的存取權。 您也可以將特定角色指派給特定資源，例如儲存體帳戶。

以下是您需要知道有關使用 RBAC 存取 Azure 儲存體帳戶之管理作業的重點︰

* 針對有權存取儲存體帳戶中資料物件的使用者，您可以為他們提供權限來讀取儲存體帳戶金鑰，而該使用者接著可以使用這些金鑰來存取檔案和目錄。
* 角色可以指派給特定的使用者帳戶、使用者群組或特定的應用程式。
* 每個角色都有「動作」與「沒有動作」清單。 例如，「虛擬機器參與者」角色具有 “listKeys” 的動作，可允許讀取儲存體帳戶金鑰。 「參與者」含有「沒有動作」，例如在 Active Directory 中更新使用者的存取權。
* 儲存體的角色包括 (但不限於) 下列角色：

  * 擁有者 – 他們可以管理一切，包括存取權。
  * 參與者 – 他們可以執行擁有者可執行的所有動作，但指派存取權除外。 擁有此角色的使用者可以檢視和重新產生儲存體帳戶金鑰。 他們可以使用儲存體帳戶金鑰來存取資料物件。
  * 讀者 – 他們可以檢視儲存體帳戶 (密碼除外) 的相關資訊。 例如，如果您將儲存體帳戶中具有讀者權限的角色指派給某人，他們就能檢視儲存體帳戶的屬性，但無法對屬性進行任何變更或檢視儲存體帳戶金鑰。
  * 儲存體帳戶參與者 – 他們可以管理儲存體帳戶 – 他們可以讀取訂用帳戶的資源群組和資源，以及建立和管理訂用帳戶資源群組部署。 他們也可以存取儲存體帳戶金鑰，這表示他們可以存取資料平面。
  * 使用者存取系統管理員 – 他們可以管理儲存體帳戶的使用者存取權。 例如，他們可將「讀者」權限授與特定的使用者。
  * 虛擬機器參與者 – 他們可以管理虛擬機器，但是無法管理他們連接的儲存體帳戶。 這個角色可以列出儲存體帳戶金鑰，這表示您指派此角色的使用者可以更新資料平面。

    為了讓使用者能夠建立虛擬機器，他們必須能夠在儲存體帳戶中建立對應的 VHD 檔案。 若要這麼做，他們需要能夠擷取儲存體帳戶金鑰，並將它傳遞給建立 VM 的 API。 因此，他們必須具備此權限，如此就能列出儲存體帳戶金鑰。
* 定義自訂角色的能力是一項功能，允許您從可在 Azure 資源上執行的可用動作清單中撰寫一系列動作。
* 您必須先在 Azure Active Directory 中設定使用者，才能為他們指派角色。
* 您可以建立一份報告，其中包含哪一位人員已使用 PowerShell 或 Azure CLI，在哪個範圍中，為哪些對象授與/撤銷何種類型的存取權。

#### <a name="resources"></a>資源

* [Azure Active Directory 角色型存取控制](../../role-based-access-control/role-assignments-portal.md)

  本文說明 Azure Active Directory 角色型存取控制及其運作方式。
* [RBAC：內建角色](../../role-based-access-control/built-in-roles.md)

  本文將詳細說明 RBAC 中所有可用的內建角色。
* [了解資源管理員部署和傳統部署](../../azure-resource-manager/resource-manager-deployment-model.md)

### <a name="managing-your-storage-account-keys"></a>管理儲存體帳戶金鑰

儲存體帳戶金鑰是 Azure 所建立的 512 位元字串，搭配儲存體帳戶名稱就能用來存取儲存在儲存體帳戶中的資料物件，例如 Blob、資料表內的實體、佇列訊息，以及 Azure 檔案共用上的檔案。 控制儲存體帳戶金鑰的存取，就能控制該儲存體帳戶之資料平面的存取。

每個儲存體帳戶在 [Azure 入口網站](http://portal.azure.com/) 和 PowerShell Cmdlet 中都有兩個稱為「金鑰 1」和「金鑰 2」的金鑰。 您可以使用下列其中一種方法手動重新產生它們，包括 (但不限於) 使用 [Azure 入口網站](https://portal.azure.com/)、PowerShell、Azure CLI，或以程式設計方式使用 .NET 儲存體用戶端程式庫或 Azure 儲存體服務 REST API。

有許多原因會導致要重新產生儲存體帳戶金鑰。

* 您可能會基於安全性理由定期重新產生它們。
* 如果某人設法駭入應用程式並擷取硬式編碼或儲存於組態檔案中的金鑰，來為他們提供您儲存體帳戶的完整存取權，則您必須重新產生儲存體帳戶金鑰。
* 重新產生金鑰的另一種情況是，如果您的小組使用儲存體總管應用程式，其中會保留儲存體帳戶金鑰，而其中一位小組成員離開時。 在他們離開之後，應用程式還會繼續運作，讓他們能夠存取您的儲存體帳戶。 這實際上是他們建立帳戶層級共用存取簽章的主要原因 – 您可以改用帳戶層級的 SAS，而不是將存取金鑰儲存於組態檔案中。

#### <a name="key-regeneration-plan"></a>金鑰重新產生計畫

您不想在沒有任何規劃的情況下，只是重新產生所使用的金鑰。 如果您這麼做時，可能會切斷對該儲存體帳戶的所有存取權，而這會造成嚴重中斷。 這就是為什麼要有兩個金鑰。 您一次應該重新產生一個金鑰。

重新產生金鑰之前，先確定您擁有相依於儲存體帳戶的所有應用程式清單，以及您在 Azure 中使用的所有其他服務。 例如，如果您使用相依於儲存體帳戶的「Azure 媒體服務」，就必須在重新產生金鑰之後，將存取金鑰與媒體服務重新同步。 如果您使用儲存體總管之類的任何應用程式，也必須為這些應用程式提供新的金鑰。 如果 VM 的 VHD 檔案儲存在儲存體帳戶中，它們將不會受到重新產生儲存體帳戶金鑰影響。

您可以在 Azure 入口網站中重新產生金鑰。 重新產生金鑰之後，最多可能需要 10 分鐘的時間，才能在所有「儲存體服務」完成同步處理。

當您準備好時，以下是一般程序，可詳細說明您應該如何變更金鑰。 在此案例中，假設您目前使用的是金鑰 1，而您想要變更所有項目以改用金鑰 2。

1. 重新產生金鑰 2，以確保該金鑰是安全的。 您可以在 Azure 入口網站中執行此動作。
2. 在儲存體金鑰儲存所在的所有應用程式中，變更儲存體金鑰以使用金鑰 2 的新值。 測試並發佈應用程式。
3. 在所有應用程式和服務已啟動並執行成功之後，重新產生金鑰 1。 這確保您未明確提供新金鑰的任何人都將不再擁有儲存體帳戶的存取權。

如果您目前使用金鑰 2，就可以使用相同的程序，但反轉金鑰名稱。

您可以過幾天後移轉，變更每個應用程式來使用新的金鑰並進行發佈。 全部完成之後，您接著應該返回並重新產生舊的金鑰，使其無法運作。

另一個選項是將儲存體帳戶金鑰放在 [Azure 金鑰保存庫](https://azure.microsoft.com/services/key-vault/) 中做為密碼，並讓您的應用程式可從中擷取該金鑰。 接著，當您重新產生金鑰並更新 Azure 金鑰保存庫時，將不需重新部署應用程式，因為它們會自動從 Azure 金鑰保存庫中挑選新的金鑰。 您可以讓應用程式每次在您需要金鑰時讀取它，或者，您可以將它快取於記憶體中，如果使用金鑰時失敗，就再次從 Azure Key Vault 中擷取該金鑰。

使用 Azure 金鑰保存庫，也會增加儲存體金鑰的安全性層級。 如果您使用這個方法，永遠都不需要將儲存體金鑰硬式編碼於組態檔中，這樣會移除某人不需特定權限即可存取金鑰的途徑。

使用 Azure 金鑰保存庫的另一個優點是，您也可以使用 Azure Active Directory 來控制金鑰的存取。 這表示您可以將存取權授與少數必須從 Azure 金鑰保存庫擷取金鑰的應用程式，並了解其他應用程式在未特別授與它們權限的情況下無法存取金鑰。

> [!NOTE]
> Microsoft 建議同一時間在您的所有應用程式中僅使用其中一個金鑰。 如果您在某些地方使用金鑰 1 並在其他地方使用金鑰 2，您就無法在沒有部分應用程式遺失存取的情況下輪換您的金鑰。

#### <a name="resources"></a>資源

* [管理 Azure 入口網站中的儲存體帳戶設定](storage-account-manage.md)
* [Azure 儲存體資源提供者 REST API 參考](https://msdn.microsoft.com/library/mt163683.aspx)

## <a name="data-plane-security"></a>資料平面安全性
資料平面安全性是指用來保護 Azure 儲存體所存資料物件的方法。 我們已了解在傳輸資料期間加密資料和安全性的方法，但您該從何處著手來控制對物件的存取？

有三個選項可供您授權 Azure 儲存體資料物件的存取權，包括：

- 使用 Azure AD 來授權檔案系統和佇列的存取權 (預覽)。 Azure AD 在授權方面的優勢勝過其他方法，包括不需要在程式碼中儲存祕密。 如需詳細資訊，請參閱[使用 Azure Active Directory 來驗證 Azure 儲存體的存取權 (預覽)](storage-auth-aad.md)。 
- 使用儲存體帳戶金鑰來透過共用金鑰授權存取權。 透過共用金鑰授權時，需要在應用程式中儲存儲存體帳戶金鑰，因此 Microsoft 建議盡可能改為使用 Azure AD。 對於生產應用程式，或是要授權 Azure 資料表和檔案的存取權，請在 Azure AD 整合處於預覽狀態時，繼續使用共用金鑰。
- 使用共用存取簽章，來授與一段特定時間對特定資料物件的受控權限。

除了透過授權限制存取，您也可以使用[防火牆和虛擬網路](storage-network-security.md)，根據網路規則來限制對儲存體帳戶的存取。  此方法可讓您拒絕對公用網際網路流量的存取，只授與對特定 Azure 虛擬網路或公用網際網路 IP 位址範圍的存取。

### <a name="storage-account-keys"></a>儲存體帳戶金鑰

儲存體帳戶金鑰是由 Azure 所建立的 512 位元字串，搭配儲存體帳戶名稱就能用來存取儲存於儲存體帳戶中的資料物件。

例如，您可以讀取檔案。 這其中許多動作都可透過 Azure 入口網站，或使用許多儲存體總管應用程式之一來執行。 您也可以撰寫程式碼來使用 REST API 執行這些作業。

如[管理平面安全性](#management-plane-security)中的章節所述，使用 Azure Resource Manager 模型來存取儲存體帳戶的儲存體金鑰，可以透過角色型存取控制 (RBAC) 來控制。

### <a name="how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies"></a>如何使用共用存取簽章和預存存取原則委派帳戶中物件的存取權

共用存取簽章是一個字串，包含可附加至 URI 的安全性權杖，可讓您委派儲存體物件的存取權，以及指定存取的權限和日期/時間範圍之類的限制。

您可以授與檔案或目錄的存取。

您可以為某一位客戶提供 SAS 權杖，讓他們可以用來將圖片上傳至 Blob 儲存體中的檔案系統，並為 Web 應用程式提供權限來讀取這些圖片。 在這兩種情況下，產生了關注點分離情況 – 每個應用程式只能取得執行其工作所需的存取權。 這可能是因為使用了共用存取簽章。

#### <a name="why-you-want-to-use-shared-access-signatures"></a>您想要使用共用存取簽章的原因

為什麼您想要使用 SAS，而不只是散發您的儲存體帳戶金鑰，哪一個方法更容易使用？ 散發您的儲存體帳戶金鑰，就像是在您的儲存體王國內共用金鑰。 它會授與完整存取權限。 其他人可以使用您的金鑰，並將其整個音樂媒體櫃上傳至您的儲存體帳戶。 他們可能也會使用受病毒感染的版本來取代您的檔案或竊取您的資料。 無限制散發您儲存體帳戶的存取權不應草率行事。

使用共用存取簽章，您可以只為用戶端提供有限時間內所需的權限。 例如，如果有人將檔案上傳至您的帳戶，您可以授與他們足夠時間的寫入權限來上傳檔案 (當然，這取決於檔案的大小)。 如果您改變心意，您可以撤銷該存取權。

此外，您可以指定將使用 SAS 所提出的要求限制為特定的 IP 位址或 Azure 外部的 IP 位址範圍。 您也可以要求使用特定通訊協定 (HTTPS 或 HTTP/HTTPS) 來提出要求。 這表示，如果您只想要允許 HTTPS 流量，您可以將必要的通訊協定設定為僅限 HTTPS，而且將會封鎖 HTTP 流量。

#### <a name="definition-of-a-shared-access-signature"></a>定義共用存取簽章

共用存取簽章是一組附加至指向資源之 URL 的查詢參數

其中提供所允許存取權的相關資訊，以及准許該存取權的時間長度。 以下提供一個範例：這個 URI 會提供五分鐘對 Blob 的讀取權限。 SAS 查詢參數必須以 URL 編碼，例如 %3A 用於冒號 (:) 或 %20 用於空格。

```
http://mystorage.dfs.core.windows.net/myfilesystem/myfile.txt (URL to the file)
?sv=2015-04-05 (storage service version)
&st=2015-12-10T22%3A18%3A26Z (start time, in UTC time and URL encoded)
&se=2015-12-10T22%3A23%3A26Z (end time, in UTC time and URL encoded)
&sr=b (resource is a blob)
&sp=r (read access)
&sip=168.1.5.60-168.1.5.70 (requests can only come from this range of IP addresses)
&spr=https (only allow HTTPS requests)
&sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D (signature used for the authentication of the SAS)
```

#### <a name="how-the-shared-access-signature-is-authorized-by-the-azure-storage-service"></a>Azure 儲存體服務授權共用存取簽章的方式

當儲存體服務收到要求時，它會取得輸入查詢參數，並使用與呼叫程式相同的方法來建立簽章。 然後比較這兩個簽章。 如果它們同意，儲存體服務接著可以檢查儲存體服務版本，以確定它是有效的、檢查目前的日期和時間是在指定時段內、確定所要求的存取權會對應至所提出的要求，依此類推。

例如，利用上述的 URL，如果 URL 指向檔案而不是 Blob，此要求便會失敗，因為它指定共用存取簽章適用於 Blob。 如果呼叫的 REST 命令會更新 Blob，則該命令會失敗，因為共用存取簽章指定只准許讀取權限。

#### <a name="types-of-shared-access-signatures"></a>共用存取簽章的類型

* 服務層級的 SAS 可用來存取儲存體帳戶中的特定資源。 這其中的一些範例會擷取檔案系統中的檔案清單或下載檔案。
* 帳戶層級的 SAS 可用來存取可使用服務層級 SAS 的任何項目。 此外，它可以為服務層級 SAS 不准許的資源提供選項，例如，能夠建立檔案系統。

#### <a name="creating-a-sas-uri"></a>建立 SAS URI

1. 您可以視需要建立 URI，每次都定義所有查詢參數。

   此方法相當有彈性，但如果您每次都有一組類似的邏輯參數，則使用「預存存取原則」會是個更好的做法。
2. 您可以針對整個檔案系統、檔案共用、表格或佇列建立預存存取原則。 然後使用這個原則做為您所建立的 SAS URI 基礎。 您可以輕鬆地撤銷以預存存取原則為基礎的權限。 您可以在每個檔案系統、佇列、資料表或檔案共用上最多定義 5 個原則。

   例如，如果您要讓許多人讀取特定檔案系統中的 Blob，您可以建立預存存取原則，表示會「提供讀取權」以及每次都一樣的任何其他設定。 接著您可以使用預存存取原則的設定，並指定到期日期/時間，來建立 SAS URI。 這樣做的優點是您不需每次指定所有查詢參數。

#### <a name="revocation"></a>撤銷

假設您的 SAS 已遭洩露，或您想要基於公司安全性或法規遵循需求加以變更。 您如何使用該 SAS 撤銷資源的存取權？ 這取決於您建立 SAS URI 的方式。

如果您使用臨機操作的 URI，您就會有三個選項。 您可以發出具有短期到期原則的 SAS 權杖，然後等待 SAS 到期。 您可以重新命名或刪除資源 (假設權杖範圍只限於單一物件)。 您可以變更儲存體帳戶金鑰。 根據使用該儲存體帳戶的服務數目而定，這最後一個選項可能會產生很大的影響，而且可能不是您在沒有任何規劃的情況下會想要執行的動作。

如果您使用衍生自預存存取原則的 SAS，就可以藉由撤銷預存存取原則來移除存取權 – 您只能在它已經完全過期時加以變更，或完全移除它。 這會立即生效，並使每個使用該預存存取原則建立的 SAS 失效。 更新或移除預存存取原則可能會影響透過 SAS 存取該特定檔案系統、檔案共用、表格或佇列的使用者，但如果會寫入用戶端，使得他們可在舊的 SAS 變成無效時要求一個新的 SAS，則這將可正常運作。

因為使用衍生自預存存取原則的 SAS 讓您能夠立即撤銷該 SAS，所以建議的最佳做法是一律儲存預存存取原則 (如果可能)。

#### <a name="resources"></a>資源

如需有關使用「共用存取簽章」和「預存存取原則」的更多詳細資訊，請完成範例並參閱下列文章︰

* 以下是參考文章。

  * [服務 SAS](https://msdn.microsoft.com/library/dn140256.aspx)

    本文提供使用服務層級 SAS 搭配 Blob、佇列、表格範圍及檔案的範例。
  * [建構服務 SAS](https://msdn.microsoft.com/library/dn140255.aspx)
  * [建構帳戶 SAS](https://msdn.microsoft.com/library/mt584140.aspx)
* 這些是使用 .NET 用戶端程式庫，來建立共用存取簽章和預存存取原則的教學課程。

  * [使用共用存取簽章 (SAS)](../storage-dotnet-shared-access-signature-part-1.md)
  * [共用存取簽章，第 2 部分：透過 Blob 服務來建立與使用 SAS](../blobs/storage-dotnet-shared-access-signature-part-2.md)

    本文包含 SAS 模型的說明、共用存取簽章的範例，以及使用 SAS 最佳做法的建議。 同時也會討論撤銷授與的權限。

* 驗證

  * [Azure 儲存體服務的驗證](https://msdn.microsoft.com/library/azure/dd179428.aspx)
* 共用存取簽章入門教學課程

  * [SAS 入門教學課程](https://github.com/Azure-Samples/storage-dotnet-sas-getting-started)

## <a name="encryption-in-transit"></a>傳輸中加密

### <a name="transport-level-encryption--using-https"></a>Transport-Level Encryption – Using HTTPS

您應該採取以確保 Azure 儲存體資料安全性的另一個步驟是在用戶端和 Azure 儲存體之間加密資料。 第一個建議是一律使用 [HTTPS](https://en.wikipedia.org/wiki/HTTPS) 通訊協定，可確保透過公用網際網路的安全通訊。

如果要有安全的通訊通道，呼叫 REST API 或存取儲存體中的物件時，您應該一律使用 HTTPS。 此外， **共用存取簽章** (可用來委派 Azure 儲存體物件的存取權) 包含一個選項，可指定在使用共用存取簽章時只能使用 HTTPS 通訊協定，以確保任何使用 SAS 權杖送出連結的人都將使用正確的通訊協定。

透過啟用儲存體帳戶[所需的安全傳輸](../storage-require-secure-transfer.md)，您可於呼叫 REST API 來存取儲存體帳戶中的物件時強制使用 HTTPS。 啟用此選項後，使用 HTTP 的連線將被拒絕。

## <a name="encryption-at-rest"></a>待用加密

### <a name="storage-service-encryption-sse"></a>儲存體服務加密 (SSE)

系統會針對所有儲存體帳戶啟用 SSE 且無法停用。 在將資料寫入至「Azure 儲存體」時，SSE 會自動加密資料。 當您從「Azure 儲存體」讀取資料時，「Azure 儲存體」會先將資料解密，然後才傳回資料。 SSE 可讓您無須修改程式碼或在任何應用程式中新增程式碼，即可保護資料。

您可以使用 Microsoft 管理的金鑰，或自己的自訂金鑰。 Microsoft 會產生受控金鑰，然後除了依照內部 Microsoft 原則定義的方式處理金鑰的安全儲存體之外，也會處理其定期輪換。 如需有關使用自訂金鑰的詳細資訊，請參閱[使用 Azure Key Vault 中客戶管理的金鑰進行儲存體服務加密](storage-service-encryption-customer-managed-keys.md)。