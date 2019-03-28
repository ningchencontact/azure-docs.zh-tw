---
title: 用於 Windows 執行階段支援 Java 的 azure App Service
description: 本主題會提供在 Windows 上的 Azure App Service 支援的 Java 執行階段陳述式。
author: bmitchell287
ms.author: brendm; joshuapa
ms.date: 3/22/2019
ms.topic: article
ms.service: app-service
manager: dougE
ms.openlocfilehash: ab0e9b9f272108745b629d2cb284f6c5ab52c76f
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2019
ms.locfileid: "58522914"
---
# <a name="java-runtime-statement-of-support-for-app-service-on-windows"></a>Windows 上的 App Service 支援的 Java 執行階段陳述式

## <a name="jdk-versions-and-maintenance"></a>JDK 版本和維護

Azure 支援的 Java Development Kit (JDK) 是透過 [Azul Systems](https://www.azul.com/) 提供的 [Zulu](https://www.azul.com/downloads/azure-only/zulu/)。

我們主要版本更新時，會透過 Azure App Service 中新的執行階段選項，為 Windows。 客戶會更新為設定其 App Service 部署的這些新版 Java，以及負責測試並確定主要更新符合其需求。

每年 1 月、4 月、7 月和 10 月都會以季為單位自動修補支援的 JDK。

## <a name="security-updates"></a>安全性更新

只要主要資訊安全漏洞的修補程式和修正程式在 Azul Systems 中變成可用，就予以發行。 在 [NIST Common Vulnerability Scoring System 第 2 版](https://nvd.nist.gov/cvss.cfm)上，「主要」弱點是由基本分數 9.0 或更大值定義。

## <a name="deprecation-and-retirement"></a>取代和淘汰

如果將淘汰支援的 Java 執行階段，則使用受影響執行階段的 Azure 開發人員將會在執行階段淘汰之前至少六個月收到淘汰通知。

## <a name="local-development"></a>本機開發

開發人員可以從 [Azul 的下載網站](https://www.azul.com/downloads/azure-only/zulu/)下載實際執行版本的 Azul Zulu Enterprise JDK，以進行本機開發。

## <a name="development-support"></a>開發支援

產品支援[Azure 支援的 Azul Zulu JDK](https://www.azul.com/downloads/azure-only/zulu/)時，可透過 Microsoft azure 開發或[Azure Stack](https://azure.microsoft.com/overview/azure-stack/)具有[限定 Azure 支援計劃](https://azure.microsoft.com/support/plans/)。

## <a name="runtime-support"></a>執行階段支援

開發人員如果具有[合格的支援方案](https://azure.microsoft.com/support/plans/)，便可透過 Azure 支援服務，針對 Azul Zulu JDK [開立問題](/azure/azure-supportability/how-to-create-azure-support-request)。

## <a name="next-steps"></a>後續步驟

本主題會提供在 Windows 上的 Azure App Service 支援的 Java 執行階段陳述式。
- 若要深入了解裝載 web 應用程式與 Azure App Service，請參閱[App Service 概觀](overview.md)。
- 如需詳細資訊 Java 在 Azure 開發[Azure Java 開發人員中心](https://docs.microsoft.com/java/azure/?view=azure-java-stable)。
