---
title: 設定 Windows JAVA 應用程式-Azure App Service |Microsoft Docs
description: 瞭解如何設定 JAVA 應用程式, 以在 Azure App Service 中的預設 Windows 實例上執行。
keywords: azure app service, web 應用程式, windows, oss, java
services: app-service
author: jasonfreeberg
manager: jeconnock
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe
ms.reviewer: cephalin
ms.custom: seodec18
ms.openlocfilehash: c1ea306d8a6b5c1876ac6a9288820e1592dbfda6
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/25/2019
ms.locfileid: "68498519"
---
# <a name="configure-a-windows-java-app-for-azure-app-service"></a>設定適用于 Azure App Service 的 Windows JAVA 應用程式

Azure App Service 可讓 JAVA 開發人員在完全受控的 Windows 服務上, 快速建立、部署及調整其 Tomcat 或 JAVA Standard Edition (SE) 封裝的 web 應用程式。 從命令列或編輯器 (例如 IntelliJ、Eclipse 或 Visual Studio Code)，使用 Maven 外掛程式部署應用程式。

本指南提供 JAVA 開發人員在 App Service 中使用的重要概念和指示。 如果您從未使用過 Azure App Service, 應該先閱讀[JAVA 快速入門](app-service-web-get-started-java.md)。 有關使用不是 JAVA 開發特定 App Service 的一般問題, 請在[App Service WINDOWS 常見問題](faq-configuration-and-management.md)中獲得解答。

> [!NOTE]
> 找不到您要尋找的內容嗎？ 如需部署和保護 JAVA 應用程式的相關資訊, 請參閱[WINDOWS OSS 常見問題](faq-configuration-and-management.md)或[java Linux 設定指南](containers/configure-language-java.md)。

## <a name="configuring-tomcat"></a>正在設定 Tomcat

若要編輯 tomcat `server.xml`或其他設定檔, 請先記下您在入口網站中的 tomcat 主要版本。

1. 執行`env`命令, 尋找您版本的 Tomcat 主目錄。 搜尋以開頭`AZURE_TOMCAT`的環境變數, 並符合您的主要版本。 例如, `AZURE_TOMCAT85_HOME`指向 tomcat 8.5 的 tomcat 目錄。
1. 一旦您識別出版本的 Tomcat 主目錄, 請將設定目錄複寫到`D:\home`。 例如, 如果`AZURE_TOMCAT85_HOME`的`D:\Program Files (x86)\apache-tomcat-8.5.37`值為, 則複製之目錄的新路徑會是`D:\home\apache-tomcat-8.5.37`。

最後，重新啟動 App Service。 您的部署應該會`D:\home\site\wwwroot\webapps`如同之前一樣。

## <a name="java-runtime-statement-of-support"></a>Java 執行階段支援聲明

### <a name="jdk-versions-and-maintenance"></a>JDK 版本和維護

Azure 支援的 Java Development Kit (JDK) 是透過 [Azul Systems](https://www.azul.com/) 提供的 [Zulu](https://www.azul.com/downloads/azure-only/zulu/)。

主要版本更新將透過 Windows Azure App Service 中的新執行時間選項來提供。 客戶會更新為設定其 App Service 部署的這些新版 Java，以及負責測試並確定主要更新符合其需求。

每年 1 月、4 月、7 月和 10 月都會以季為單位自動修補支援的 JDK。

### <a name="security-updates"></a>安全性更新

只要主要資訊安全漏洞的修補程式和修正程式在 Azul Systems 中變成可用，就予以發行。 在 [NIST Common Vulnerability Scoring System 第 2 版](https://nvd.nist.gov/cvss.cfm)上，「主要」弱點是由基本分數 9.0 或更大值定義。

### <a name="deprecation-and-retirement"></a>取代和淘汰

如果將淘汰支援的 Java 執行階段，則使用受影響執行階段的 Azure 開發人員將會在執行階段淘汰之前至少六個月收到淘汰通知。

### <a name="local-development"></a>本機開發

開發人員可以從 [Azul 的下載網站](https://www.azul.com/downloads/azure-only/zulu/)下載實際執行版本的 Azul Zulu Enterprise JDK，以進行本機開發。

### <a name="development-support"></a>開發支援

使用[合格的 Azure 支援方案](https://azure.microsoft.com/support/plans/)來開發 azure 或[Azure Stack](https://azure.microsoft.com/overview/azure-stack/)時, 可透過 Microsoft 取得支援[azure 的 Azul 祖魯 JDK](https://www.azul.com/downloads/azure-only/zulu/)的產品支援。

### <a name="runtime-support"></a>執行階段支援

開發人員如果具有[合格的支援方案](https://azure.microsoft.com/support/plans/)，便可透過 Azure 支援服務，針對 Azul Zulu JDK [開立問題](/azure/azure-supportability/how-to-create-azure-support-request)。

## <a name="next-steps"></a>後續步驟

本主題提供 JAVA 執行時間對 Windows Azure App Service 的支援聲明。

- 若要深入瞭解如何使用 Azure App Service 裝載 web 應用程式, 請參閱[App Service 總覽](overview.md)。
- 如需有關 Azure 開發之 JAVA 的詳細資訊, 請參閱適用[于 JAVA 開發人員中心的 Azure](https://docs.microsoft.com/java/azure/?view=azure-java-stable)。
