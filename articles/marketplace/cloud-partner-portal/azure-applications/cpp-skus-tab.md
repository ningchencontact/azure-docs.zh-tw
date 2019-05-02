---
title: 設定 Sku 的 Azure 應用程式供應項目 |Azure Marketplace
description: 如何設定 Azure 受控應用程式和 Azure 解決方案範本的 SKU。
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pabutler
ms.openlocfilehash: ef4ea2419c64d0376023ea5d291460df48a51c63
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943413"
---
# <a name="azure-application-skus-tab"></a>Azure 應用程式 SKU 索引標籤

本文說明如何使用 SKU 索引標籤來建立 Azure 應用程式。 

> [!IMPORTANT]
> 設定 SKU 的步驟與受控應用程式供應項目和解決方案範本供應項目的步驟不同。 如需了解這些差異，請參閱這篇文章。 

## <a name="configure-azure-application-skus"></a>設定 Azure 應用程式 SKU

### <a name="create-a-new-sku"></a>建立新的 SKU

若要建立新的 SKU，使用下列步驟：

1. 選取 [SKU] 索引標籤。
2. 在 SKU 下，選取 [+ 新增 SKU]。

    ![新增 SKU 提示](./media/azureapp-plus-sku.png)

3. 在 [新增 SKU] 快顯視窗中，輸入 [SKU 識別碼]。 此識別碼的長度限制為 50 個字元，且只能包含小寫英數字元、連字號或底線。 SKU 識別碼不能以連字號結尾。
4. 客戶會看到此 SKU 識別碼顯示在產品的 URL、Resource Manager 範本 (如果適用) 和計費報告中。 供應項目發行之後，您無法修改此識別碼。

### <a name="sku-details-for-a-solution-template"></a>解決方案範本的 SKU 詳細資料

下一個螢幕擷取畫面顯示 SKU 詳細資料表單解決方案範本。

![解決方案範本的 SKU 詳細資料表單](./media/azureapp-sku-details-solutiontemplate.png)

提供下列的 SKU 值。  需要加上星號的欄位。

