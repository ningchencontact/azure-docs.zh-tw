---
title: 容器支援
titleSuffix: Azure Cognitive Services
description: 瞭解如何建立 Azure 容器實例資源。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 1679862b1660d3c8b2505c6e0c54f203f5d4665d
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383523"
---
## <a name="create-an-azure-container-instance-resource"></a>建立 Azure 容器實例資源

1. 移至容器實例的 [[建立](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances)] 頁面。

2. 在 [**基本**] 索引標籤上，輸入下列詳細資料：

    |設定|Value|
    |--|--|
    |Subscription|選取您的訂用帳戶。|
    |Resource group|選取可用的資源群組，或建立一個新的 [`cognitive-services`]。|
    |容器名稱|輸入 [名稱]，例如 `cognitive-container-instance`。 名稱必須是小寫。|
    |Location|選取要部署的區域。|
    |映像類型|如果您的容器映射儲存在不需要認證的容器登錄中，請選擇 [`Public`]。 如果存取您的容器映射需要認證，請選擇 [`Private`]。 如需容器映射是否 `Public` 或 `Private` （「公開預覽」）的詳細資訊，請參閱[容器存放庫和映射](../../cognitive-services-container-support.md#container-repositories-and-images)。 |
    |映像名稱|輸入認知服務容器位置。 位置是用來做為 `docker pull` 命令之引數的位置。 請參閱[容器存放庫和映射](../../cognitive-services-container-support.md#container-repositories-and-images)，以取得可用映射名稱及其對應的儲存機制。<br><br>映射名稱必須是完整的指定三個部分。 首先，容器登錄，然後是存放庫，最後是映射名稱： `<container-registry>/<repository>/<image-name>`。<br><br>以下是範例，`mcr.microsoft.com/azure-cognitive-services/keyphrase` 代表 Azure 認知服務存放庫下 Microsoft Container Registry 中的關鍵片語擷取映射。 另一個範例是，`containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` 代表容器預覽容器登錄的 Microsoft 存放庫中的語音轉換文字映射。 |
    |OS 類型|`Linux`|
    |大小|針對您特定的認知服務容器，將大小變更為建議的建議：<br>2個 CPU 核心<br>4GB

3. 在 [**網路**功能] 索引標籤上，輸入下列詳細資料：

    |設定|Value|
    |--|--|
    |連接埠|將 TCP 埠設定為 [`5000`]。 公開端口5000上的容器。|

4. 在 [ **Advanced** ] 索引標籤上，為 Azure 容器實例資源的容器帳單設定輸入必要的**環境變數**：

    | 索引鍵 | Value |
    |--|--|
    |`apikey`|從資源的 [**金鑰**] 頁面複製。 它是32英數位元字串，不含空格或連字號，`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`。|
    |`billing`|從資源的 [**總覽**] 頁面複製的。|
    |`eula`|`accept`|

1. 按一下 [**審查並建立**]
1. 通過驗證之後，按一下 [**建立**] 以完成建立程式
1. 當資源部署成功時，即已準備就緒