---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: bea14544949f90290bf3878295b49f1e08be9eea
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2019
ms.locfileid: "59684397"
---
資料層中-班機：

- 會在裝置與 Azure 之間的資料，會使用標準的 TLS 1.2。 沒有任何後援至 TLS 1.1 或更早版本。 不支援 TLS 1.2，則為，將會封鎖代理程式通訊。 TLS 1.2 時也需要針對入口網站與 SDK 管理作業。
- 當用戶端會存取您的裝置，透過本機 web UI，在瀏覽器中時，標準的 TLS 1.2 作為預設的安全通訊協定。

    - 最佳做法是設定您的瀏覽器使用 TLS 1.2。
    - 如果瀏覽器不支援 TLS 1.2，您可以使用 TLS 1.1 或 TLS 1.0。
- 若要保護的資料，當您從您資料的伺服器進行複製時，我們建議，您會使用 SMB 3.0 加密。
