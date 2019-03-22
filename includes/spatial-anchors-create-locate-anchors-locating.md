---
ms.openlocfilehash: 430302d8b1b2a01febbbe2f11057bb331ec80c28
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "57907666"
---
## <a name="locate-a-cloud-spatial-anchor"></a>找出雲端空間錨點

若要尋找雲端空間錨點，您必須知道其識別項。 錨點識別碼可能會儲存在您的應用程式後端服務，而且可以適當地向它的所有裝置存取。 如需範例的這個，請參閱[教學課程：跨裝置共用空間的錨點](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/)。

具現化 AnchorLocateCriteria 物件，設定您要尋找的並在工作階段上叫用 CreateWatcher 方法，藉由提供您 AnchorLocateCriteria 的識別碼。
