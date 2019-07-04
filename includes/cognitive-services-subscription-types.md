---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: ad1527a5e7f1cb2ff1beb9ddace5460f41bb8a87
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461499"
---
## <a name="azure-cognitive-service-subscription-types"></a>Azure 認知服務訂用帳戶類型

> [!NOTE]
> 訂用帳戶擁有者可以藉由套用 [Azure 原則](https://docs.microsoft.com/azure/governance/policy/overview#policy-definition)、指派「不允許的資源類型」原則定義，以及指定 **Microsoft.CognitiveServices/accounts** 作為目標資源類型，針對資源群組或訂用帳戶停用認知服務帳戶的建立。

您可以透過兩個不同的訂用帳戶來存取 Azure 認知服務：多服務的訂用帳戶或其中一個單一服務。 這些訂用帳戶可讓您一次連接到單一服務或多個服務。

### <a name="multi-service-subscription"></a>多服務訂用帳戶

>[!WARNING]
> 目前，以下服務**不**支援多服務金鑰：QnA Maker、 語音服務、 自訂的視覺和異常偵測器。

Azure 認知服務的多重服務訂用帳戶可讓您針對大部分的 Azure 認知服務，使用單一訂用帳戶和 Azure 資源，並合併來自您所使用的服務計費。 如需其他資訊，請參閱[認知服務定價](https://azure.microsoft.com/pricing/details/cognitive-services/)。

### <a name="single-service-subscription"></a>單一服務訂用帳戶

單一服務，例如電腦視覺或語音服務訂用帳戶。 單一服務訂用帳戶僅限於該資源。 
