---
title: 對使用 Azure Site Recovery 從 VMWare VM 災害復原至 Azure 期間所進行的內部部署環境容錯回復進行疑難排解 | Microsoft Docs
description: 本文說明對使用 Azure Site Recovery 從 VMWare VM 災害復原至 Azure 期間所發生的容錯回復和重新保護問題進行疑難排解的方式。
author: vDonGlover
manager: JarrettRenshaw
ms.service: site-recovery
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: v-doglov
ms.openlocfilehash: c598c5e238458c010500579c5371622b85e71de0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60565186"
---
# <a name="troubleshoot-vcenter-discovery-failures"></a>對 vCenter 探索進行疑難排解失敗

這篇文章可協助您疑難排解因為 VMware vCenter 探索失敗而發生的問題。

## <a name="non-numeric-values-in-the-maxsnapshots-property"></a>MaxSnapShots 屬性中的非數字值

之前的版本 9.20，vCenter 中斷連線時它會擷取屬性的非數字值`snapshot.maxSnapShots`VM 上的屬性。

錯誤 ID 95126 識別此問題。

    ERROR :: Hit an exception while fetching the required informationfrom vCenter/vSphere.Exception details:
    System.FormatException: Input string was not in a correct format.
       at System.Number.StringToNumber(String str, NumberStyles options, NumberBuffer& number, NumberFormatInfo info, Boolean parseDecimal)
       at System.Number.ParseInt32(String s, NumberStyles style, NumberFormatInfo info)
       at VMware.VSphere.Management.InfraContracts.VirtualMachineInfo.get_MaxSnapshots()
    
若要解決此問題：

- 識別 VM，並將值設定為數值 （編輯 VM 在 vCenter 中的設定）。

或

- 9.20 或更新版本的版本升級設定伺服器。

## <a name="proxy-configuration-issues-for-vcenter-connectivity"></a>VCenter 連線的 proxy 設定問題

vCenter 探索會接受系統使用者所設定的系統預設 proxy 設定。 DRA 服務會接受使用統一的安裝程式或 OVA 範本的組態伺服器安裝期間由使用者提供的 proxy 設定。 

一般情況下，使用與公用網路; 通訊的 proxy例如與 Azure 通訊。 如果 proxy 設定，vCenter 會在本機環境中，將無法與 DRA 通訊。

此問題發生時，就會發生下列情況：

- VCenter server \<vCenter > 找不到因為發生錯誤：遠端伺服器傳回錯誤：伺服器無法使用 (503)
- VCenter server \<vCenter > 找不到因為發生錯誤：遠端伺服器傳回錯誤：無法連線到遠端伺服器。
- 無法連線至 vCenter/ESXi 伺服器。

若要解決此問題：

下載[PsExec 工具](https://aka.ms/PsExec)。 

您可以使用 PsExec 工具來存取系統的使用者內容，並判斷是否已設定的 proxy 位址。 然後，您就可以略過清單中，使用下列程序來加入 vCenter。

探索 proxy 設定：

1. 在使用 PsExec 工具的系統使用者內容中的開啟 IE。
    
    psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"

2. 修改中 Internet Explorer 略過的 vCenter IP 位址的 proxy 設定。
3. 重新啟動 tmanssvc 服務。

DRA 的 proxy 設定：

1. 開啟命令提示字元，並開啟 [Microsoft Azure Site Recovery Provider] 資料夾。
 
    **cd C:\Program Files\Microsoft Azure Site Recovery 提供者**

3. 從命令提示字元中，執行下列命令。
   
   **DRCONFIGURATOR。EXE /AddBypassUrls /configure [IP 位址 /FQDN 的 vCenter Server 所提供的時間新增 vCenter]**

4. 重新啟動的 DRA 提供者服務。

## <a name="next-steps"></a>後續步驟

[管理組態伺服器 VMware VM 災害復原](https://docs.microsoft.com/azure/site-recovery/vmware-azure-manage-configuration-server#refresh-configuration-server) 
