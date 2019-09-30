---
title: Windows 虛擬桌面疑難排解總覽、意見反應和支援-Azure
description: 在設定 Windows 虛擬桌面租使用者環境時，針對問題進行疑難排解的總覽。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/08/2019
ms.author: helohr
ms.openlocfilehash: b16d993717529953da1dc31604e6112f53ed7ac9
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679444"
---
# <a name="troubleshooting-overview-feedback-and-support"></a>疑難排解概觀、意見反應和支援

本文概述您在設定 Windows 虛擬桌面租使用者環境時可能會遇到的問題，並提供解決問題的方法。

## <a name="provide-feedback"></a>提供意見反應

請瀏覽 [Windows 虛擬桌面技術社群](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)，與產品小組和活躍的社群成員一起討論 Windows 虛擬桌面服務。

## <a name="escalation-tracks"></a>呈報追蹤

使用下表來找出並解決使用遠端桌面用戶端設定租使用者環境時可能會遇到的問題。 設定租使用者之後，您就可以使用新的[診斷服務](https://docs.microsoft.com/azure/virtual-desktop/diagnostics-role-service)來識別常見案例的問題。

>[!NOTE]
> 我們有一個技術社區論壇，可供您造訪以與產品小組和主動式社區成員討論您的問題。 前往[Windows 虛擬桌面技術社區](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) 

| **問題**                                                            | **建議的解決方案**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| 建立租使用者                                                    | 如果發生 Azure 中斷，請聯絡[Azure 支援](https://azure.microsoft.com/support/options/);否則，請**開啟 Windows 虛擬桌面的支援要求（計算）** 。|
| 存取 Azure 入口網站中的 Marketplace 範本       | 如果發生 Azure 中斷，請聯絡[Azure 支援](https://azure.microsoft.com/support/options/)。 <br> <br> Azure Marketplace Windows 虛擬桌面範本可免費使用。|
| 從 GitHub 存取 Azure Resource Manager 範本                                  | 請參閱[租使用者和主機集區建立](troubleshoot-set-up-issues.md)的「建立 Windows 虛擬桌面工作階段主機 vm」一節。 如果問題仍然無法解決，請洽詢[GitHub 支援小組](https://github.com/contact)。 <br> <br> 如果錯誤是在存取 GitHub 中的範本之後發生，請聯絡[Azure 支援](https://azure.microsoft.com/support/options/)。|
| 工作階段主機集區 Azure 虛擬網路（VNET）和 Express Route 設定               | 聯絡**Azure 支援（網路）** 。 |
| 工作階段主機集區虛擬機器（VM）會在未使用 Windows 虛擬桌面提供的 Azure Resource Manager 範本時建立 | 聯絡**Azure 支援（計算）** 。 <br> <br> 如需 Windows 虛擬桌面隨附之 Azure Resource Manager 範本的問題，請參閱建立租使用者的 Windows 虛擬桌面租使用者一節[和建立主機集](troubleshoot-set-up-issues.md)區。 |
| 從 Azure 入口網站管理 Windows 虛擬桌面工作階段主機環境    | 請聯絡**Azure 支援**。 <br> <br> 如需使用遠端桌面服務/Windows 虛擬桌面 PowerShell 時的管理問題，請參閱[Windows 虛擬桌面 PowerShell](troubleshoot-powershell.md)或**開啟 Windows 虛擬桌面的支援要求（計算）** 。 |
| 管理系結至主機集區和應用程式群組的 Windows 虛擬桌面設定（應用程式群組）      | 請參閱[Windows 虛擬桌面 PowerShell](troubleshoot-powershell.md)，或**開啟 Windows 虛擬桌面（計算）的支援要求**。 <br> <br> 如果問題系結至範例圖形化使用者介面（GUI），請與 Yammer 社區聯繫。|
| 遠端桌面用戶端在啟動時無法正常運作                                                 | 請參閱[遠端桌面用戶端](troubleshoot-client-connection.md)連線，如果無法解決問題，請**開啟 Windows 虛擬桌面的支援要求（計算）** 。  <br> <br> 如果是網路問題，您的使用者必須聯絡其網路系統管理員。 |
| 已連線但沒有摘要                                                                 | 使用[遠端桌面用戶端](troubleshoot-client-connection.md)連線的「使用者連接但不顯示任何內容（沒有摘要）」區段進行疑難排解。 <br> <br> 如果您的使用者已指派給應用程式群組，請**開啟 Windows 虛擬桌面的支援要求（計算）** 。 |
| 因網路而造成的摘要探索問題                                            | 您的使用者必須聯絡其網路系統管理員。 |
| 連接用戶端                                                                    | 請參閱[遠端桌面用戶端](troubleshoot-client-connection.md)連線，如果無法解決您的問題，請參閱[工作階段主機虛擬機器](troubleshoot-vm-configuration.md)設定。 |
| 遠端應用程式或桌上型電腦的回應能力                                      | 如果問題系結至特定的應用程式或產品，請洽詢負責該產品的小組。 |
| 授權訊息或錯誤                                                          | 如果問題系結至特定的應用程式或產品，請洽詢負責該產品的小組。 |

## <a name="next-steps"></a>後續步驟

- 若要針對在 Windows 虛擬桌面環境中建立租使用者和主機集區的問題進行疑難排解, 請參閱[建立租使用者和主機集](troubleshoot-set-up-issues.md)區。
- 若要在 Windows 虛擬桌面中設定虛擬機器 (VM) 時針對問題進行疑難排解, 請參閱[工作階段主機虛擬機器](troubleshoot-vm-configuration.md)設定。
- 若要疑難排解 Windows 虛擬桌面用戶端連線的問題, 請參閱[遠端桌面用戶端連接](troubleshoot-client-connection.md)。
- 若要針對搭配 Windows 虛擬桌面使用 PowerShell 時的問題進行疑難排解, 請參閱[Windows 虛擬桌面 PowerShell](troubleshoot-powershell.md)。
- 若要深入瞭解此服務，請參閱[Windows 虛擬桌面環境](https://docs.microsoft.com/azure/virtual-desktop/environment-setup)。
- 若要進行疑難排解教學課程，請參閱[教學課程：針對 Resource Manager 範本部署](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot)進行疑難排解。
- 若要了解稽核動作，請參閱 [使用 Resource Manager 來稽核作業](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)。
- 若要了解部署期間可採取哪些動作來判斷錯誤，請參閱 [檢視部署作業](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations)。