|    欄位         |       描述                                                            |
|  ---------       |     ---------------                                                          |
|  **標題\***     | SKU 的標題。 此標題顯示在此項目的資源庫中。   |
| **摘要\***    | SKU 的簡短摘要描述。 (長度上限是 100 個字元。)  |
| **說明\*** | SKU 的詳細的描述。 支援基本的 HTML。                 | 
| **SKU 類型\***   | 類型的 Azure 應用程式方案，然後選取 ***方案範本**此案例中。 |
| **雲端可用性\*** | SKU 的位置。 預設值是**公用 Azure**。  <b/>   **公用 Azure** -應用程式將會部署至整合 marketplace 的所有公用 Azure 區域中的客戶。  <b/>   **Azure Government 雲端**-將會在 Azure Government 雲端中部署應用程式。 之前發行至[Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)，Microsoft 建議發行者測試及驗證解決方案能如預期般在此環境中。 若要執行暫存和測試，請要求[試用帳戶](https://azure.microsoft.com/offers/ms-azr-usgov-0044p/)。  |
| **這是私用的 SKU 嗎？\*** | 選取 **是**如果此 SKU 只會提供給客戶的選取群組。 |
|   |   |

  > [!NOTE] 
  > Microsoft Azure Government 是政府社群雲端，US 聯邦政府、本地客戶或部落客戶，及符合資格可以為這些實體提供服務的合作夥伴，擁有此雲端的控管權限。


### <a name="sku-details-for-managed-application"></a>受控應用程式的 SKU 詳細資料

下一個螢幕擷取畫面會顯示受控應用程式的 SKU 詳細資料表單。

   ![受控應用程式的 SKU 詳細資料表單](./media/azureapp-sku-details-managedapplication.png)

設定下列的 SKU 設定。 需要加上星號的欄位。

|    欄位         |       描述                                                            |
|  ---------       |     ---------------                                                          |
|  **標題\***     | SKU 的標題。 此標題顯示在此項目的資源庫中。   |
| **摘要\***    | SKU 的簡短摘要描述。 (長度上限是 100 個字元。)  |
| **說明\*** | SKU 的詳細的描述。 支援基本的 HTML。                 | 
| **SKU 類型\***   | 類型的 Azure 應用程式方案，然後選取 ***受管理的應用程式**此案例中。 
| **雲端可用性\*** | SKU 的位置。 預設值是**公用 Azure**。  <b/>   **公用 Azure** -應用程式將會部署至整合 marketplace 的所有公用 Azure 區域中的客戶。  <b/>   **Azure Government 雲端**-將會在 Azure Government 雲端中部署應用程式。 之前發行至[Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)，Microsoft 建議發行者測試及驗證解決方案能如預期般在此環境中。 若要執行暫存和測試，請要求[試用帳戶](https://azure.microsoft.com/offers/ms-azr-usgov-0044p/)。   Microsoft Azure Government 是政府社群雲端，US 聯邦政府、本地客戶或部落客戶，及符合資格可以為這些實體提供服務的合作夥伴，擁有此雲端的控管權限。 |
| **這是私用的 SKU 嗎？\*** | 選取 **是**如果此 SKU 只會提供給客戶的選取群組。 |
| **國家/地區可用性\*** | 使用**區域以選取**若要檢視可用的國家/地區的清單。 檢查每個國家/地區，然後選取 [確定] 以儲存您的選擇。  <b/>   ![國家/地區和區域可用性清單](./media/azure-app-select-country-region.png)  |
| **舊的定價\*** | Sku，以美元為單位每月價格。 價格會根據設定時當前匯率的當地貨幣來設定。 由於您最後會擁有這些設定，請驗證這些選項。 若要個別設定或檢視每個國家/地區的價格，請匯出價格試算表，然後匯入自訂價格。  您必須儲存您的定價變更，即可匯出/匯入的定價資料。  |
| **簡化的貨幣的價格\*** | Sku，以美元為單位每月價格。 這必須與舊的定價相同。 如需詳細資訊，請參閱[簡化的貨幣定價](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-update-existing-offer)。 |
|  |  |


### <a name="package-details-for-solution-template"></a>解決方案範本的套件詳細資料

   ![解決方案範本的套件詳細資料](./media/azureapp-sku-pkgdetails-solutiontemplate.png)

提供下列**套件詳細資料**值。  需要加上星號的欄位。

- **版本\*** -您要上傳封裝的版本。 版本標籤格式應為 X.Y.Z，其中 X、Y 和 Z 是整數。
- **封裝檔案 (.zip)\***  -此套件包含下列檔案，並儲存在.zip 檔案。
  - MainTemplate.json - 用來部署解決方案/應用程式並建立針對解決方案定義之資源的部署範本檔案。 如需詳細資訊，請參閱[如何製作部署範本檔案](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template)。
  - createUIDefinition.json - Azure 入口網站會使用這個檔案來產生使用者介面，以便佈建此解決方案/應用程式。 如需詳細資訊，請參閱[為您的受控應用程式建立 Azure 入口網站使用者介面](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)。

  >[!IMPORTANT] 
  >此套件應該包含佈建此應用程式所需的任何巢狀範本或指令碼。 MainTemplate.json 檔案與 createUIDefinition.json 檔案必須位於根資料夾。


### <a name="package-details-for-managed-application"></a>受控應用程式的套件詳細資料

   ![受控應用程式的套件詳細資料](./media/azureapp-sku-pkgdetails-managedapplication.png)

提供下列套件詳細資料。  需要加上星號的欄位。

- **版本\*** -您要上傳封裝的版本。 版本標籤格式應為 X.Y.Z，其中 X、Y 和 Z 是整數。
- **封裝檔案 (.zip)\***  -此套件包含下列檔案，並儲存在.zip 檔案。
  - applianceMainTemplate.json - 用來部署解決方案/應用程式並建立所定義資源的部署範本檔案。 如需詳細資訊，請參閱[快速入門：使用 Azure 入口網站建立及部署 Azure Resource Manager 範本](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)。 
  - applianceCreateUIDefinition.json - Azure 入口網站會使用這個檔案來產生使用者介面，以便佈建此解決方案/應用程式。 如需詳細資訊，請參閱[為您的受控應用程式建立 Azure 入口網站使用者介面](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)。
  - mainTemplate.json - 只包含 Microsoft.Solution/appliances 資源的範本檔案。 如需詳細資訊，請參閱[了解 Azure Resource Manager 範本的結構和語法](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)。 <br>
請注意這項資源的下列索引鍵屬性：
    - “kind” - 在 Marketplace 受控應用程式案例中，這個值應為 "Marketplace"。
    - "ManagedResourceGroupId” - 客戶訂用帳戶中的資源群組，當中會部署 applianceMainTemplate.json 中所定義的所有資源。
    - "PublisherPackageId" - 唯一識別套件的字串。 此值必須以下列方式建構：它是 [publisherId].[OfferId]-preview[SKUID].[PackageVersion]. 的串連。

  >[!IMPORTANT] 
  >此套件應該包含佈建此應用程式所需的任何巢狀範本或指令碼。 這些檔案必須位在根資料夾中：MainTemplate.json、applianceMainTemplate.json 和 applianceCreateUIDefinition.json。

- **租用戶識別碼\*** -貴組織的 Azure Active Directory 租用戶識別碼。
- **啟用 JIT 存取嗎？\***  – 選取**是**啟用 Just In Time 使用這項優惠的客戶部署的管理存取權。

  >[!NOTE] 
  >如果您啟用 JIT，則必須更新 CreateUiDefinition.json 檔案以支援 JIT 存取。

針對受控應用程式，您必須設定授權和原則設定。


#### <a name="authorization"></a>授權

將使用者、群組或應用程式的 Azure Active Directory 識別碼，新增至您要向其授與受控資源群組的存取權。 授與的權限由角色定義識別碼所指示。可能的擁有者、 參與者或任何自訂角色。


#### <a name="policy-settings"></a>原則設定

新增受控應用程式符合的原則。 如需深入了解 Azure 資源原則，請參閱[什麼是 Azure 原則？](../../../governance/policy/overview.md)

   ![受控應用程式的授權和原則設定](./media/azureapp-sku-details-managedapp-auth-policy.png)

**如需建立新的授權：**

1. 在 [授權] 下，選取 [+ 新增授權]。
2. 針對**主體識別碼**，將使用者、群組或應用程式的 Azure Active Directory 識別碼，輸入至您要向其授與受控資源群組的存取權。 授與的權限由角色定義所指示。
3. 針對 [角色定義]，從下拉式清單中選取其中一個選項：擁有者或參與者。 如需詳細資訊，請參閱 [Azure 資源的內建角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)。

>[!NOTE] 
>可以新增多個授權。 不過，建議要建立 Active Directory 使用者群組，並在 "PrincipalId" 中指定其識別碼。 如此可新增更多使用者到使用者群組，而不需要更新 SKU。

**如需建立新的原則：**

1. 在 [原則設定] 下，選取 [+ 新增原則]。
2. 針對 [原則名稱]，請輸入原則名稱。 該名稱的長度上限為 50 個字元。
3. 針對 [原則]，從下拉式清單中選取其中一個選項： 選擇當應用程式使用資料時，資料提供者要啟用的原則。 如需詳細資訊，請參閱 [Azure 原則範例](https://docs.microsoft.com/azure/governance/policy/samples/index)。

    ![受控應用程式的授權設定](./media/azureapp-sku-policy-settings.png)

4. 針對 [原則 SKU]，選取免費或標準作為原則 SKU 的類型。 稽核原則需要標準 SKU。


## <a name="next-steps"></a>後續步驟

您將進一步說明您的供應項目，並提供中的行銷資產[marketplace](./cpp-marketplace-tab.md)。 
