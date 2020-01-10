---
title: 使用入口網站建立或修改 Exchange 對等互連
titleSuffix: Azure
description: 使用入口網站建立或修改 Exchange 對等互連
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 2c186decf68d167ab2c5ab7696c2dfb51d77a071
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774571"
---
# <a name="create-or-modify-an-exchange-peering-using-the-portal"></a>使用入口網站建立或修改 Exchange 對等互連

本文說明如何使用入口網站建立 Microsoft Exchange 對等互連。 本文也會說明如何檢查資源的狀態、加以更新，或刪除並取消布建。

如果您想要的話，可以使用[PowerShell](howto-exchange-powershell.md)來完成本指南。

## <a name="before-you-begin"></a>開始之前
* 開始設定之前，請先參閱[必要條件](prerequisites.md)和[Exchange 對等的逐步](walkthrough-exchange-all.md)解說。
* 如果您已有 Microsoft 的 Exchange 對等互連，但未轉換成 Azure 資源，請參閱[使用入口網站將舊版 Exchange 對等互連轉換成 azure 資源](howto-legacy-exchange-portal.md)

## <a name="create-and-provision-an-exchange-peering"></a>建立和布建 Exchange 對等互連

### <a name="sign-in-to-portal-and-select-your-subscription"></a>登入入口網站並選取您的訂用帳戶
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name=create></a>建立 Exchange 對等互連

您可以使用對**等互連**資源建立新的對等互連要求。

#### <a name="launch-resource-and-configure-basic-settings"></a>啟動資源並設定基本設定
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>設定連接並提交
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration.md)]

### <a name=get></a>驗證 Exchange 對等互連
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="modify"></a>修改 Exchange 對等互連
[!INCLUDE [peering-exchange-modify-portal](./includes/exchange-portal-modify.md)]

## <a name="delete"></a>取消布建 Exchange 對等互連
[!INCLUDE [peering-exchange-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>後續步驟

* [使用入口網站建立或修改直接對等互連](howto-direct-portal.md)
* [使用入口網站將舊版直接對等互連轉換為 Azure 資源](howto-legacy-direct-portal.md)

## <a name="additional-resources"></a>其他資源

如需詳細資訊，請造訪[網際網路對等常見問題](faqs.md)
