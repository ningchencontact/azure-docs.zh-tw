---
title: 將程式碼升級至最新的平台 | Microsoft Docs
description: 此主題說明如何將 Microsoft Dynamics 365 for Operations 平台版本升級至最新的平台版本
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 787d9ef509bcafeb6a21d4b0ec952ea1eb4fd52b
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2018
ms.locfileid: "48806397"
---
# <a name="upgrading-code-to-the-latest-platform"></a>將程式碼升級至最新的平台

此文章說明如何將 Microsoft Dynamics 365 for Operations 平台版本升級至最新的平台版本。

## <a name="overview"></a>概觀

Microsoft Dynamics 365 for Operations 平台是由下列元件所組成：

Dynamics 365 for Operations 平台二進位檔，例如應用程式物件伺服器 (AOS)、資料管理架構、報告和商務智慧 (BI) 架構、開發工具和分析服務。 下列應用程式物件樹狀結構 (AOT) 套件：

1. 應用程式平台
2. 應用程式基礎
3. 測試基本資訊

**重要**：若要移至最新的 Dynamics 365 for Operations平台，，您的 Dynamics 365 for Operations 實作不能有任何屬於該平台之 AOT 套件的自訂項目 (overlayering)。 此限制是在平台更新 3 引進的，因此可以對平台進行無縫式的持續更新。 如果您在早於平台更新 3 的平台上執行，請參閱此文章末的＜從舊版組建升級到平台更新 3＞一節。

如需程式碼升級的詳細資訊，請參閱[此處](https://docs.microsoft.com/dynamics365/operations/dev-itpro/migration-upgrade/upgrade-latest-platform-update)。