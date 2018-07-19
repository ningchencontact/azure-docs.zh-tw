---
title: Azure 檔案同步內部部署防火牆和 Proxy 設定 | Microsoft Docs
description: Azure 檔案同步內部部署網路組態
services: storage
documentationcenter: ''
author: fauhse
manager: aungoo
editor: tamram
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: fauhse
ms.openlocfilehash: 7d86082abb6412072af44a6b2d794bcf536fa18d
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2018
ms.locfileid: "37342721"
---
# <a name="azure-file-sync-proxy-and-firewall-settings"></a>Azure 檔案同步 Proxy 和防火牆設定
Azure 檔案同步會將您的內部部署伺服器連線到 Azure 檔案服務，以啟用多網站同步處理和雲端層功能。 因此，內部部署伺服器必須連線到網際網路。 IT 系統管理員必須決定進入 Azure 雲端服務之伺服器的最佳路徑。

這篇文章會提供特定需求的深入解析，以及可以將伺服器成功且安全地連線到 Azure 檔案同步的選項。

> [!Important]
> Azure 檔案同步尚未針對儲存體帳戶支援防火牆與虛擬網路。 

## <a name="overview"></a>概觀
Azure 檔案同步會作為 Windows Server、Azure 檔案共用和其他數個 Azure 服務之間的協調流程服務，以便如您的同步群組中所述同步處理資料。 為了讓 Azure 檔案同步正常運作，您必須將伺服器設定為與下列 Azure 服務通訊：

- Azure 儲存體
- Azure 檔案同步
- Azure Resource Manager
- 驗證服務

> [!Note]  
> Windows Server 上的 Azure 檔案同步代理程式會啟動對雲端服務的所有要求，因而只需要從防火牆的觀點考慮輸出流量。 <br /> 沒有任何 Azure 服務會啟動與 Azure 檔案同步代理程式的連線。


## <a name="ports"></a>連接埠
Azure 檔案同步會透過 HTTPS 以獨佔方式移動檔案資料和中繼資料，而且要求連接埠 443 開放連出。
如此一來，所有流量都會加密。

## <a name="networks-and-special-connections-to-azure"></a>網路以及 Azure 的特殊連線
Azure 檔案同步代理程式沒有與 Azure 特殊通道 (例如 [ExpressRoute](../../expressroute/expressroute-introduction.md) 等) 相關的需求。

Azure 檔案同步會運用任何可用的方法來允許連線到 Azure，自動調整各種網路特性 (例如頻寬、延遲) ，以及提供系統管理控制權以進行微調。 目前不會提供所有功能。 如果您想要設定特定行為，請透過 [Azure 檔案服務 UserVoice](https://feedback.azure.com/forums/217298-storage?category_id=180670) 讓我們知道。

## <a name="proxy"></a>Proxy
Azure 檔案同步支援應用程式特定和整部電腦的 Proxy 設定。

整部電腦的 Proxy 設定對於 Azure 檔案同步代理程式而言是透明的，因為伺服器的整個流量都會透過 Proxy 路由傳送。

應用程式特定的 Proxy 設定可特別針對 Azure 檔案同步流量允許 Proxy 的組態。 代理程式 3.0.12.0 版或更新版本可支援應用程式特定的 Proxy 設定，且可以在代理程式安裝期間進行設定，或藉由使用 Set-StorageSyncProxyConfiguration PowerShell Cmdlet。

以下 PowerShell 命令可用來設定應用程式特定的 Proxy 設定：
```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Set-StorageSyncProxyConfiguration -Address <url> -Port <port number> -ProxyCredential <credentials>
```

## <a name="firewall"></a>防火牆
如前一節所述，連接埠 443 必須開放連出。 根據您資料中心、分公司或區域的原則，可能會想要或需要進一步限制透過此連接埠送至特定網域的流量。

下表描述通訊所需的網域：

| 服務 | 網域 | 使用量 |
|---------|----------------|------------------------------|
| **Azure Resource Manager** | https://management.azure.com | 任何使用者呼叫 (例如 PowerShell) 都會通過這個 URL，包括初始伺服器註冊呼叫。 |
| **Azure Active Directory** | https://login.windows.net | Azure Resource Manager 呼叫必須由已驗證的使用者進行。 為了獲致成功，此 URL 用於進行使用者驗證。 |
| **Azure Active Directory** | https://graph.windows.net/ | 在部署 Azure 檔案同步時，將會在訂用帳戶的 Azure Active Directory 中建立服務主體。 針對該目的使用此 URL。 此主體用於將最小權限集合委派給 Azure 檔案同步服務。 執行 Azure 檔案同步之初始設定的使用者，必須是具有訂用帳戶擁有者權限的已驗證使用者。 |
| **Azure 儲存體** | &ast;.core.windows.net | 當伺服器下載檔案時，如果伺服器可以直接與儲存體帳戶中的 Azure 檔案共用通話，就可以更有效率地執行資料移動。 伺服器具有只允許存取目標檔案共用的 SAS 金鑰。 |
| **Azure 檔案同步** | &ast;.one.microsoft.com | 初始伺服器註冊之後，伺服器會收到該區域中 Azure 檔案同步服務執行個體的區域 URL。 伺服器可以使用 URL 直接且有效率地與處理其同步的執行個體進行通訊。 |

> [!Important]
> 當允許前往 &ast;.one.microsoft.com 的流量時，伺服器可能會有前往同步服務以外的流量。 子網域底下有許多 Microsoft 服務可用。

如果 &ast;.one.microsoft.com 太廣泛，您可只允許 Azure 檔案同步服務的明確區域執行個體，藉此限制伺服器的通訊。 要選擇哪個執行個體，取決於您部署及註冊伺服器之儲存體同步服務的區域。 這是您需要針對伺服器允許的區域。 很快就會有更多的 URL，以便啟用新的商務持續性功能。 

| 區域 | Azure 檔案同步區域端點 URL |
|--------|---------------------------------------|
| 澳洲東部 | https://kailani-aue.one.microsoft.com |
| 加拿大中部 | https://kailani-cac.one.microsoft.com |
| 美國東部 | https://kailani1.one.microsoft.com |
| 東南亞 | https://kailani10.one.microsoft.com |
| 英國南部 | https://kailani-uks.one.microsoft.com |
| 西歐 | https://kailani6.one.microsoft.com |
| 美國西部 | https://kailani.one.microsoft.com |

> [!Important]
> 如果您定義這些詳細的防火牆規則，請經常檢查這份文件並更新您的防火牆規則，以避免由於您防火牆設定中的 URL 清單過時或不完整所造成的服務中斷。

## <a name="summary-and-risk-limitation"></a>摘要和風險限制
本文件中稍早的清單包含 Azure 檔案同步目前與其通訊的 URL。 防火牆必須能夠允許輸出至這些網域的流量，以及來自這些網域的回應。 Microsoft 致力於讓這份清單保持最新狀態。

設定網域限制防火牆規則可以提高安全性。 如果使用這些防火牆組態，則必須記住 URL 會增加並隨著時間而改變。 因此，在將一個 Azure 檔案同步代理程式版本變更為最新代理程式測試部署上另一個版本的變更管理程序中，檢查本文件中的表格是很謹慎的做法。 如此一來，您可確保您的防火牆已設定為允許前往最新代理程式所需之網域的流量。

## <a name="next-steps"></a>後續步驟
- [規劃 Azure 檔案同步部署](storage-sync-files-planning.md)
- [部署 Azure 檔案同步 (預覽)](storage-sync-files-deployment-guide.md)
