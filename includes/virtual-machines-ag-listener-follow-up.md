---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 8ae22ec7f75b9cd0d7958977dfd97169706c9389
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2018
ms.locfileid: "50226805"
---
建立可用性群組接聽程式之後，可能需要調整接聽程式資源的 RegisterAllProvidersIP 和 HostRecordTTL 叢集參數。 這些參數可縮短容錯移轉之後的重新連線時間，並可能防止連線逾時。 如需這些參數的詳細資訊以及範例程式碼，請參閱[建立或設定可用性群組接聽程式](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover)。

