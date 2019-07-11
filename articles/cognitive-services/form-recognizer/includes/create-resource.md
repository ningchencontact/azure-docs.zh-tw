---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: a37087c90b6c9b3629402c7a8c2fa5861e46ae9a
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592512"
---
當您獲得使用表單辨識器的存取權時，您將會收到「歡迎使用」電子郵件，內含數個連結和資源。 請使用該訊息中的 [Azure 入口網站] 連結開啟 Azure 入口網站，並建立表單辨識器資源。 在 [建立]  窗格中，提供下列資訊：

|    |    |
|--|--|
| **名稱** | 資源的描述性名稱。 建議您使用描述性名稱，例如 *MyNameFormRecognizer*。 |
| **訂用帳戶** | 選取已獲存取權的 Azure 訂用帳戶。 |
| **位置** | 您的認知服務執行個體的位置。 位置不同可能會造成延遲，但不會影響您資源執行階段的可用性。 |
| **定價層** | 資源的成本取決於您選擇的定價層和您的使用量。 如需詳細資訊，請參閱 API [定價詳細資料](https://azure.microsoft.com/pricing/details/cognitive-services/)。
| **資源群組** | 將包含您的資源的 [Azure 資源群組](https://docs.microsoft.com/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access#what-is-an-azure-resource-group)。 您可以建立新的群組，或將群組新增到既有的群組。 |

> [!IMPORTANT]
> 一般而言，當您在 Azure 入口網站中建立認知服務資源時，您可以選擇建立多服務的訂用帳戶金鑰 (可跨多個認知服務使用) 或單一服務的訂用帳戶金鑰 (僅用於特定認知服務)。 不過，由於表單辨識器是預覽版本，而並未納入多服務的訂用帳戶中，因此您無法建立單一服務的訂用帳戶，除非您使用「歡迎使用」電子郵件中提供的連結。

當您的表單辨識器資源完成部署後，您可以從入口網站中的 [所有資源]  清單尋找並選取該項資源。 然後，選取 [金鑰]  索引標籤即可檢視您的訂用帳戶金鑰。 這兩種金鑰都可讓您的應用程式存取資源。 複製**金鑰 1** 的值。 您在下一節將會用到此位址。