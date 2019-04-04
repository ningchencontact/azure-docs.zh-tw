---
title: 設定檢閱工具設定-Content Moderator
titlesuffix: Azure Cognitive Services
description: 用以設定或擷取您的小組、 標記、 連接器、 工作流程和認證 Content Moderator 審核工具。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/15/2019
ms.author: sajagtap
ms.openlocfilehash: f88ccbabc925b651abbc06f571a9d4220ed8aeb2
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2019
ms.locfileid: "58756512"
---
# <a name="configure-the-review-tool"></a>設定檢閱工具

[審核工具](https://contentmoderator.cognitive.microsoft.com)有幾項重要功能，您可以透過存取**設定**儀表板的功能表。

![太內容仲裁者檢閱設定 功能表](images/settings-1.png)

## <a name="manage-team-and-subteams"></a>管理團隊和子團隊

**Team**索引標籤可讓您管理您的團隊和子團隊&mdash;可以收到通知，當特定的使用者群組[人工審核](../review-api.md#reviews)都已啟動。 您只能有一個團隊 （您建立使用檢閱工具註冊時），但您可以建立多個子小組。 小組系統管理員可以邀請成員、 設定其權限，並將它們指派給不同的子團隊。

![檢閱工具小組設定](images/settings-2-team.png)

子小組適合用於建立擴大小組，或專門用來審核特定內容類別的小組。 例如，您可能會傳送的成人內容以供進一步檢閱由不同團隊。

本節說明如何建立子小組，並快速地指派即時的評論。 不過，您可以使用[工作流程](workflows.md)，根據特定準則來指派審核。

### <a name="create-a-subteam"></a>建立子小組

移至**子團隊**區段，然後按一下**加入小組**。 在對話方塊中輸入您的小組名稱，然後按一下**儲存**。

![子小組名稱](images/1-Teams-2.PNG)

#### <a name="invite-teammates"></a>邀請小組成員

您無法指定有人給小組如果它們還不預設小組成員，因此您必須先將檢閱者新增至預設小組。 按一下 [**邀請**上**小組**] 索引標籤。

![邀請使用者](images/invite-users.png)

#### <a name="assign-teammates-to-subteam"></a>指派 subteam 的小組成員

按一下  **Add Member**將從預設小組的成員指派給一或多個子團隊的按鈕。 您只能將現有使用者新增至子小組。 如需新增不在審查工具中的新使用者，請使用 [小組設定] 頁面上的 [邀請] 按鈕來邀請他們。

![指派子小組成員](images/1-Teams-3.PNG)

### <a name="assign-reviews-to-subteams"></a>將檢閱指派給子團隊

一旦您已建立您的子團隊，並指派成員，您可以開始將內容指派[檢閱](../review-api.md#reviews)到這些子團隊。 這是從**檢閱**站台 索引標籤。
若要將內容指派給小組中，按一下 在右上角，選取省略符號**移至**，然後選取小組。

![將影像審核指派給子小組](images/3-review-image-subteam-1.png)

### <a name="switch-between-subteams"></a>切換子小組

如果您是多個小組的成員，您可以切換這些子變更哪些內容檢閱會顯示為您的團隊。 在 **檢閱**索引標籤上，選取標示為 下拉式選單**預設**，然後選取**選擇小組**。 您可以檢視針對不同的子團隊，而是只是其中內容的評論您身為成員。

![切換子小組](images/3-review-image-subteam-2.png)

## <a name="tags"></a>標記

**標記**索引標籤可讓您定義自訂的仲裁標記，除了兩個預設仲裁標記&mdash;**isadult** () 和**isracy** (**r**)。 當您建立自訂的標記時，可用在預設標記與檢閱中。 您可以變更哪些標籤出現在檢閱中藉由切換其可見性設定。

![標記檢視，包括 「 」 顯示的核取方塊](images/tags-4-disable.png)

### <a name="create-custom-tags"></a>建立自訂的標記

若要建立新的標記，您必須對應欄位中輸入簡短的程式碼、 名稱和描述。

- **簡短的程式碼**:輸入您的標籤的兩個字母代碼。 範例： **cb**
- **名稱**：輸入簡短且具描述性標記名稱不含空格的小寫。 範例： **isbullying**。
- **描述**: （選擇性） 輸入的內容種類的描述，您的標籤目標。 範例：**描述或執行個體的網路 bullying**。

按一下 **新增**以新增標記，然後按一下**儲存**當您完成建立標記。

![檢閱工具建立新標記 對話方塊](images/settings-3-tags.png)

### <a name="delete-tags"></a>刪除標記

您可以藉由選取 在標記清單中，其項目旁邊的垃圾桶圖示刪除自訂的標記，但您無法刪除預設標籤。

## <a name="connectors"></a>連接器

**連接器**索引標籤可讓您管理您的連接器，也就是一種不同的方式可以處理的內容做為內容一部分的服務特定外掛程式[工作流程](../review-api.md#workflows)。

當您建立工作流程的預設連接器是 Content Moderator 連接器，可以將標記的內容**成人**或**猥褻**、 尋找不雅內容，並依此類推。 不過，您可以使用其他的連接器，此處列出，但前提是有其各自的服務認證 (若要使用人臉識別 API 連接器，例如，您必須取得[人臉識別 API](https://docs.microsoft.com/azure/cognitive-services/face/overview)訂用帳戶金鑰)。

[審核工具](./human-in-the-loop.md)包括下列連接器：

- Emotion API
- 人臉識別 API
- PhotoDNA Cloud Service
- 文字分析 API

### <a name="add-a-connector"></a>新增連接器

若要新增的連接器 (並使它可供使用的內容中使用[工作流程](../review-api.md#workflows))，選取適當**Connect**  按鈕。 在下一步 對話方塊中，輸入您的訂用帳戶金鑰，該服務。 完成之後，您的新連接器應該會出現在頁面頂端。

![Content Moderator 連接器設定](images/settings-4-connectors.png)

## <a name="workflows"></a>工作流程

**工作流程**索引標籤可讓您管理您[工作流程](../review-api.md#workflows)。 工作流程是雲端為基礎的篩選，如需內容，以及它們使用連接器，以不同的方式排序的內容，並採取適當動作。 在這裡，您可以定義、 編輯及測試您的工作流程。 請參閱[定義和使用工作流程](Workflows.md)如需如何執行這項操作的指引。

![Content Moderator 工作流程設定](images/settings-5-workflows.png)

## <a name="credentials"></a>認證

**認證** 索引標籤提供快速存取您內容仲裁的訂用帳戶金鑰，您將需要存取任何仲裁服務從用戶端 SDK 或 REST 呼叫。

![Content Moderator 認證](images/settings-6-credentials.png)

### <a name="use-external-credentials-for-workflows"></a>工作流程使用外部認證

[審核工具](https://contentmoderator.cognitive.microsoft.com)會產生免費的試用版金鑰，當您註冊，但您也可以設定它使用現有的 Azure 內容仲裁服務金鑰從您的 Azure 帳戶。 這建議用於大規模的案例，因為免費的試用版金鑰有嚴格的使用量限制 ([價格和限制](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/))。

如果您已建立[Content Moderator 資源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator)在 Azure 中，瀏覽至它在 Azure 入口網站，然後選取**金鑰**刀鋒視窗。 其中一個金鑰的複本。

![Azure 入口網站中的 Content Moderator 金鑰](images/credentials-azure-portal-keys.PNG)

在 [審核工具](https://contentmoderator.cognitive.microsoft.com)的**認證**索引標籤上，移至**工作流程設定**窗格中，選取**編輯**，並將您的金鑰貼到**Ocp Apim-訂用帳戶金鑰**欄位。 現在，呼叫仲裁 Api 的工作流程會使用您的 Azure 認證。

> [!NOTE]
> 其他兩個欄位**工作流程設定**窗格會用於自訂的詞彙和映像清單。 請參閱[自訂條款](../try-terms-list-api.md)或是[自訂映像](../try-image-list-api.md)指南，以了解這些。

### <a name="use-your-azure-account-with-the-review-apis"></a>檢閱 Api 搭配使用您的 Azure 帳戶

若要檢閱 Api 以使用您的 Azure 金鑰，您需要擷取資源識別碼。 移至您的內容仲裁資源在 Azure 入口網站，然後選取**屬性**刀鋒視窗。 複製的資源識別碼值，並將它貼至**列入允許清單中的資源識別碼**欄位的檢閱工具**認證** 索引標籤。

![Azure 入口網站中的 Content Moderator 資源識別碼](images/credentials-azure-portal-resourceid.PNG)

如果您在兩個位置中輸入您的訂用帳戶金鑰，您檢閱工具的帳戶所隨附的試用版金鑰不會使用，但仍可供使用。

## <a name="next-steps"></a>後續步驟

請遵循[檢閱工具快速入門](../quick-start.md)若要開始在內容仲裁的情況下使用審核工具。