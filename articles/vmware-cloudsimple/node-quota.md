---
title: Azure VMware Solution by CloudSimple-CloudSimple 節點配額
description: 描述 CloudSimple 節點的配額限制, 以及如何要求增加配額
author: sharaths-cs
ms.author: dikamath
ms.date: 04/30/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e765d7c87f2f941a5e2d558b71c4e5a71d2df9b1
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816681"
---
# <a name="cloudsimple-node-quota-limits"></a>CloudSimple 節點配額限制

當您的訂用帳戶已啟用 CloudSimple 服務時, 有四個節點是可用來布建的預設數量。  您可以從 Azure 入口網站布建任何[節點類型](cloudsimple-node.md)。  必須至少有三個相同 SKU 的節點, 才能建立私人雲端。  如果您已布建節點, 當您嘗試提供其他節點時, 可能會看到錯誤。

## <a name="quota-increase"></a>增加配額

您可以透過提交支援要求來增加節點配額。 服務營運小組會評估要求, 並與您合作以增加節點配額。  當您開啟新的票證時, 請選取下列選項:

* 問題類型：**技術**
* 訂閱:**您的訂用帳戶識別碼**
* 服務類型:**VMware 解決方案 (依 CloudSimple)**
* 問題類型:**專用節點配額**
* 問題子類型:**增加專用節點的配額**
* 主體:**增加配額**

在支援票證的詳細資料中, 提供必要的節點數目和節點 SKU。

您也可以在上[azurevmwaresales@microsoft.com](mailto:azurevmwaresales@microsoft.com)聯絡您的 Microsoft 帳戶代表, 以提高訂用帳戶的節點配額。  您必須提供:

* 訂用帳戶識別碼
* Node SKU
* 您正在要求增加配額的其他節點數目

## <a name="next-steps"></a>後續步驟

* [布建節點](create-nodes.md)
* [CloudSimple 節點總覽](cloudsimple-node.md)