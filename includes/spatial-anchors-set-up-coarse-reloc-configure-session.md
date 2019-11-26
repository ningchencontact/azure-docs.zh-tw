---
author: bucurb
ms.author: bobuc
ms.date: 09/18/2019
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: 7ac98f608cb1ca5bab590714623bc78036d46b81
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2019
ms.locfileid: "74092994"
---
## <a name="configure-the-cloud-anchor-session"></a>設定雲端錨點工作階段

接下來，我們將會負責設定雲端錨點工作階段。 在第一行中，我們會在工作階段上設定感應器提供者。 從現在開始，我們在工作階段期間建立的任何錨點都會與一組感應器讀數相關聯。 接下來，我們會將接近裝置的尋找準則具現化，並將其初始化以符合應用程式需求。 最後，我們會藉由從接近裝置的準則建立監看員，指示工作階段在尋找錨點時使用感應器資料。