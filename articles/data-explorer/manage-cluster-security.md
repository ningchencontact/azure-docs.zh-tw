---
title: 在 Azure 中保護您的叢集資料總管
description: 本文說明如何在 Azure 入口網站中保護 Azure 資料總管中的叢集。
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: ad08bb19f96aadad42e973eebb8adce6875e07b1
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876584"
---
# <a name="secure-your-cluster-in-azure-data-explorer"></a>在 Azure 中保護您的叢集資料總管

[Azure 磁碟加密](/azure/security/azure-security-disk-encryption-overview)有助於保護和保護您的資料, 以符合組織的安全性和合規性承諾。 它提供叢集虛擬機器的 OS 和資料磁片的磁片區加密。 它也會與[Azure Key Vault](/azure/key-vault/)整合, 協助您控制及管理磁片加密金鑰和密碼, 並確保 VM 磁片上的所有資料在 Azure 儲存體時都已加密。 您的叢集安全性設定可讓您在叢集上啟用磁片加密。
  
## <a name="enable-encryption-at-rest"></a>啟用待用加密
  
在您的叢集上啟用待用[加密](/azure/security/fundamentals/encryption-atrest)可為儲存的資料 (待用) 提供資料保護。 

1. 在 Azure 入口網站中, 移至您的 Azure 資料總管叢集資源。 在 [**設定**] 標題下, 選取 [**安全性**]。 

    ![開啟待用加密](media/manage-cluster-security/security-encryption-at-rest.png)

1. 在 [**安全性**] 視窗中, 針對 [**磁片加密**] 安全性設定選取 [**開啟**]。 

1. 選取 [ **儲存**]。
 
> [!NOTE]
> 加密可在啟用後停用。

## <a name="next-steps"></a>後續步驟

[檢查叢集健康情況](/azure/data-explorer/check-cluster-health)
