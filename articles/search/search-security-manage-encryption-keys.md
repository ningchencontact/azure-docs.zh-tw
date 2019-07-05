---
title: 在加密在 Azure 金鑰保存庫 （預覽）-Azure 搜尋服務中使用客戶管理金鑰
description: 索引和在 Azure 搜尋服務的同義字地圖，透過您建立和管理 Azure 金鑰保存庫中的索引鍵的補充伺服器端加密。
author: NatiNimni
manager: jlembicz
ms.author: natinimn
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.custom: ''
ms.openlocfilehash: 949628fa52b4b020d70b75f4a0e7895f1e0f8bba
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485325"
---
# <a name="azure-search-encryption-using-customer-managed-keys-in-azure-key-vault"></a>在 Azure Key Vault 中使用客戶管理金鑰的 azure 搜尋服務加密

> [!Note]
> 使用客戶管理金鑰的加密是處於預覽階段，應用程式不是用於生產環境而定。 [REST API 版本 2019-05-06-Preview](search-api-preview.md) 提供此功能。 您也可以使用.NET SDK 版本 8.0-preview。
>
> 這項功能不適用於免費服務。 您必須使用建立當天或之後於 2019年-01-01 的可計費的搜尋服務。 沒有任何入口網站支援此功能。

根據預設，Azure 搜尋服務會加密待用與使用者的內容[服務管理的金鑰](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest#data-encryption-models)。 您可以使用您建立和管理在 Azure Key Vault 的金鑰額外的加密層級來補充預設加密。 這篇文章會引導您逐步完成。

透過整合支援伺服器端加密[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)。 您可以建立自己的加密金鑰，然後將其儲存在金鑰保存庫中，或是使用 Azure Key Vault 的 API 來產生加密金鑰。 使用 Azure Key Vault，您也可以稽核金鑰使用方法。 

設定使用客戶管理金鑰的加密是在索引或同義字對應層級時建立這些物件，而不是在搜尋服務層級上。 您無法加密已經存在的內容。 

您可以使用不同的索引鍵，從不同的金鑰保存庫。 這表示在單一搜尋服務可以裝載多個加密的 indexes\synonym 對應，每個可能使用不同的客戶管理金鑰，不會使用客戶管理金鑰加密的 indexes\synonym 對應與加密。 

## <a name="prerequisites"></a>必要條件

此範例中，會使用下列服務。 

+ [建立 Azure 搜尋服務](search-create-service-portal.md)，或在您目前的訂用帳戶下方[尋找現有服務](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 您可以使用本教學課程的免費服務。

+ [建立 Azure Key Vault 資源](https://docs.microsoft.com/azure/key-vault/quick-create-portal#create-a-vault)或尋找現有的保存庫訂用帳戶。

+ [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)或是[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)用於設定工作。

+ [Postman](search-get-started-postman.md)， [Azure PowerShell](search-create-index-rest-api.md)並[Azure 搜尋服務 SDK](https://aka.ms/search-sdk-preview)可以用來呼叫 REST API 的預覽。 沒有任何入口網站或客戶管理的加密，在此階段的.NET SDK 支援。

## <a name="1---enable-key-recovery"></a>1-啟用金鑰修復

這個步驟是選擇性的但強烈建議。 建立 Azure Key Vault 資源之後，啟用**虛刪除**並**清除保護**中選取的金鑰保存庫，藉由執行下列 PowerShell 或 Azure CLI 命令：   

```powershell
$resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "<vault_name>").ResourceId

$resource.Properties | Add-Member -MemberType NoteProperty -Name "enableSoftDelete" -Value 'true'

$resource.Properties | Add-Member -MemberType NoteProperty -Name "enablePurgeProtection" -Value 'true'

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

```azurecli-interactive
az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
```

>[!Note]
> 客戶管理金鑰 功能與加密的本質，因為 Azure 搜尋服務將無法擷取您的資料，如果您的 Azure Key vault 金鑰遭到刪除。 若要避免因意外的 Key Vault 金鑰刪除的資料遺失，強烈建議在所選的金鑰保存庫啟用虛刪除 」 和 「 清除的保護。 如需詳細資訊，請參閱 < [Azure Key Vault 的虛刪除](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)。   

## <a name="2---create-a-new-key"></a>2-建立新的金鑰

如果您使用現有的金鑰來加密 Azure 搜尋服務內容，請略過此步驟。

1. [登入 Azure 入口網站](https://portal.azure.com)並瀏覽至金鑰保存庫儀表板。

1. 選取 **按鍵**從左側的導覽窗格中，設定，然後按一下 **+ 產生/匯入**。

1. 在 [建立金鑰]  窗格中，從 [選項]  清單中選擇您要用來建立金鑰的方法。 您可以 [產生]  新的金鑰、[上傳]  現有金鑰，或使用 [還原備份]  來選取金鑰的備份。

1. 請輸入**名稱**針對您的金鑰，並選擇性地選取其他的索引鍵屬性。

1. 按一下 **建立**按鈕以開始部署。

請記下的金鑰識別碼 – 這由**機碼值的 Uri**，則**機碼名稱**，而**金鑰版本**。 您將需要這些檔案來定義 Azure 搜尋服務中的加密的索引。
 
![建立新的金鑰保存庫金鑰](./media/search-manage-encryption-keys/create-new-key-vault-key.png "建立新的金鑰保存庫金鑰")

## <a name="3---create-a-service-identity"></a>3-建立服務識別

將身分識別指派給您的搜尋服務，可讓您授與您的搜尋服務的 Key Vault 存取權限。 您的搜尋服務會使用其身分識別，來向 Azure 金鑰保存庫。

Azure 搜尋服務支援兩種方式來指派身分識別： 受管理的身分識別或外部管理 Azure Active Directory 應用程式。 

可能的話，請使用受控身分識別。 將身分識別指派給您的搜尋服務的最簡單的方式，且應該在大部分情況下。 如果您使用多個索引鍵索引和同義字地圖，或如果您的解決方案是 disqualifies 身分識別型驗證的分散式架構中使用進階[外部管理 Azure Active Directory 方法](#aad-app)結尾的這篇文章所述。

 一般情況下，受管理的身分識別可讓您向 Azure 金鑰保存庫，而不需要將認證儲存在程式碼中的搜尋服務。 這種類型的受管理的身分識別生命週期會連結到您的搜尋服務，只能有一個受管理的身分識別的生命週期。 [深入了解受管理身分識別](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。

1. 若要建立的受管理的身分識別[toAzure 入口網站中登入](https://portal.azure.com)並開啟您的搜尋服務儀表板。 

1. 按一下 **身分識別**的左側的導覽窗格中，變更其狀態變更為**上**，然後按一下**儲存**。

![啟用受管理的身分識別](./media/search-enable-msi/enable-identity-portal.png "啟用受管理的身分識別")

## <a name="4---grant-key-access-permissions"></a>4-授與金鑰的存取權限

若要啟用您的搜尋服務，使用您的金鑰保存庫金鑰，您必須授與您的搜尋服務的特定存取權限。

在任何時候，可以撤銷存取權限。 一旦撤銷，則會變成無法使用任何搜尋服務索引或同義字對應使用該金鑰保存庫。 在稍後還原金鑰保存庫的存取權限，將會還原 index\synonym 對應的存取。 如需詳細資訊，請參閱 <<c0> [ 保護的金鑰保存庫的存取安全](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)。

1. [登入 Azure 入口網站](https://portal.azure.com)然後開啟您的金鑰保存庫概觀頁面。 

1. 選取 **存取原則**左側的導覽窗格中，從設定，然後按一下 **+ 新增**。

   ![加入新的金鑰保存庫存取原則](./media/search-manage-encryption-keys/add-new-key-vault-access-policy.png "新增新的金鑰保存庫存取原則")

1. 按一下 **選取主體**，然後選取您的 Azure 搜尋服務。 依名稱或出現在之後啟用受管理的身分識別的物件識別碼，您可以搜尋它。

   ![選取的金鑰保存庫的存取原則主體](./media/search-manage-encryption-keys/select-key-vault-access-policy-principal.png "選取的金鑰保存庫的存取原則主體")

1. 按一下 **金鑰權限**，然後選取*取得*，*解除包裝金鑰*並*包裝金鑰*。 您可以使用*Azure Data Lake 儲存體或 Azure 儲存體*範本以快速地選取 必要權限。

   必須授與 azure 搜尋服務，使用下列[存取權限](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-operations):

   * *取得*-可讓您擷取金鑰保存庫中金鑰的公開部分的搜尋服務
   * *包裝金鑰*-可讓您的搜尋服務來使用您的金鑰來保護內部的加密金鑰
   * *「 解除包裝金鑰*-可讓您的搜尋服務來使用您的金鑰來解除包裝的內部加密金鑰

   ![選取金鑰保存庫的存取原則金鑰權限](./media/search-manage-encryption-keys/select-key-vault-access-policy-key-permissions.png "選取金鑰保存庫的存取原則金鑰權限")

1. 按一下  **確定**並**儲存**存取原則的變更。

> [!Important]
> Azure 搜尋服務中的加密的內容設定為使用特定的 Azure Key Vault 金鑰與特定**版本**。 如果您變更索引鍵或版本，索引或同義字地圖必須更新為使用新 key\version**之前**刪除先前 key\version。 不這麼做的話將會呈現索引或同義字對應無法使用，在您將無法解密內容，一旦金鑰存取權限將會遺失。   

## <a name="5---encrypt-content"></a>5-加密內容

建立一個使用客戶管理的金鑰加密的同義資料表或索引對應目前還不能使用 Azure 入口網站。 您可以使用 Azure 搜尋服務 REST API 來建立這類索引或同義字地圖。

索引和同義字對應支援，一個新的最上層**encryptionKey**用指定的索引鍵屬性。 

使用**金鑰保存庫 Uri**，**機碼名稱**並**金鑰版本**的金鑰保存庫金鑰，我們可以建立**encryptionKey**定義：

```json
{
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
  }
}
```
> [!Note] 
> 沒有任何這些金鑰保存庫詳細資料會被視為祕密，而且可以輕鬆地擷取瀏覽至 Azure 入口網站中相關的 Azure Key Vault 金鑰頁面。

如果您使用 AAD 應用程式的金鑰保存庫驗證，而不是使用受控身分識別，新增 AAD 應用程式**存取認證**加密金鑰： 
```json
{
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

## <a name="example-index-encryption"></a>範例：索引加密
在找不到建立新的索引，透過 REST API 的詳細資料[建立索引 (Azure 搜尋服務 REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index)，其中唯一的差別指定的加密金鑰的詳細資料做為索引定義的一部分： 

```json
{
 "name": "hotels",  
 "fields": [
  {"name": "HotelId", "type": "Edm.String", "key": true, "filterable": true},
  {"name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false},
  {"name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene"},
  {"name": "Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
  {"name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
  {"name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true},
  {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true},
  {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true},
  {"name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true},
  {"name": "Location", "type": "Edm.GeographyPoint", "filterable": true, "sortable": true},
 ],
 "encryptionKey": {
   "keyVaultUri": "https://demokeyvault.vault.azure.net",
   "keyVaultKeyName": "myEncryptionKey",
   "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
 }
}
```
您可以現在傳送索引的建立要求，然後再啟動 通常使用索引。

## <a name="example-synonym-map-encryption"></a>範例：同義字對應加密

建立新的同義字地圖，透過 REST API 的詳細資訊，請參閱[建立 (Azure 搜尋服務 REST API) 的同義字地圖](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map)，其中唯一的差別指定的加密金鑰的詳細資料的同義字對應定義的一部分： 

```json
{   
  "name" : "synonymmap1",  
  "format" : "solr",  
  "synonyms" : "United States, United States of America, USA\n
  Washington, Wash. => WA",
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
  }
}
```
您可以現在傳送同義字地圖建立要求，然後再啟動 通常使用它。

>[!Important] 
> 雖然**encryptionKey**無法新增至現有的 Azure 搜尋服務索引或同義字地圖，它可能會更新為任何三個金鑰保存庫詳細資料 （例如，更新金鑰的版本） 提供不同的值。 當變更為新的 Key Vault 金鑰或新的金鑰版本，Azure 搜尋服務索引或同義字地圖，便會使用金鑰必須先更新為使用新 key\version**之前**刪除先前 key\version。 不這麼做的話將會呈現索引，或無法使用，因為它無法再解密內容一次索引鍵存取的同義字地圖會遺失。   
> 在稍後還原金鑰保存庫的存取權限，將會還原內容的存取。

## <a name="aad-app"></a> 進階：使用外部的受管理的 Azure Active Directory 應用程式

當受管理的身分識別不可行，您可以建立 Azure Active Directory 應用程式使用的安全性主體的 Azure 搜尋服務。 具體來說，受管理的身分識別不是在這些情況下可行：

* 您無法直接授與您的搜尋服務的金鑰保存庫的存取權限 （例如，如果搜尋服務會與 Azure 金鑰保存庫中不同的 Active Directory 租用戶中）。

* 在單一搜尋服務，才能裝載多個加密的 indexes\synonym 對應，各使用不同的金鑰，從不同的金鑰保存庫中，每個金鑰保存庫必須使用其中**不同的身分識別**進行驗證。 如果使用不同的身分識別來管理不同的金鑰保存庫，就不需要，請考慮使用上述的受管理的身分識別選項。  

為了滿足這類的拓撲，Azure 搜尋服務支援使用 Azure Active Directory (AAD) 應用程式，您的搜尋服務和金鑰保存庫之間進行驗證。    
若要在入口網站中建立 AAD 應用程式：

1. [建立 Azure Active Directory 應用程式](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application)。

1. [取得應用程式識別碼和驗證金鑰](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)因為這些都需要建立加密的索引。 您必須提供的值包括**應用程式識別碼**並**驗證金鑰**。

>[!Important]
> 決定要使用 AAD 應用程式的驗證，而不是受管理的身分識別時，考慮到 Azure 搜尋服務來管理您的 AAD 應用程式，代替您未獲授權，並由您管理 AAD 應用程式，例如定期輪替應用程式驗證金鑰。
> 時變更 AAD 應用程式或其驗證金鑰，請 Azure 搜尋服務索引或同義字地圖會使用該應用程式，必須先更新為使用新的應用程式 ID\key**之前**刪除先前的應用程式或其索引鍵和前撤銷您的 Key Vault 存取權的授權。
> 不這麼做的話將會呈現索引，或無法使用，因為它無法再解密內容一次索引鍵存取的同義字地圖會遺失。   

## <a name="next-steps"></a>後續步驟

如果您不熟悉 Azure 安全性架構，請檢閱[Azure 安全性文件](https://docs.microsoft.com/azure/security/)，以及特別是，這篇文章：

> [!div class="nextstepaction"]
> [資料靜態加密](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)
