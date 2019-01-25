---
title: 支援適用於 Azure 資源的受控識別 Azure 服務
description: 支援 Azure 資源和 Azure AD 驗證受控識別的服務清單
services: active-directory
author: daveba
ms.author: daveba
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: active-directory
ms.component: msi
manager: daveba
ms.openlocfilehash: 7591860d36a3d6472411321c60c608411ab4eb8b
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2019
ms.locfileid: "54437669"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>支援適用於 Azure 資源的受控識別服務

Azure 資源受控識別會在 Azure Active Directory 中為 Azure 服務提供自動受控識別。 您可以使用受控身分識別，向任何支援 Azure AD 驗證的服務進行驗證，而不需要您程式碼中的任何認證。 我們正在努力對於 Azure 的 Azure 資源和 Azure AD 驗證整合受控識別。 請經常回來檢查更新。

> [!NOTE]
> 先前稱為「受控服務識別」(MSI) 的服務，其新名稱為「Azure 資源適用受控識別」。

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>支援適用於 Azure 資源的受控識別 Azure 服務

下列 Azure 服務支援 Azure 資源的受控識別：

### <a name="azure-virtual-machines"></a>Azure 虛擬機器

|受控識別類型 |  全部正式推出<br>全域 Azure 區域 | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| 系統指派 | 可用 | 預覽 | 預覽 | 預覽 | 預覽 |
| 使用者指派 | 預覽 | 預覽 | 預覽 | 預覽 | 預覽

請參閱下列清單來設定 Azure 虛擬機器的受控識別 (若區域提供)：

- [Azure 入口網站](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure 資源管理員範本](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-virtual-machine-scale-sets"></a>Azure 虛擬機器擴展集

|受控識別類型 |  全部正式推出<br>全域 Azure 區域 | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| 系統指派 | 可用 | 預覽 | 預覽 | 預覽 |
| 使用者指派 | 預覽 | 預覽 | 預覽 | 預覽

請參閱下列清單來設定 Azure 虛擬機器擴展集的受控識別 (若區域提供)：

- [Azure 入口網站](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure 資源管理員範本](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-app-service"></a>Azure App Service

|受控識別類型 |  全部正式推出<br>全域 Azure 區域 | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| 系統指派 | 可用 | 可用 | 可用 | 可用 |
| 使用者指派 | 預覽 | 尚未提供 | 尚未提供 | 尚未提供

請參閱下列清單來設定 Azure App Service 的受控識別 (若區域提供)：

- [Azure 入口網站](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Azure Resource Manager 範本](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-functions"></a>Azure Functions

受控識別類型 |  全部正式推出<br>全域 Azure 區域 | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| 系統指派 | 可用 | 可用 | 可用 | 可用 |
| 使用者指派 | 預覽 | 尚未提供 | 尚未提供 | 尚未提供

請參閱下列清單來設定 Azure Functions 的受控識別 (若區域提供)：

- [Azure 入口網站](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Azure Resource Manager 範本](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-logic-apps"></a>Azure Logic Apps

受控識別類型 |  全部正式推出<br>全域 Azure 區域 | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| 系統指派 | 可用 | 可用 | 可用 | 可用 |
| 使用者指派 | 尚未提供 | 尚未提供 | 尚未提供 | 尚未提供

請參閱下列清單來設定 Azure Logic Apps 的受控識別 (若區域提供)：

- [Azure 入口網站](/azure/logic-apps/create-managed-service-identity#azure-portal)
- [Azure Resource Manager 範本](/azure/app-service/overview-managed-identity#deployment-template)

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

受控識別類型 |  全部正式推出<br>全域 Azure 區域 | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| 系統指派 | 可用 | 尚未提供 | 尚未提供 | 尚未提供 |
| 使用者指派 | 尚未提供 | 尚未提供 | 尚未提供 | 尚未提供

請參閱下列清單來設定 Azure Data Factory V2 的受控識別 (若區域提供)：

- [Azure 入口網站](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-rest-api)
- [SDK](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-sdk)

### <a name="azure-api-management"></a>Azure API 管理

受控識別類型 |  全部正式推出<br>全域 Azure 區域 | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| 系統指派 | 可用 | 可用 | 尚未提供 | 尚未提供 |
| 使用者指派 | 尚未提供 | 尚未提供 | 尚未提供 | 尚未提供

請參閱下列清單來設定 Azure APIM 的受控識別 (若區域提供)：

- [Azure Resource Manager 範本](/azure/api-management/api-management-howto-use-managed-service-identity)

### <a name="azure-container-instances"></a>Azure Container Instances

受控識別類型 |  全部正式推出<br>全域 Azure 區域 | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| 系統指派 | Linux：預覽<br>Windows:尚未提供 | 尚未提供 | 尚未提供 | 尚未提供 |
| 使用者指派 | Linux：預覽<br>Windows:尚未提供 | 尚未提供 | 尚未提供 | 尚未提供

請參閱下列清單來設定 Azure 容器執行個體的受控識別 (若區域提供)：

- [Azure CLI](~/articles/container-instances/container-instances-managed-identity.md)
- [Azure Resource Manager 範本](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)


## <a name="azure-services-that-support-azure-ad-authentication"></a>支援 Azure AD 驗證的 Azure 服務

下列服務支援 Azure AD 驗證，並已經過使用 Azure 資源受控識別的用戶端服務進行的測試。

| 服務 | 資源識別碼 | 狀態 | 指派存取權 |
| ------- | ----------- | ------ | ---- | ------------- |
| Azure Resource Manager | `https://management.azure.com/` | 可用 | [Azure 入口網站](howto-assign-access-portal.md) <br>[PowerShell](howto-assign-access-powershell.md) <br>[Azure CLI](howto-assign-access-CLI.md) <br>[Azure Resource Manager 範本](../../role-based-access-control/role-assignments-template.md) |
| Azure 金鑰保存庫 | `https://vault.azure.net` | 可用 |  
| Azure Data Lake | `https://datalake.azure.net/` | 可用 |
| Azure SQL | `https://database.windows.net/` | 可用 |
| Azure 事件中心 | `https://eventhubs.azure.net` | 預覽 |
| Azure 服務匯流排 | `https://servicebus.azure.net` | 預覽 |
| Azure 儲存體 | `https://storage.azure.com/` | 預覽 |