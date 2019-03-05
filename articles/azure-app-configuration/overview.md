---
title: 什麼是 Azure 應用程式設定 | Microsoft Docs
description: Azure 應用程式設定服務的概觀。
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 11dd91039bb352e86800982d0a294f82622a56fe
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2019
ms.locfileid: "56885016"
---
# <a name="what-is-azure-app-configuration"></a>什麼是 Azure 應用程式設定

Azure 應用程式設定提供集中管理應用程式設定的服務。 最新的程式 (特別是在雲端中執行的程式) 通常會有許多分散性質的元件。 將組態設定分散到這些元件，可能會導致應用程式部署期間發生難以排解的錯誤。 應用程式設定可讓您儲存應用程式的所有設定，並在同一個地方保護其存取權。

## <a name="why-use-app-configuration"></a>為什麼使用應用程式設定

雲端式應用程式通常會在多個區域中的多個虛擬機器或容器上執行，並使用多個外部服務。 建立這類穩固且可調整的分散式應用程式真的是一項挑戰。 為協助開發人員在建置這些應用程式時，能處理不斷提高的複雜度，現在已有各式各樣的程式設計方法可使用。 例如，12-factor 應用程式會詳述許多通過完整測試的架構模式和最佳做法，以搭配雲端應用程式使用。 本指南中的其中一個重要建議就是區隔組態與程式碼。 這表示應用程式的組態 (例如設定) 應該保留在其可執行檔的外部，並從其執行階段環境或外部來源讀入。

任何應用程式都可使用此功能，而以下是較適用應用程式設定的應用程式類型範例：

* 以一個或多個地理位置上所部署 AKS、Service Fabric 或其他容器化應用程式為基礎的微服務。
* 無伺服器應用程式，包括 Azure Functions 或其他由事件驅動的無狀態計算應用程式。
* 持續部署管線。

應用程式設定具有下列優點︰

* 能在幾分鐘內完成設定的完全受控服務。
* 彈性的索引鍵表示法和對應。
* 使用標籤進行標記。
* 根據時間點重新執行設定。
* 在自行定義的維度上比較兩個設定集。
* 已透過 Azure 管理的身分識別增強安全性。
* 完整的資料加密 (待用或傳輸中加密)。
* 與熱門架構原生整合。

## <a name="how-to-use-app-configuration"></a>如何使用應用程式設定

您可以透過 Microsoft 提供的用戶端程式庫，將應用程式設定存放區新增至您的應用程式，這是最簡單的方式。 以下是適用於不同程式設計語言和架構的最佳方法：

| 程式設計語言和架構 | 如何連接 |
|---|---|
| **.NET Core** 和 **ASP.NET Core** | 適用於 .NET Core 的應用程式設定組態提供者。 |
| **.NET** 和 **ASP.NET** | 適用於 .NET 的應用程式設定組態建立器。 |
| **Java Spring** | 適用於 Spring Cloud 的應用程式設定組態用戶端。 |
| 其他 | 應用程式設定 REST API。 |

## <a name="next-steps"></a>後續步驟

* [快速入門：建立 ASP.NET Web 應用程式](quickstart-aspnet-core-app.md)  
