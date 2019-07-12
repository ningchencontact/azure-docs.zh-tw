---
title: Windows 虛擬桌面疑難排解概觀、 意見反應和支援-Azure
description: 設定 Windows 虛擬桌面的租用戶環境時疑難排解問題的概觀。
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/08/2019
ms.author: v-chjenk
ms.openlocfilehash: b349d7f384c801e95d745f7a18535f9f5d13cf86
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605217"
---
# <a name="troubleshooting-overview-feedback-and-support"></a>疑難排解概觀、意見反應和支援

本文中的問題，您設定的 Windows 虛擬桌面的租用戶環境時，可能會遇到，並提供如何解決問題的概觀。

## <a name="provide-feedback"></a>提供意見反應

我們目前不採用 Windows 虛擬桌面處於預覽狀態時的支援案例。 請瀏覽 [Windows 虛擬桌面技術社群](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)，與產品小組和活躍的社群成員一起討論 Windows 虛擬桌面服務。

## <a name="escalation-tracks"></a>擴大追蹤

使用下表來識別及解決您在設定使用遠端桌面用戶端的租用戶環境時可能會遇到的問題。 一旦您的租用戶的設定時，您可以使用新[診斷服務](https://docs.microsoft.com/azure/virtual-desktop/diagnostics-role-service)找出問題的常見案例。

>[!NOTE]
>我們目前不採用 Windows 虛擬桌面處於預覽狀態時的支援案例。 每當我們參照 Windows 虛擬桌面的支援，請移至我們的技術社群論壇現在。 請瀏覽[Windows 虛擬桌面技術社群](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)討論與產品小組和活躍的社群成員的問題。 如果您需要解決支援問題，包括活動識別碼和約略時間範圍，此問題發生時。

| **問題**                                                            | **建議的解決方案**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| 建立租用戶                                                    | 如果沒有 Azure 服務中斷，請連絡[Azure 支援](https://azure.microsoft.com/support/options/); 否則請連絡**遠端桌面服務/Windows 虛擬桌面支援**。|
| 存取 Azure 入口網站中的 Marketplace 範本       | 如果沒有 Azure 服務中斷，請連絡[Azure 支援](https://azure.microsoft.com/support/options/)。 <br> <br> Azure Marketplace 的 Windows 虛擬桌面範本會免費提供。|
| 從 GitHub 中存取 Azure Resource Manager 範本                                  | 請參閱 「 建立 Windows 虛擬桌面工作階段主機 Vm 」 一節[租用戶，然後主應用程式集區建立](troubleshoot-set-up-issues.md)。 如果問題仍無法解決，請連絡[GitHub 支援小組](https://github.com/contact)。 <br> <br> 如果在存取 GitHub 中的範本之後，就會發生錯誤，請連絡[Azure 支援](https://azure.microsoft.com/support/options/)。|
| 工作階段主應用程式集區的 Azure 虛擬網路 (VNET) 和 Expressroute 設定               | 請連絡**Azure 支援 （網路）** 。 |
| 工作階段主應用程式集區的虛擬機器 (VM) 建立時不會使用與 Windows 虛擬桌面提供的 Azure Resource Manager 範本 | 請連絡**Azure 支援服務 （計算）** 。 <br> <br> 如所提供的 Windows 虛擬桌面，Azure Resource Manager 範本的問題，請參閱 「 建立 Windows 虛擬桌面的租用戶一節[租用戶，然後主應用程式集區建立](troubleshoot-set-up-issues.md)。 |
| 從 Azure 入口網站中管理 Windows 虛擬桌面工作階段主機環境    | 請連絡**Azure 支援**。 <br> <br> 使用遠端桌面服務/Windows 虛擬桌面 PowerShell 時，就會管理問題，請參閱[Windows 虛擬桌面的 PowerShell](troubleshoot-powershell.md) ，或連絡**遠端桌面服務/Windows 虛擬桌面支援小組**. |
| 管理 Windows 虛擬桌面設定繫結至主應用程式集區和應用程式群組 （應用程式群組）      | 請參閱[Windows 虛擬桌面的 PowerShell](troubleshoot-powershell.md)，或連絡**遠端桌面服務/Windows 虛擬桌面支援小組**。 <br> <br> 如果問題會繫結至範例圖形化使用者介面 (GUI) 中，連至 Yammer 的社群。|
| [開始] 上的遠端桌面用戶端發生問題                                                 | 請參閱[遠端桌面用戶端連線](troubleshoot-client-connection.md)，如果這無法解決此問題，請連絡**遠端桌面服務/Windows 虛擬桌面支援小組**。  <br> <br> 如果是網路問題，您的使用者，就需要連絡他們的網路系統管理員。 |
| 已連接但任何摘要                                                                 | 疑難排解使用 「 使用者連線，但不是會顯示 （任何摘要） 」 一節[遠端桌面用戶端連線](troubleshoot-client-connection.md)。 <br> <br> 如果您的使用者已指派給 「 應用程式群組，提升為**遠端桌面服務/Windows 虛擬桌面支援小組**。 |
| 摘要因網路的探索問題                                            | 您的使用者，就需要連絡他們的網路系統管理員。 |
| 用戶端連線                                                                    | 請參閱[遠端桌面用戶端連線](troubleshoot-client-connection.md)如果這麼做無法解決您的問題，請參閱[工作階段主機的虛擬機器設定](troubleshoot-vm-configuration.md)。 |
| 遠端應用程式或桌面的回應性                                      | 如果問題會繫結至特定的應用程式或產品，請連絡負責該產品的小組。 |
| 授權訊息或錯誤                                                          | 如果問題會繫結至特定的應用程式或產品，請連絡負責該產品的小組。 |

## <a name="next-steps"></a>後續步驟

- 若要針對問題進行疑難排解在 Windows 虛擬桌面環境中建立的租用戶和主應用程式集區時，請參閱[租用戶，然後主應用程式集區建立](troubleshoot-set-up-issues.md)。
- 若要設定虛擬機器 (VM) 中 Windows 虛擬桌面時疑難排解問題，請參閱[工作階段主機的虛擬機器設定](troubleshoot-vm-configuration.md)。
- 若要疑難排解使用 Windows 的虛擬桌面用戶端連線的問題，請參閱[遠端桌面用戶端連線](troubleshoot-client-connection.md)。
- 若要使用 PowerShell 與 Windows 虛擬桌面時，請疑難排解問題，請參閱[Windows 虛擬桌面 PowerShell](troubleshoot-powershell.md)。
- 若要深入了解預覽服務，請參閱[Windows Desktop 預覽環境](https://docs.microsoft.com/azure/virtual-desktop/environment-setup)。
- 若要進行疑難排解教學課程，請參閱[教學課程：針對 Resource Manager 範本部署進行疑難排解](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot)。
- 若要了解稽核動作，請參閱 [使用 Resource Manager 來稽核作業](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)。
- 若要了解部署期間可採取哪些動作來判斷錯誤，請參閱 [檢視部署作業](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations)。