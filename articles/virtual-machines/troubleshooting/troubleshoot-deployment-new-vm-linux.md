---
title: 針對 Linux VM 部署進行疑難排解 | Microsoft Docs
description: 針對在 Azure 中建立新 Linux 虛擬機器的 Resource Manager 部署問題進行疑難排解
services: virtual-machines-linux, azure-resource-manager
documentationcenter: ''
author: JiangChen79
manager: jeconnoc
editor: ''
tags: top-support-issue, azure-resource-manager
ms.assetid: 906a9c89-6866-496b-b4a4-f07fb39f990c
ms.service: virtual-machines-linux
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/09/2016
ms.author: cjiang
ms.openlocfilehash: 9fea914fdf9b025fd5d38219a6bfc81b4a9cc584
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125611"
---
# <a name="troubleshoot-resource-manager-deployment-issues-with-creating-a-new-linux-virtual-machine-in-azure"></a>針對在 Azure 中建立新 Linux 虛擬機器的 Resource Manager 部署問題進行疑難排解
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="top-issues"></a>常見問題
[!INCLUDE [support-disclaimer](../../../includes/virtual-machines-linux-troubleshoot-deploy-vm-top.md)]

如需了解其他 VM 部署問題，請參閱[針對 Azure 中的 Linux 虛擬機器部署問題進行疑難排解](troubleshoot-deploy-vm-linux.md)。

## <a name="collect-activity-logs"></a>收集活動記錄
若要開始進行排解疑難，請收集活動記錄，以識別與問題相關的錯誤。 以下链接包含有关要遵循的过程的详细信息。

[檢視部署作業](../../azure-resource-manager/resource-manager-deployment-operations.md)

[檢視活動記錄以管理 Azure 資源](../../resource-group-audit.md)

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-linux-troubleshoot-deployment-new-vm-table](../../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-table.md)]

**Y：** 如果作業系統是一般化的 Linux，而且它是上傳和 （或） 使用的是一般化設定所擷取，則不會有任何錯誤。 同樣地，如果作業系統是特殊化的 Linux，且上傳和 (或) 擷取它時使用的是特殊化設定，就不會有任何錯誤。

**上傳錯誤：**

**N<sup>1</sup>：** 如果 OS 是通用的 Linux，但是以专用设置上传的，则会发生预配超时错误，并且 VM 会卡在预配阶段。

**N<sup>2</sup>：** 如果 OS 是专用的 Linux，但是以通用设置上传的，则会发生预配失败错误，因为新 VM 是以原始计算机名称、用户名和密码运行的。

**解決方案：**

若要解决这两个错误，请上传原始 VHD、可用的本地设置、以及与该 OS（通用/专用）相同的设置。 若要以一般化形式上傳，請務必先執行 -deprovision。

**擷取錯誤：**

**N<sup>3</sup>：** 如果 OS 是通用的 Linux，但是以专用设置捕获的，则会发生预配超时错误，因为标记为通用的原始 VM 不可用。

**N<sup>4</sup>：** 如果 OS 是专用的 Linux，但是以通用设置捕获的，则会发生预配失败错误，因为新 VM 是以原始计算机名称、用户名和密码运行的。 此外，原始 VM 會因被標示為特殊化而無法供使用。

**解決方案：**

若要解決這兩個錯誤，請從入口網站中刪除目前的映像，然後使用與作業系統相同的設定 (一般化/特殊化) [從目前的 VHD 重新擷取映像](../linux/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

## <a name="issue-custom-gallery-marketplace-image-allocation-failure"></a>問題：自定义/库/市场映像；分配失败
當新的 VM 要求被釘選到不支援所要求的 VM 大小、或沒有可用空間可處理要求的叢集，便會發生此錯誤。

**原因 1：** 叢集無法支援要求的 VM 大小。

**解決方式 1：**

* 以較小的 VM 大小重試要求。
* 如果無法變更要求的 VM 的大小︰
  * 停止可用性設定組中的所有 VM。
    单击“资源组” >  *你的资源组*  > “资源” >  *你的可用性集*  > “虚拟机” >  *你的虚拟机*  > “停止”。
  * 所有 VM 都停止後，建立所需大小的新 VM。
  * 先啟動新 VM，然後選取每個已停止的 VM 並按一下 [啟動] 。

**原因 2：** 叢集沒有可用的資源。

**解決方式 2：**

* 稍後再重試要求。
* 如果新的 VM 可以屬於不同的可用性設定組
  * 在不同的可用性設定組 (位於相同區域) 中建立新的 VM。
  * 將新的 VM 加入相同的虛擬網路。

## <a name="next-steps"></a>後續步驟
如果您在啟動已停止的 Linux VM，或重新調整 Azure 中現有的 Linux VM 大小時遇到問題，請參閱 [針對在 Azure 中重新啟動或調整現有 Linux 虛擬機器大小的 Resource Manager 部署問題進行疑難排解](../linux/restart-resize-error-troubleshooting.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

