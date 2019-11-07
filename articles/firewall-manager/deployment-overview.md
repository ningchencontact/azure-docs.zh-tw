---
title: Azure 防火牆管理員預覽部署概觀
description: 了解 Azure 防火牆管理員預覽所需的概略部署步驟
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: df87e652d2969d4ae12e97a2b455648cf39382c3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488254"
---
# <a name="azure-firewall-manager-preview-deployment-overview"></a>Azure 防火牆管理員預覽部署概觀

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

有多種方法可以部署 Azure 防火牆管理員預覽，但建議使用下列一般程序。

## <a name="prerequisites"></a>必要條件

> [!IMPORTANT]
> 您必須使用 `Register-AzProviderFeature` PowerShell 命令，明確地啟用 Azure 防火牆管理員預覽。
>從 PowerShell 命令提示字元執行下列命令：
>
>```azure-powershell
>connect-azaccount
>Register-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network
>```
>需要 30 分鐘才能完成功能註冊。 執行下列命令來檢查您的註冊狀態：
>
>`Get-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network`



## <a name="general-deployment-process"></a>一般部署程序

1. 建立中樞和輪輻架構

   - 使用 Azure 防火牆管理員建立安全虛擬中樞，並新增虛擬網路連線。<br>*or*<br>
   - 建立虛擬 WAN 中樞並新增虛擬網路連線。
2. 選取安全性提供者

   - 在建立安全虛擬中樞時完成。<br>*or*<br>
   - 將現有虛擬 WAN 中樞轉換成安全虛擬中樞。
3. 建立防火牆原則，並將它與您的中樞建立關聯

   - 僅在使用 Azure 防火牆時才適用。
   - 協力廠商安全性即服務 (SECaaS) 原則是透過合作夥伴管理體驗來設定。
4. 設定路由設定以將流量路由傳送至安全中樞

   - 使用 [安全虛擬中樞路由設定] 頁面，輕鬆地將流量路由傳送到安全中樞，以進行篩選和記錄，而不需要使用者定義路由 (UDR) 在輪輻虛擬網路上。

> [!NOTE]
> - 每個區域的每個虛擬 WAN 上不能有超過一個中樞。 但是，您可以在區域中新增多個虛擬 WAN 來達成此目的。
> - vWAN 中的中樞不能有重疊的 IP 空間。
> - 您的中樞 VNet 連線必須與中樞位於相同的區域。

## <a name="next-steps"></a>後續步驟

- [教學課程：使用 Azure 入口網站以 Azure 防火牆管理員預覽來保護您的雲端網路](secure-cloud-network.md)