---
title: Azure 傳統部署模型 |Microsoft Docs
description: Azure 傳統部署模型
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/20/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 5ae2a56c084116ae8d57a16696223e7990258558
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/21/2019
ms.locfileid: "67313291"
---
# <a name="classic-deployment-model"></a>傳統部署模型

傳統部署模型是將 Azure 部署模式 land 會強制執行全域的 vCPU 配額限制的虛擬機器和虛擬機器擴展集的較舊版本。 傳統部署模型不會再建議和 Resource Manager 模型現在已取代。 若要深入了解這些兩種部署模型和利用資源管理員的參考 Resource Manager 部署模型 頁面。 建立新的訂用帳戶時，預設的 Vcpu 配額會指派給它。 使用傳統部署模型部署為新的 VM，每當新的和現有的 Vcpu 跨所有區域的使用方式的總和不得超過核准適用於傳統部署模型的 vCPU 配額。 深入了解 Azure 訂用帳戶和服務限制頁面上的配額。

您可以要求增加 Vcpu 限制傳統部署模型，透過 說明 + 支援 刀鋒視窗或使用方式 + 配額 刀鋒視窗，在入口網站。

