---
title: Azure Resource Manager 範本範例 - App Service | Microsoft Docs
description: Azure Resource Manager 的 App Service 範本範例
services: app-service
documentationcenter: app-service
author: tfitzmac
tags: azure-service-management
ms.service: app-service
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 01/04/2019
ms.author: tomfitz
ms.custom: mvc
ms.openlocfilehash: 83bb357544d9069da1c86f583eaca5a470953ce8
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70066478"
---
# <a name="azure-resource-manager-templates-for-app-service"></a>Azure Resource Manager 的 App Service 範本

下表包含 Azure Resource Manager 的 Azure App Service 範本連結。 如需有關在建立應用程式範本時避免發生常見錯誤的建議，請參閱[使用 Azure Resource Manager 範本來部署應用程式的指引](deploy-resource-manager-template.md)。

若要深入了解 App Service 資源的 JSON 語法和屬性，請參閱 [Microsoft.Web 資源類型](/azure/templates/microsoft.web/allversions)。

| | |
|-|-|
|**部署應用程式**||
| [App Service 方案和基本 Linux 應用程式](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-linux) \(英文\) | 部署針對 Linux 設定的 App Service 應用程式。 |
| [App Service 方案和基本 Windows 應用程式](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-windows) \(英文\) | 部署針對 Windows 設定的 App Service 應用程式。 |
| [連結至 GitHub 存放庫的應用程式](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-github-deploy) \(英文\)| 部署可從 GitHub 提取程式碼的 App Service 應用程式。 |
| [具有自訂部署位置的應用程式](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-custom-deployment-slots) \(英文\)| 部署具有自訂部署位置/環境的 App Service 應用程式。 |
|**設定應用程式**||
| [來自金鑰保存庫的應用程式憑證](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault) \(英文\)| 部署來自 Azure Key Vault 祕密的 App Service 應用程式憑證，並使用它來進行 SSL 繫結。 |
| [具有自訂網域的應用程式](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain) \(英文\)| 部署具有自訂主機名稱的 App Service 應用程式。 |
| [具有自訂網域和 SSL 的應用程式](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain-and-ssl) \(英文\)| 部署具有自訂主機名稱的 App Service 應用程式，並從 Key Vault 取得應用程式憑證來進行 SSL 繫結。 |
| [具有 GoLang 延伸模組的應用程式](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-with-golang) \(英文\)| 部署具有 Golang 網站延伸模組的 App Service 應用程式。 然後，您可以在 Azure 上執行以 Golang 開發的 Web 應用程式。 |
| [採用 Java 8 和 Tomcat 8 的應用程式](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-java-tomcat) \(英文\)| 部署已啟用 Java 8 和 Tomcat 8 的 App Service 應用程式。 然後，您可以在 Azure 中執行 Java 應用程式。 |
|**具有已連線資源的 Linux 應用程式**||
| [在採用 MySQL 的 Linux 上部署應用程式](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-mysql) \(英文\) | 在具有「適用於 MySQL 的 Azure 資料庫」的 Linux 上部署 App Service 應用程式。 |
| [在採用 PostgreSQL 的 Linux 上部署應用程式](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-postgresql) \(英文\) | 在具有「適用於 PostgreSQL 的 Azure 資料庫」的 Linux 上部署 App Service 應用程式。 |
|**具有已連線資源的應用程式**||
| [搭配 MySQL 的應用程式](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-mysql) \(英文\)| 在具有「適用於 MySQL 的 Azure 資料庫」的 Windows 上部署 App Service 應用程式。 |
| [搭配 PostgreSQL 的應用程式](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-postgresql) \(英文\)| 在具有「適用於 PostgreSQL 的 Azure 資料庫」的 Windows 上部署 App Service 應用程式。 |
| [搭配 SQL 資料庫的應用程式](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database) \(英文\)| 在「基本」服務層級部署 App Service 應用程式與 SQL 資料庫。 |
| [具有 Blob 儲存體連線的應用程式](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-blob-connection) \(英文\)| 使用 Azure Blob 儲存體連接字串來部署 App Service 應用程式。 如此，您便可以從該應用程式使用 Blob 儲存體。 |
| [具有 Azure Redis 快取的應用程式](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-redis-cache) \(英文\)| 部署具有「Azure Redis 快取」的 App Service 應用程式。 |
|**適用於 PowerApps 的 App Service Environment**||
| [建立 App Service Environment v2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-create) | 在虛擬網路中建立 App Service Environment v2。 |
| [建立具有 ILB 位址的 App Service Environment v2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-ilb-create/) | 在虛擬網路中建立具有私人內部負載平衡位址的 App Service Environment v2。 |
| [設定 ILB App Service Environment 或 ILB App Service Environment v2 的預設 SSL 憑證](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-ase-ilb-configure-default-ssl) | 設定 ILB App Service Environment 或 ILB App Service Environment v2 的預設 SSL 憑證。 |
| | |
