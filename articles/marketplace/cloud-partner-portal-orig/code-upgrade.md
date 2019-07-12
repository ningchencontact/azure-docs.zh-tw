---
title: 升級至最新的平台的程式碼 |Azure Marketplace
description: 此主題說明如何將 Microsoft Dynamics 365 for Operations 平台版本升級至最新的平台版本
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricardo.Villalobos
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: aedc2c7474de0fe068a329eb2205e9bb08e62c3a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935292"
---
# <a name="upgrading-code-to-the-latest-platform"></a>將程式碼升級至最新的平台

此文章說明如何將 Microsoft Dynamics 365 for Operations 平台版本升級至最新的平台版本。

## <a name="overview"></a>總覽

Microsoft Dynamics 365 for Operations 平台是由下列元件所組成：

Dynamics 365 for Operations 平台二進位檔，例如應用程式物件伺服器 (AOS)、資料管理架構、報告和商務智慧 (BI) 架構、開發工具和分析服務。 下列應用程式物件樹狀結構 (AOT) 套件：

1. 應用程式平台
2. 應用程式基礎
3. 測試基本資訊

**重要**：若要移至最新的 Dynamics 365 作業平台，作業實作您 Dynamics 365 不能有任何自訂項目 (overlayering) 的任何屬於平台的 AOT 封裝。 此限制是在平台更新 3 引進的，因此可以對平台進行無縫式的持續更新。 如果您在早於平台更新 3 的平台上執行，請參閱此文章末的＜從舊版組建升級到平台更新 3＞一節。

如需程式碼升級的詳細資訊，請參閱[此處](https://docs.microsoft.com/dynamics365/operations/dev-itpro/migration-upgrade/upgrade-latest-platform-update)。