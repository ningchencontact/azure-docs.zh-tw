---
ms.openlocfilehash: 6d6886996d926ce778600dfa1d69caaa1c7ac004
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60232601"
---
## <a name="pause-reset-or-stop-the-session"></a>暫停、 重設，或停止工作階段

若要停止工作階段，您可以叫用`Stop()`。 這樣會停止任何監看員和環境的處理，即使您叫用 ProcessFrame()。 您可以再叫用`Start()`繼續處理。 繼續時，會維護已擷取的工作階段中的環境資料。
