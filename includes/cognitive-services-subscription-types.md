---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 674dd30ff3e493ec4c4036f032f82624a6ca5749
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/19/2019
ms.locfileid: "68334246"
---
## <a name="azure-cognitive-service-resource-types"></a>Azure 認知服務資源類型

> [!NOTE]
> 訂用帳戶擁有者可以藉由套用[Azure 原則](https://docs.microsoft.com/azure/governance/policy/overview#policy-definition)、指派「不允許的資源類型」原則定義, 並指定 **, 來停用資源群組和訂閱的認知服務資源建立CognitiveServices/accounts**作為目標資源類型。

您可以透過兩個不同的資源存取 Azure 認知服務:多服務資源或單一服務。 這些訂用帳戶可讓您一次連接到單一服務或多個服務。

### <a name="multi-service-resource"></a>多服務資源

>[!WARNING]
> 目前，以下服務**不**支援多服務金鑰：QnA Maker、語音服務、自訂視覺和異常偵測器。

訂閱多服務認知服務資源:
* 可讓您將單一 Azure 資源用於大部分的 Azure 認知服務。
* 合併您所使用服務的帳單。 如需其他資訊，請參閱[認知服務定價](https://azure.microsoft.com/pricing/details/cognitive-services/)。

### <a name="single-service-resource"></a>單一服務資源

單一服務資源 (例如電腦視覺或語音服務) 僅限於其指定的服務。