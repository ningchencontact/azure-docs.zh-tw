---
title: 使用 Azure Stack 中的入口網站部署範本 | Microsoft Docs
description: 了解如何使用 Azure Stack 入口網站部署範本。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: eafa60f2-16c9-4ef1-b724-47709e9ea29e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2019
ms.author: sethm
ms.reviewer: unknown
ms.openlocfilehash: 4a8d4ab4d93831abbd9489d9023dd9b6f5c66d6d
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2019
ms.locfileid: "54246886"
---
# <a name="deploy-templates-using-the-azure-stack-portal"></a>使用 Azure Stack 入口網站部署範本

*適用於：Azure Stack 整合式系統和 Azure Stack 開發套件*

您可以使用入口網站將 Azure Resource Manager 範本部署到 Azure Stack。

## <a name="to-deploy-a-template"></a>部署範本

1. 登入入口網站，選取 [+ 建立資源]，然後選取 [自訂]。
2. 選取 [範本部署]。
3. 選取 [編輯範本]，然後將 JSON 範本程式碼貼入程式碼視窗。 選取 [ **儲存**]。
4. 選取 [編輯參數]，為顯示的參數提供值，然後選取 [確定]。
5. 選取 [訂用帳戶]。 選擇您想要使用的訂用帳戶，然後選取 [確定]。
6. 選取 [資源群組]。 選擇現有的資源群組或建立新群組，然後選取 [確定]。
7. 選取 [建立] 。 在儀表板上的新圖格會追蹤範本部署的進度。

## <a name="next-steps"></a>後續步驟

若要深入了解部署範本，請參閱下列文章：

- [使用 PowerShell 部署範本](azure-stack-deploy-template-powershell.md)
