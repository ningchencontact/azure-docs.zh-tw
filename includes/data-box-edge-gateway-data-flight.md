---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: acf1195616d45b155445604ef0204528e5f7ca03
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60754253"
---
對於傳輸中的資料：

- 標準的 TLS 1.2 用於裝置與 Azure 之間傳輸資料。 沒有任何後援至 TLS 1.1 或更早版本。 如果不支援 TLS 1.2，將會封鎖代理程式通訊。 TLS 1.2 時也需要針對入口網站與 SDK 管理。
- 當用戶端透過本機 web UI 的瀏覽器存取您的裝置時，會將標準的 TLS 1.2 作為預設的安全通訊協定。

    - 最佳做法是設定您的瀏覽器使用 TLS 1.2。
    - 如果瀏覽器不支援 TLS 1.2，您可以使用 TLS 1.1 或 TLS 1.0。
- 我們建議您使用 SMB 3.0 加密來保護資料，當您從您資料的伺服器進行複製時。
