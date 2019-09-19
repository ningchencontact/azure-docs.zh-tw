---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: include
ms.date: 09/18/2019
ms.author: azcspmt;ayshak;cynthn
ms.custom: include file
ms.openlocfilehash: 0f528117b19a3f26b964dee7ae453fc184c8dddb
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/19/2019
ms.locfileid: "71123057"
---
Azure 計算服務所提供的虛擬機器大小不受特定硬體類型限制，而且為單一客戶專用。  這些虛擬機器大小最適合需要與其他客戶高度隔離，且涉及合規性和法規要求等元素的工作負載。  客戶也可以選擇使用 [Azure 的巢狀虛擬機器支援](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)，進一步細分這些隔離虛擬機器的資源。

使用隔離大小可確保只有您的虛擬機器會在該特定伺服器執行個體上執行。  目前的隔離虛擬機器供應項目包括：
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_M128ms
* Standard_GS5
* Standard_G5
* Standard_DS15_v2
* Standard_D15_v2
* Standard_F72s_v2

您可以在[這裡](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory)深入瞭解每個可用的隔離大小。