---
ms.openlocfilehash: eafe71902ee849c310847f4537214722e129ad42
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "57907613"
---
## <a name="update-properties"></a>更新屬性

若要更新的錨點的屬性，您可以使用 UpdateAnchorProperties 方法。 如果兩個或多個裝置會嘗試同時更新相同的錨點屬性，我們會使用開放式並行存取模型。 這表示第一次寫入會獲得優先權。  所有其他寫入將會取得 「 並行 」 錯誤： 屬性的重新整理就需要再試。
