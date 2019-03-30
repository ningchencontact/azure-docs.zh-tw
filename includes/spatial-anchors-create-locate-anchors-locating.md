---
ms.openlocfilehash: 52dfbfca5f79a7f92848ea39eddc00aa10f05ff1
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632025"
---
## <a name="locate-a-cloud-spatial-anchor"></a>找出雲端空間錨點

找不到先前上傳的雲端空間錨點是使用 Azure 空間的錨點程式庫的主要原因之一。 若要尋找雲端空間錨點，您必須知道其識別項。 錨點識別碼可儲存在您的應用程式後端服務中，可以正確地向它的所有裝置存取。 如需範例的這個，請參閱[教學課程：跨裝置共用空間的錨點](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/)。

具現化`AnchorLocateCriteria`物件，設定您要尋找的並叫用的識別項`CreateWatcher`方法所提供的工作階段上您`AnchorLocateCriteria`。
