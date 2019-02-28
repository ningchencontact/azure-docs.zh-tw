---
title: 使用 Azure Stack 中的系統管理入口網站 | Microsoft Docs
description: 身為 Azure Stack 操作員，您應了解如何使用系統管理入口網站。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 02/25/2019
ms.author: jeffgilb
ms.reviewer: efemmano
ms.lastreviewed: 02/25/2019
ms.openlocfilehash: 8662cfde881a90ce8f7fc6cceaa576d3b971d9d6
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2019
ms.locfileid: "56817927"
---
# <a name="quickstart-use-the-azure-stack-administration-portal"></a>快速入門：使用 Azure Stack 系統管理入口網站

*適用於：Azure Stack 整合式系統和 Azure Stack 開發套件*

Azure Stack 中有兩種入口網站：系統管理入口網站和使用者入口網站 (有時也稱作「租用戶」入口網站)。身為 Azure Stack 操作員，您可以使用系統管理入口網站進行 Azure Stack 的日常管理和作業。

## <a name="access-the-administrator-portal"></a>存取系統管理員入口網站

若要存取系統管理員入口網站，請瀏覽至入口網站 URL，然後使用 Azure Stack 操作員的認證進行登入。 如果是整合系統，則入口網站 URL 會依 Azure Stack 部署的區域名稱和外部完整網域名稱 (FQDN) 之不同而有所不同。 Azure Stack 開發套件 (ASDK) 部署的系統管理入口網站 URL 一律是相同的。 

| 環境 | 系統管理員入口網站 URL |   
| -- | -- | 
| ASDK| https://adminportal.local.azurestack.external  |
| 整合系統 | https://adminportal.&lt;*區域*&gt;.&lt;*FQDN*&gt; | 
| | |

> [!TIP]
> 對於 ASDK 環境，您必須先確定可以透過「遠端桌面連線」或透過虛擬私人網路 (VPN) [連線到開發套件主機](azure-stack-connect-azure-stack.md)。

 ![系統管理入口網站](media/azure-stack-manage-portals/admin-portal.png)

所有 Azure Stack 部署的預設時區都會設定為國際標準時間 (UTC)。 

在系統管理員入口網站中，您可以執行如下的作業：

* [向 Azure 註冊 Azure Stack](azure-stack-registration.md)
* [填入 Marketplace](azure-stack-download-azure-marketplace-item.md)
* [為使用者建立方案、供應項目和訂用帳戶](azure-stack-plan-offer-quota-overview.md)
* [監視健全狀況和警示](azure-stack-monitor-health.md)
* [管理 Azure Stack 更新](azure-stack-updates.md)

[快速入門教學課程] 圖格會提供最常見工作的線上文件連結。

雖然操作員能夠在系統管理入口網站中建立虛擬機器、虛擬網路及儲存體帳戶等資源，但您應[登入使用者入口網站](user/azure-stack-use-portal.md)來建立和測試資源。

>[!NOTE]
>快速入門教學課程圖格中的 [建立虛擬機器] 連結會讓您在系統管理入口網站中建立虛擬機器，但此程序的目的只是要驗證 Azure Stack 已成功部署。

## <a name="understand-subscription-behavior"></a>了解訂用帳戶行為

在系統管理入口網站中依預設會建立三個訂用帳戶：取用、預設提供者和計量。 身為操作員，您大多會使用「預設提供者訂用帳戶」。 您無法新增任何其他的訂用帳戶並在系統管理入口網站中加以使用。 

使用者可根據您為其建立的方案和供應項目在使用者入口網站中建立其他訂用帳戶。 不過，在使用者入口網站中無法存取系統管理入口網站的任何管理或操作功能。

系統管理和使用者入口網站是由 Azure Resource Manager 的個別執行個體所支援的。 由於 Azure Resource Manager 有此區分，訂用帳戶無法跨入口網站使用。 例如，如果您以 Azure Stack 操作員的身分登入使用者入口網站，便無法存取「預設提供者訂用帳戶」。 雖然您無法存取任何系統管理功能，但可以從可用的公用供應項目為自己建立訂用帳戶。 只要您登入使用者入口網站，系統就會將您視為租用戶使用者。

  >[!NOTE]
  >在 ASDK 環境中，如果使用者與 Azure Stack 操作員屬於相同的租用戶目錄，就不會封鎖他們登入系統管理入口網站。 不過，這些使用者無法存取任何管理功能，或是新增訂用帳戶以存取使用者入口網站中可供他們使用的供應項目。

## <a name="administration-portal-tips"></a>管理入口網站的秘訣

### <a name="customize-the-dashboard"></a>自訂儀表板

儀表板包含一組預設圖格。 您可以選取 [編輯儀表板] 來修改預設儀表板，或是選取 [新增儀表板] 來新增自訂儀表板。 您可以輕鬆地將圖格新增到儀表板中。 例如，您可以選取 [+ 建立資源]，在 [供應項目 + 方案] 上按一下滑鼠右鍵，然後選取 [釘選到儀表板]。

有時，您可能會在入口網站中看到空白的儀表板。 若要復原儀表板，請按一下 [編輯儀表板]，然後按一下滑鼠右鍵並選取 [重設為預設狀態]。

### <a name="quick-access-to-online-documentation"></a>快速存取線上文件

若要存取 Azure Stack 操作員文件，請使用系統管理員入口網站右上角的 [說明及支援] 圖示 (問號)。 將游標移到該圖示，然後選取 [說明 + 支援]。

### <a name="quick-access-to-help-and-support"></a>快速存取說明及支援

如果您選取系統管理員入口網站右上角的 [說明及支援] 圖示 (問號)，然後選取 [新增支援要求]，則會發生下列其中一種結果：

- 如果您使用的是整合系統，此動作會開啟一個網站，可讓您直接向「Microsoft 客戶支援服務」(CSS) 建立支援票證。 若要了解何時該尋求 Microsoft 支援或原始設備製造商 (OEM) 硬體廠商支援，請參閱[從哪裡取得支援](azure-stack-manage-basics.md#where-to-get-support)。
- 如果您使用 ASDK，則此動作會直接開啟 [Azure Stack 論壇網站](https://social.msdn.microsoft.com/Forums/home?forum=AzureStack)。 我們會定期留意這些論壇。 由於 ASDK 是一個評估環境，因此並未透過 Microsoft CSS 提供官方支援。

### <a name="quick-access-to-the-azure-roadmap"></a>快速存取 Azure 藍圖

如果您選取系統管理入口網站右上角的 [說明及支援] (問號)，然後選取 [Azure 藍圖]，便會有新的瀏覽器索引標籤開啟，並帶您前往 Azure 藍圖。 在 [產品] 搜尋方塊中輸入 **Azure Stack**，您就可以看到所有的 Azure Stack 藍圖更新。

## <a name="next-steps"></a>後續步驟

[向 Azure 註冊 Azure Stack](azure-stack-registration.md)，並在 [Azure Stack Marketplace](azure-stack-marketplace.md) 中填入要為使用者提供的項目。 
