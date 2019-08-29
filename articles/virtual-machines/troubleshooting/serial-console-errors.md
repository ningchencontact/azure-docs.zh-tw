---
title: Azure 序列主控台錯誤 |Microsoft Docs
description: Azure 序列主控台中的常見錯誤
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 8/20/2019
ms.author: alsin
ms.openlocfilehash: a997675a2f7a280e8311e2bba7dca21de82a086e
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129662"
---
# <a name="common-errors-within-the-azure-serial-console"></a>Azure 序列主控台中的常見錯誤
Azure 序列主控台內有一組已知錯誤。 這是這些錯誤和風險降低步驟的清單。

## <a name="common-errors"></a>常見錯誤

Error                            |   風險降低
:---------------------------------|:--------------------------------------------|
無法擷取 *&lt;VMNAME&gt;* 的開機診斷設定。 若要使用序列主控台，請確定已針對此 VM 啟用診斷開機診斷。 ![開機診斷錯誤](./media/virtual-machines-serial-console/virtual-machines-serial-console-boot-diagnostics-error.png) | 請確認 VM 或虛擬機器擴展集已啟用[開機診斷](boot-diagnostics.md)。 如果您在虛擬機器擴展集實例上使用序列主控台, 請確定您的實例具有最新的模型。
VM 處於已停止 (已解除配置) 狀態。 啟動 VM 並重試序列主控台連線。 ![已解除配置的錯誤](./media/virtual-machines-serial-console/virtual-machines-serial-console-deallocating-error.png) | VM 或虛擬機器擴展集實例必須處於已啟動狀態, 才能存取序列主控台。 啟動您的 VM 或虛擬機器擴展集實例, 然後再試一次。
存取此 VM 的開機診斷儲存體帳戶時，遇到「禁止」回應。 ![儲存體帳戶防火牆錯誤](./media/virtual-machines-serial-console/virtual-machines-serial-console-firewall-error.png)| 請確定開機診斷沒有帳戶防火牆。 必須要有可存取的開機診斷儲存體帳戶，序列主控台才能運作。
您沒有可使用此 VM 與序列主控台的必要權限。 請確定您至少具有「虛擬機器參與者」角色權限。| 若要存取序列主控台, 您必須在 VM 或虛擬機器擴展集上具有參與者層級的存取權或更新版本。 如需詳細資訊, 請參閱[總覽頁面](serial-console-overview.md)。
找不到此 VM 上用於開機診斷的儲存體帳戶 ' '。 請確認此 VM 已啟用開機診斷, 此儲存體帳戶尚未刪除, 而且您可以存取此儲存體帳戶。 | 再次確認您未刪除 VM 或虛擬機器擴展集的開機診斷儲存體帳戶
此 VM 的布建尚未成功。 請確定已完全部署 VM, 然後重試序列主控台連線。 | 您的 VM 或虛擬機器擴展集可能仍在布建中。 請稍候片刻, 然後再試一次。
您沒有必要的許可權, 無法寫入此 VM 的開機診斷儲存體帳戶。 請確定您至少有 ' ' 的 VM 參與者許可權。 | 序列主控台存取需要開機診斷儲存體帳戶的參與者層級存取權。 如需詳細資訊, 請參閱[總覽頁面](serial-console-overview.md)。
無法判斷開機診斷儲存體帳戶 *&lt;STORAGEACCOUNTNAME&gt;* 的資源群組。 請確認已針對此 VM 啟用開機診斷，且您具有此儲存體帳戶的存取權。 | 序列主控台存取需要開機診斷儲存體帳戶的參與者層級存取權。 如需詳細資訊, 請參閱[總覽頁面](serial-console-overview.md)。
Web 通訊端已關閉或無法開啟。 | 您可能需要將防火牆存取新增至`*.console.azure.com`。 更詳細但較長的方法是允許防火牆存取 Microsoft Azure 的[資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653), 這會定期變更。


## <a name="next-steps"></a>後續步驟
* 深入瞭解[適用于 Linux vm 的 Azure 序列主控台](./serial-console-linux.md)
* 深入瞭解[適用于 Windows vm 的 Azure 序列主控台](./serial-console-windows.md)