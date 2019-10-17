---
title: 參考： DSVM 影像淘汰
description: 影響 Azure 資料科學虛擬機器的棄用功能詳細資料（DSVM）
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 10/14/2019
ms.topic: reference
ms.openlocfilehash: 4968ac08b75141ed84994ca287215a34728232c4
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333335"
---
# <a name="reference-deprecation-of-dsvm-images"></a>參考：取代 DSVM 映射

下面我們將討論在 Azure 資料科學虛擬機器上處理即將推出之棄用功能的建議。

## <a name="windows-2012-migrating-data-disks"></a>Windows 2012：正在遷移資料磁片

我們將于2019年11月5日停止支援 Windows 2012 DSVM 映射。 若要將資料磁片從現有的 Windows 2012 DSVM 遷移至 Windows 2016 DSVM，請執行下列步驟：

1. 依照[這裡](./provision-vm.md#create-your-dsvm)所示的指示，建立新的 WINDOWS 2016 DSVM。
1. 使用[這些指示](../../virtual-machines/windows/detach-disk.md)從 Windows 2012 映射卸離現有的資料磁片。
1. 使用[這些指示](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm)，將上一個步驟中的磁片連結至您的 Windows 2016 映射。
