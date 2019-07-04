---
title: 包含檔案
description: 包含檔案
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 09/12/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 66e21aefa5648262ca2fcc61501905f725ac0e4b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67173883"
---
在開始設定之前，請確認您已符合下列條件：

* 如果您已經有想要與之連線的虛擬網路，請確認沒有任何內部部署網路的子網路與想要連線的虛擬網路重疊。 您的虛擬網路不需要閘道子網路，而且不能有任何虛擬網路閘道。 如果您還沒有虛擬網路，可以使用本文中的步驟建立一個。
* 取得中樞區域的 IP 位址範圍。 中樞是虛擬網路，而您為中樞區域指定的位址範圍不能與任何連線的現有虛擬網路重疊。 也不能與連線至內部部署的位址範圍重疊。 如果您不熟悉位於內部部署網路組態的 IP 位址範圍，您需要與能夠提供那些詳細資料的人協調。
* 如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。