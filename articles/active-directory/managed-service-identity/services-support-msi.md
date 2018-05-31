---
title: 支援「受控服務識別」的 Azure 服務
description: 支援「受控服務識別」和「Azure AD 驗證」的服務清單
services: active-directory
author: daveba
ms.author: daveba
ms.date: 03/28/2018
ms.topic: reference
ms.service: active-directory
ms.component: msi
manager: mtillman
ms.openlocfilehash: d31c169600c594fc4764262cb8d080e9aee96b21
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2018
ms.locfileid: "34353606"
---
# <a name="services-that-support-managed-service-identity"></a>支援受控服務識別的服務 

在 Azure Active Directory 中，「受控服務身分識別」會提供自動受控身分給 Azure 服務。 您可以使用受控身分識別，向任何支援 Azure AD 驗證的服務進行驗證，而不需要您程式碼中的任何認證。 我們正在努力整合 MSI 和整個 Azure 的 Azure AD 驗證。 請經常回來檢查更新。

## <a name="azure-services-that-support-managed-service-identity"></a>支援「受控服務身分識別」的 Azure 服務

下列 Azure 服務支援受控服務身分識別。

| 服務 | 狀態 | 日期 | 設定 | 取得權杖 |
| ------- | ------ | ---- | --------- | ----------- |
| Azure 虛擬機器 | 預覽 | 2017 年 9 月 | [Azure 入口網站](qs-configure-portal-windows-vm.md)<br>[PowerShell](qs-configure-powershell-windows-vm.md)<br>[Azure CLI](qs-configure-cli-windows-vm.md)<br>[Azure 資源管理員範本](qs-configure-template-windows-vm.md) | [REST](how-to-use-vm-token.md#get-a-token-using-http)<br>[.NET](how-to-use-vm-token.md#get-a-token-using-c)<br>[Bash/Curl](how-to-use-vm-token.md#get-a-token-using-curl)<br>[Go](how-to-use-vm-token.md#get-a-token-using-go)<br>[PowerShell](how-to-use-vm-token.md#get-a-token-using-azure-powershell) |
| Azure App Service | 預覽 | 2017 年 9 月 | [Azure 入口網站](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Azure Resource Manager 範本](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol) |
| Azure Functions | 預覽 | 2017 年 9 月 | [Azure 入口網站](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Azure Resource Manager 範本](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol) |
| Azure Data Factory V2 | 預覽 | 2017 年 11 月 | [Azure 入口網站](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity)<br>[PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-powershell)<br>[REST](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-rest-api)<br>[SDK](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-sdk) |
| Azure API 管理 | 預覽 | 2017 年 10 月 | [Azure Resource Manager 範本](/azure/api-management/api-management-howto-use-managed-service-identity) |

## <a name="azure-services-that-support-azure-ad-authentication"></a>支援 Azure AD 驗證的 Azure 服務

下列服務支援 Azure AD 驗證，也經過使用受控服務識別的用戶端服務所測試。

| 服務 | 資源識別碼 | 狀態 | 日期 | 指派存取權 |
| ------- | ----------- | ------ | ---- | ------------- |
| Azure Resource Manager | https://management.azure.com/ | 可用 | 2017 年 9 月 | [Azure 入口網站](howto-assign-access-portal.md) <br>[PowerShell](howto-assign-access-powershell.md) <br>[Azure CLI](howto-assign-access-CLI.md) |
| Azure 金鑰保存庫 | https://vault.azure.net | 可用 | 2017 年 9 月 | |
| Azure Data Lake | https://datalake.azure.net/ | 可用 | 2017 年 9 月 | |
| Azure SQL | https://database.windows.net/ | 可用 | 2017 年 10 月 | |
| Azure 事件中心 | https://eventhubs.azure.net | 可用 | 2017 年 12 月 | |
| Azure 服務匯流排 | https://servicebus.azure.net | 可用 | 2017 年 12 月 | |
