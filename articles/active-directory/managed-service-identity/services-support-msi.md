---
title: 支援「受控服務識別」的 Azure 服務
description: 支援「受控服務識別」和「Azure AD 驗證」的服務清單
services: active-directory
author: daveba
ms.author: daveba
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: active-directory
ms.component: msi
manager: mtillman
ms.openlocfilehash: 74fb9e784122dadd1ad2f6f29a497398eacf7464
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/02/2018
ms.locfileid: "39412877"
---
# <a name="services-that-support-managed-service-identity"></a>支援受控服務識別的服務 

在 Azure Active Directory 中，「受控服務身分識別」會提供自動受控身分給 Azure 服務。 您可以使用受控身分識別，向任何支援 Azure AD 驗證的服務進行驗證，而不需要您程式碼中的任何認證。 我們正在努力整合受控服務識別和整個 Azure 的 Azure AD 驗證。 請經常回來檢查更新。

## <a name="azure-services-that-support-managed-service-identity"></a>支援「受控服務身分識別」的 Azure 服務

下列 Azure 服務支援受控服務身分識別。

| 服務 | 系統指派的狀態 | 使用者指派的狀態| 設定 | 取得權杖 |
| ------- | ------ | ---- | --------- | ----------- |
| Azure 虛擬機器 | 預覽 | 預覽 | [Azure 入口網站](qs-configure-portal-windows-vm.md)<br>[PowerShell](qs-configure-powershell-windows-vm.md)<br>[Azure CLI](qs-configure-cli-windows-vm.md)<br>[Azure 資源管理員範本](qs-configure-template-windows-vm.md)<br>[REST](qs-configure-rest-vm.md) | [REST](how-to-use-vm-token.md#get-a-token-using-http)<br>[.NET](how-to-use-vm-token.md#get-a-token-using-c)<br>[Bash/Curl](how-to-use-vm-token.md#get-a-token-using-curl)<br>[Go](how-to-use-vm-token.md#get-a-token-using-go)<br>[PowerShell](how-to-use-vm-token.md#get-a-token-using-azure-powershell) |
| 虛擬機器擴展集 | 預覽 | 預覽 | [Azure 入口網站](qs-configure-portal-windows-vmss.md)<br>[PowerShell](qs-configure-powershell-windows-vmss.md)<br>[Azure CLI](qs-configure-cli-windows-vmss.md)<br>[Azure 資源管理員範本](qs-configure-template-windows-vmss.md)<br>[REST](qs-configure-rest-vmss.md) | [REST](how-to-use-vm-token.md#get-a-token-using-http)<br>[.NET](how-to-use-vm-token.md#get-a-token-using-c)<br>[Bash/Curl](how-to-use-vm-token.md#get-a-token-using-curl)<br>[Go](how-to-use-vm-token.md#get-a-token-using-go)<br>[PowerShell](how-to-use-vm-token.md#get-a-token-using-azure-powershell)
| Azure App Service | 可用 | 尚未提供 | [Azure 入口網站](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Azure CLI](/azure/app-service/app-service-managed-service-identity#using-the-azure-cli)<br>[Azure PowerShell](/azure/app-service/app-service-managed-service-identity#using-azure-powershell)<br>[Azure Resource Manager 範本](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol)<br>[.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[JavaScript](/azure/app-service/app-service-managed-service-identity#token-js)<br>[PowerShell](/azure/app-service/app-service-managed-service-identity#token-powershell)  |
| Azure Functions | 可用 | 尚未提供 | [Azure 入口網站](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Azure CLI](/azure/app-service/app-service-managed-service-identity#using-the-azure-cli)<br>[Azure PowerShell](/azure/app-service/app-service-managed-service-identity#using-azure-powershell)<br>[Azure Resource Manager 範本](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol)<br>[.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[JavaScript](/azure/app-service/app-service-managed-service-identity#token-js)<br>[PowerShell](/azure/app-service/app-service-managed-service-identity#token-powershell) |
| Azure Data Factory V2 | 預覽 | 尚未提供 | [Azure 入口網站](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity)<br>[PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-powershell)<br>[REST](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-rest-api)<br>[SDK](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-sdk) |
| Azure API 管理 | 預覽 | 尚未提供 | [Azure Resource Manager 範本](/azure/api-management/api-management-howto-use-managed-service-identity) | 


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
| Azure 儲存體 | https://storage.azure.com/ | 預覽 | 2018 年 5 月 | |
