---
title: Azure Stack 中設定檔所支援的資源提供者 API 版本 | Microsoft Docs
description: 了解 Azure Stack 中設定檔所支援的 Azure Resource Manager 版本。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: db01df21c95ee41197344cec719f1c2ab2dfc2ed
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2018
---
# <a name="resource-provider-api-versions-supported-by-profiles-in-azure-stack"></a>Azure Stack 中設定檔所支援的資源提供者 API 版本

Azure 資源提供者會提供可透過 Azure Resource Manager 來部署及管理的資源。 每個提供者皆會提供可與資源搭配運作的作業。 一些常見的資源提供者包括 Microsoft.Compute (提供虛擬機器)、Microsoft.Storage (提供儲存體帳戶資源) 及 Microsoft.Web (提供與 Web 應用程式相關的資源)。 如需詳細資訊，請參閱[資源提供者和類型](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services)。

以下是每個資源提供者的表格，當中指出使用設定檔時，針對 Azure Stack 支援的 API 版本。

### <a name="microsoftauthorization"></a>Microsoft.Authorization

您需使用角色型存取控制，來管理您組織內使用者可對資源進行的動作。 這一組作業可讓您定義角色、將角色指派給使用者或群組，以及取得權限的相關資訊。 如需詳細資訊，請參閱[授權](https://docs.microsoft.com/rest/api/authorization/)。

| 資源類型 | API 版本 |
|---------------------|--------------------|
| 鎖定 | 2017-04-01 |
| 作業 | 2015-07-01 |
| 權限 | 2015-07-01 |
| 原則指派 | 2016-12-01 (2017-06-01-preview) |
| 原則定義 | 2016-12-01 |
| 提供者作業 | 2015-07-01-preview |
| 角色指派 | 2015-07-01 |
| 角色定義 | 2015-07-01 |

### <a name="microsoftcommerce"></a>Microsoft.Commerce

| 資源類型 | API 版本 |
|----------------------------------|----------------------|
| 委派的提供者訂用帳戶 | 2015-06-01 - preview |
| 委派的使用量彙總 | 2015-06-01 - preview |
| 預估資源支出 | 2015-06-01 – preview |
| 作業 | 2015-06-01 - preview |
| 訂閱者使用量彙總 | 2015-06-01 - preview |
| 使用量彙總 | 2015-06-01 - preview |

### <a name="microsoftcompute"></a>Microsoft.Compute

「Azure 計算 API」可讓您透過程式設計方式，存取虛擬機器及存取支援虛擬機器的資源。 如需詳細資訊，請參閱 [Azure 計算](https://docs.microsoft.com/rest/api/compute/)。

| 資源類型 | API 版本 |
|---------------------------------------------------------------|-------------|
| 可用性設定組 (Availability Sets) | 2016-03-30 |
| 位置 | 2016-03-30 |
| 位置/作業 | 2016-03-30 |
| 位置/發行者 | 2016-03-30 |
| 位置/使用量 | 2016-03-30 |
| 位置/VM 大小 | 2016-03-30 |
| 作業 | 2016-03-30 |
| 虛擬機器 | 2016-03-30 |
| 虛擬機器/延伸模組 | 2016-03-30 |
| 虛擬機器擴展集 | 2016-03-30 |
| 虛擬機器擴展集/延伸模組 | 2016-03-30 |
| 虛擬機器擴展集/網路介面 | 2016-03-30 |
| 虛擬機器擴展集/虛擬機器 | 2016-03-30 |
| 虛擬機器擴展集/虛擬機器/網路介面 | 2016-03-30 |

### <a name="microsoftgallery"></a>Microsoft.Gallery

| 資源類型 | API 版本 |
|------------------|-------------|
| 鑑藏 | 2015-04-01 |
| 鑑藏內容 | 2015-04-01 |
| 鑑藏解壓縮 | 2015-04-01 |
| 圖庫項目 | 2015-04-01 |
| 作業 | 2015-04-01 |
| 入口網站 | 2015-04-01 |
| Search | 2015-04-01 |
| 建議 | 2015-04-01 |

### <a name="microsoftinsights"></a>Microsoft.Insights

| 資源類型 | API 版本 |
|--------------------|--------------------|
| 警示規則 | 2016-03-01 |
| 事件類別 | 2017-03-01-preview |
| 事件類型 | 2017-03-01-preview |
| 計量定義 | 2016-03-01 |
| 作業 | 2015-04-01 |

### <a name="microsoftkeyvault"></a>Microsoft.KeyVault

管理金鑰保存庫，以及金鑰保存庫內的金鑰、祕密和憑證。 如需詳細資訊，請參閱 [Azure Key Vault REST API 參考](https://docs.microsoft.com/rest/api/keyvault/) \(英文\)。

| 資源類型 | API 版本 |
|-------------------------|--------------|
| 作業 | 2016-10-01 |
| 保存庫 | 2016-10-01 |
| 保存庫/存取原則 | 2016-10-01 |
| 保存庫/祕密 | 2016-10-01 |

### <a name="microsoftkeyvaultadmin"></a>Microsoft.Keyvault.Admin

管理金鑰保存庫，以及金鑰保存庫內的金鑰、祕密和憑證。 如需詳細資訊，請參閱 [Azure Key Vault REST API 參考](https://docs.microsoft.com/rest/api/keyvault/) \(英文\)。

| 資源類型 | API 版本 |
|------------------|--------------------|
| 位置 | 2017-02-01-preview |
| 位置/配額 | 2017-02-01-preview |

### <a name="microsoftnetwork"></a>Microsoft.Network

作業呼叫結果所呈現的是可用的網路雲端作業清單。 如需詳細資訊，請參閱 [作業 REST API](https://docs.microsoft.com/rest/api/operation/) \(英文\)。

| 資源類型 | API 版本 |
|---------------------------|--------------|
| 連線 | 2015-06-15 |
| DNS 區域 | 2016-04-01 |
| 負載平衡器 | 2015-06-15 |
| 區域網路閘道 | 2015-06-15 |
| 位置 | 2016-04-01 |
| 位置/作業結果 | 2016-04-01 |
| 位置/作業 | 2016-04-01 |
| 位置/使用量 | 2016-04-01 |
| 網路介面 | 2015-06-15 |
| 網路安全性群組 | 2015-06-15 |
| 作業 | 2015-06-15 |
| 公用 IP 位址 | 2015-06-15 |
| 路由表 | 2015-06-15 |
| 虛擬網路閘道 | 2015-06-15 |
| 虛擬網路 | 2015-06-15 |

### <a name="microsoftresources"></a>Microsoft.Resources

Azure Resource Manager 可讓您部署和管理 Azure 解決方案的基礎結構。 您可組織資源群組中的相關資源，並使用 JSON 範本部署資源。 如需有關使用 Resource Manager 來部署和管理資源的簡介，請參閱 [Azure Resource Manager 概觀](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)。

| 資源類型 | API 版本 |
|-----------------------------------------|-------------------|
| 應用程式註冊 | 2015-01-01 |
| 檢查資源名稱 | 2015-012016-09-01 |
| 委派的提供者 | 2015-01-01 |
| 委派的提供者/提供項目 | 2015-01-01 |
| 委派的提供者/提供項目/預估價格 | 2015-01-01 |
| 部署 | 2016-0209-01 |
| 部署/作業 | 2016-0209-01 |
| 延伸模組中繼資料 | 2015-01-01 |
| 連結 | 2015-012016-09-01 |
| 位置 | 2015-01-01 |
| 優惠 | 2015-01-01 |
| 作業 | 2015-01-01 |
| 提供者 | 2015-012017-08-01 |
| 資源群組 | 2015-012016-09-01 |
| 資源 | 2015-012016-09-01 |
| 訂用帳戶 | 2015-012016-09-01 |
| 訂用帳戶/位置 | 2015-012016-09-01 |
| 訂用帳戶/作業結果 | 2015-012016-09-01 |
| 訂用帳戶/提供者 | 2015-012017-08-01 |
| 訂用帳戶/資源群組 | 2015-012016-09-01 |
| 訂用帳戶/資源群組/資源 | 2015-012016-09-01 |
| 訂用帳戶/資源 | 2015-012016-09-01 |
| 訂用帳戶/標記名稱 | 2016-0609-01 |
| 訂用帳戶/標記名稱/標記值 | 2016-0609-01 |
| 租用戶 | 2015-012017-08-01 |

### <a name="microsoftstorage"></a>Microsoft.Storage 

「儲存體資源提供者」(SRP) 可讓您透過程式設計方式管理儲存體帳戶和金鑰。 如需詳細資訊，請參閱 [Azure 儲存體資源提供者 REST API 參考](https://docs.microsoft.com/rest/api/storagerp/) \(英文\)。

| 資源類型 | API 版本 |
|-------------------------|--------------|
| 檢查名稱可用性 | 2016-01-01 |
| 位置 | 2016-01-01 |
| 位置/配額 | 2016-01-01 |
| 作業 | 2016-01-01 |
| 儲存體帳戶 | 2016-01-01 |
| 使用方式 | 2016-01-01 |

## <a name="next-steps"></a>後續步驟

* [安裝 Azure Stack 適用的 PowerShell](azure-stack-powershell-install.md)
* [設定 Azure Stack 使用者的 PowerShell 環境](azure-stack-powershell-configure-user.md)  
