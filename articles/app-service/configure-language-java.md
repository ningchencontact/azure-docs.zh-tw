---
title: 設定 Windows Java 應用程式-Azure App Service |Microsoft Docs
description: 了解如何設定 Azure App Service 中的預設 Windows 執行個體上執行的 Java 應用程式。
keywords: azure 應用程式服務、 web 應用程式、 windows、 os、 java
services: app-service
author: jasonfreeberg
manager: jeconnock
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe;cephalin
ms.custom: seodec18
ms.openlocfilehash: efeab014c7d92a6c19d516a121fbc4436925be75
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2019
ms.locfileid: "60013640"
---
# <a name="configure-a-windows-java-app-for-azure-app-service"></a>設定 Windows 的 Azure App Service 的 Java 應用程式

Azure App Service 可讓 Java 開發人員快速建置、 部署及調整其 Tomcat 或 Java Standard Edition (SE) 封裝完全受控、 以 Windows 為基礎的服務上的 web 應用程式。 從命令列或編輯器 (例如 IntelliJ、Eclipse 或 Visual Studio Code)，使用 Maven 外掛程式部署應用程式。

本指南提供重要概念和適用於 Java 開發人員使用 App Service 中的指示。 如果您從未使用過 Azure App Service，您應該閱讀[Java 快速入門](app-service-web-get-started-java.md)第一次。 中的關於使用 App Service 不是專用的 Java 開發的一般問題會有所解答[應用程式服務 Windows 常見問題集](faq-configuration-and-management.md)。

> [!NOTE]
> 找不到您要尋找的內容嗎？ 請參閱[Windows OSS 常見問題集](faq-configuration-and-management.md)或[Java Linux 設定指南](containers/app-service-linux-java.md)如需部署和保護您的 Java 應用程式。

## <a name="configuring-tomcat"></a>設定 Tomcat

若要編輯 Tomcat 的`server.xml`或其他組態檔中，先記下您 Tomcat 主要版本的入口網站中。

1. 尋找您的版本中的 Tomcat 主目錄，執行`env`命令。 搜尋開始的環境變數`AZURE_TOMCAT`且符合您的主要版本。 比方說，`AZURE_TOMCAT85_HOME`指向 Tomcat 8.5 Tomcat 目錄。
1. 一旦您已為您的版本來識別 Tomcat 主目錄，複製到的設定目錄`D:\home`。 例如，如果`AZURE_TOMCAT85_HOME`具有的值是`D:\Program Files (x86)\apache-tomcat-8.5.37`，複製的設定檔目錄的完整路徑會是`D:\home\tomcat\conf`。

最後，重新啟動 App Service。 您的部署應該移至`D:\home\site\wwwroot\webapps`如同先前一樣。

## <a name="java-runtime-statement-of-support"></a>Java 執行階段支援聲明

### <a name="jdk-versions-and-maintenance"></a>JDK 版本和維護

Azure 支援的 Java Development Kit (JDK) 是透過 [Azul Systems](https://www.azul.com/) 提供的 [Zulu](https://www.azul.com/downloads/azure-only/zulu/)。

我們主要版本更新時，會透過 Azure App Service 中新的執行階段選項，為 Windows。 客戶會更新為設定其 App Service 部署的這些新版 Java，以及負責測試並確定主要更新符合其需求。

每年 1 月、4 月、7 月和 10 月都會以季為單位自動修補支援的 JDK。

### <a name="security-updates"></a>安全性更新

只要主要資訊安全漏洞的修補程式和修正程式在 Azul Systems 中變成可用，就予以發行。 在 [NIST Common Vulnerability Scoring System 第 2 版](https://nvd.nist.gov/cvss.cfm)上，「主要」弱點是由基本分數 9.0 或更大值定義。

### <a name="deprecation-and-retirement"></a>取代和淘汰

如果將淘汰支援的 Java 執行階段，則使用受影響執行階段的 Azure 開發人員將會在執行階段淘汰之前至少六個月收到淘汰通知。

### <a name="local-development"></a>本機開發

開發人員可以從 [Azul 的下載網站](https://www.azul.com/downloads/azure-only/zulu/)下載實際執行版本的 Azul Zulu Enterprise JDK，以進行本機開發。

### <a name="development-support"></a>開發支援

產品支援[Azure 支援的 Azul Zulu JDK](https://www.azul.com/downloads/azure-only/zulu/)時，可透過 Microsoft azure 開發或[Azure Stack](https://azure.microsoft.com/overview/azure-stack/)具有[限定 Azure 支援計劃](https://azure.microsoft.com/support/plans/)。

### <a name="runtime-support"></a>執行階段支援

開發人員如果具有[合格的支援方案](https://azure.microsoft.com/support/plans/)，便可透過 Azure 支援服務，針對 Azul Zulu JDK [開立問題](/azure/azure-supportability/how-to-create-azure-support-request)。

## <a name="next-steps"></a>後續步驟

本主題會提供在 Windows 上的 Azure App Service 支援的 Java 執行階段陳述式。

- 若要深入了解裝載 web 應用程式與 Azure App Service，請參閱[App Service 概觀](overview.md)。
- 如需詳細資訊 Java 在 Azure 開發[Azure Java 開發人員中心](https://docs.microsoft.com/java/azure/?view=azure-java-stable)。
