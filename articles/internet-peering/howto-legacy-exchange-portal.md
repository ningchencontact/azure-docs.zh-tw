---
title: 使用入口網站將舊版直接對等互連轉換為 Azure 資源
titleSuffix: Azure
description: 使用入口網站將舊版直接對等互連轉換為 Azure 資源
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: a40c7bbc9f37135814b7bba3396d368faf97a166
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775195"
---
# <a name="convert-a-legacy-exchange-peering-to-azure-resource-using-the-portal"></a>使用入口網站將舊版 Exchange 對等互連轉換成 Azure 資源

本文說明如何使用入口網站將現有的舊版 Exchange 對等互連轉換成 Azure 資源。

如果您想要的話，可以使用[PowerShell](howto-legacy-exchange-powershell.md)來完成本指南。

## <a name="before-you-begin"></a>開始之前
* 開始設定之前，請先參閱[必要條件](prerequisites.md)和[Exchange 對等的逐步](walkthrough-exchange-all.md)解說。

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>將舊版 Exchange 對等互連轉換成 Azure 資源

### <a name="sign-in-to-portal-and-select-your-subscription"></a>登入入口網站並選取您的訂用帳戶
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name=create></a>轉換舊版 Exchange 對等互連

您可以使用對**等互連**資源轉換舊版對等互連連線。

#### <a name="launch-resource-and-configure-basic-settings"></a>啟動資源並設定基本設定
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>設定連接並提交
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration-legacy.md)]

### <a name=get></a>驗證 Exchange 對等
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="additional-resources"></a>其他資源

如需詳細資訊，請造訪[網際網路對等常見問題](faqs.md)

## <a name="next-steps"></a>後續步驟

* [使用入口網站建立或修改 Exchange 對等互連](howto-exchange-portal.md)
