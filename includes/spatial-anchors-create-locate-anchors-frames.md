---
ms.openlocfilehash: 10eb16dc1757255c77ed8967dc132f42660e070f
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/23/2019
ms.locfileid: "66110605"
---
## <a name="provide-frames-to-the-session"></a>提供工作階段的畫面格

空間的錨點的工作階段的運作方式是將對應的使用者周圍的空間。 這樣做有助於判斷錨點的所在位置。 行動平台 （iOS 和 Android） 需要相機摘要，從您的平台 AR 程式庫取得框架的原生呼叫。 相反地，HoloLens 正在持續掃描環境中，這樣就不需要像使用行動裝置特定的呼叫。