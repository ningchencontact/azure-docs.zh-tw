---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 3b808f4eb853cf05eb08cd1acf08dedccabf71a6
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70274585"
---
## <a name="azure-cognitive-service-resource-types"></a>Azure 認知服務資源類型

<!-- > [!NOTE]
> Subscription owners can disable the creation of Cognitive Services resources for resource groups and subscriptions by applying [Azure policy](https://docs.microsoft.com/azure/governance/policy/overview#policy-definition), assigning a “Not allowed resource types” policy definition, and specifying **Microsoft.CognitiveServices/accounts** as the target resource type. -->
您可以透過兩個不同的資源存取 Azure 認知服務：多服務資源或單一服務。 這些訂用帳戶可讓您一次連接到單一認知服務或多個認知服務。

### <a name="multi-service-resource"></a>多服務資源

訂閱多服務認知服務資源：
* 可讓您將單一 Azure 資源用於大部分的 Azure 認知服務。
* 您會取得可與多個 Azure 認知服務搭配使用的單一金鑰。
* 合併您所使用服務的帳單。 如需其他資訊，請參閱[認知服務定價](https://azure.microsoft.com/pricing/details/cognitive-services/)。

>[!WARNING]
> 目前，以下服務**不**支援多服務金鑰：QnA Maker、語音服務、自訂視覺和異常偵測器。

### <a name="single-service-resource"></a>單一服務資源

訂閱單一服務認知服務資源：
* 可讓您使用您為其建立資源的指定認知服務（例如電腦視覺或語音服務）。
* 您會取得針對您為其建立資源的認知服務特定的金鑰。