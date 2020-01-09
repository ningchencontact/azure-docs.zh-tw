---
title: Azure 服務層威脅偵測-Azure 資訊安全中心
description: 本主題提供 Azure 資訊安全中心中可用的 Azure 服務層警示。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: conceptual
ms.date: 08/25/2019
ms.author: memildin
ms.openlocfilehash: d4033989830323856ac14ed06eea7df74806f128
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2020
ms.locfileid: "75665700"
---
# <a name="threat-detection-for-the-azure-service-layer-in-azure-security-center"></a>Azure 資訊安全中心中的 Azure 服務層威脅偵測

本主題提供監視下列 Azure 服務層級時可用的 Azure 資訊安全中心警示：

* [Azure 網路層](#network-layer)
* [Azure 管理層（Azure Resource Manager）（預覽）](#management-layer)
* [Azure 金鑰保存庫](#azure-keyvault)

## Azure 網路層<a name="network-layer"></a>

資訊安全中心的網路層分析是以範例[IPFIX 資料](https://en.wikipedia.org/wiki/IP_Flow_Information_Export)為基礎，這是 Azure 核心路由器所收集的封包標頭。 根據此資料摘要，資訊安全中心會使用機器學習模型來識別並標示惡意的流量活動。 資訊安全中心也會使用 Microsoft 威脅情報資料庫來充實 IP 位址。

某些網路設定可能會限制資訊安全中心產生可疑網路活動的警示。 如需產生網路警示的資訊安全中心，請確定：

- 您的虛擬機器具有公用 IP 位址（或位於具有公用 IP 位址的負載平衡器上）。

- 外部識別碼解決方案不會封鎖您虛擬機器的網路輸出流量。

- 您的虛擬機器在發生可疑通訊的整個一小時，已指派相同的 IP 位址。 這也適用于在受控服務中建立的 Vm （例如 AKS、Databricks）。

如需 Azure 網路層警示的清單，請參閱[警示的參考資料表](alerts-reference.md#alerts-azurenetlayer)。

如需資訊安全中心如何使用網路相關信號來套用威脅防護的詳細資訊，請參閱[資訊安全中心中的啟發式 DNS](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/)偵測。


## Azure 管理層（Azure Resource Manager）（預覽）<a name ="management-layer"></a>

以 Azure Resource Manager 為基礎的資訊安全中心保護層目前為預覽狀態。

資訊安全中心藉由使用 Azure Resource Manager 事件來提供額外的保護層，這會被視為 Azure 的控制平面。 藉由分析 Azure Resource Manager 記錄，資訊安全中心在 Azure 訂用帳戶環境中偵測到不尋常或可能有害的作業。

如需 Azure Resource Manager （預覽）警示的清單，請參閱[警示的參考資料表](alerts-reference.md#alerts-azureresourceman)。



>[!NOTE]
> 其中幾個先前的分析是由 Microsoft Cloud App Security 提供技術支援。 若要受益于這些分析，您必須啟用 Cloud App Security 授權。 如果您有 Cloud App Security 授權，則預設會啟用這些警示。 若要停用它們：
>
> 1. 在 [**資訊安全中心**] 分頁中，選取 [**安全性原則**]。 針對您想要變更的訂用帳戶，選取 [**編輯設定**]。
> 2. 選取 [**威脅偵測**]。
> 3. 在 [**啟用**整合] 下，清除 [**允許 Microsoft Cloud App Security 存取我的資料**]，然後選取 [**儲存**]。

>[!NOTE]
>資訊安全中心會將安全性相關的客戶資料儲存在與其資源相同的地理位置。 如果 Microsoft 尚未在資源的地理位置部署資訊安全中心，則會將資料儲存在美國。 啟用 Cloud App Security 時，會根據 Cloud App Security 的地理位置規則來儲存這項資訊。 如需詳細資訊，請參閱[非區域服務的資料儲存體](https://azuredatacentermap.azurewebsites.net/)。

## Azure Key Vault （預覽）<a name="azure-keyvault"></a>

Azure Key Vault 是用來保護加密金鑰和祕密 (例如憑證、連接字串和密碼) 的雲端服務。 

Azure 資訊安全中心包含適用于 Azure Key Vault 的 Azure 原生、先進的威脅防護，提供額外一層的安全性情報。 資訊安全中心偵測到不尋常且可能有害的嘗試存取或惡意探索 Key Vault 的帳戶。 這一層保護可讓您在不是安全性專家的情況下處理威脅，而不需要管理協力廠商的安全性監視系統。  

發生異常活動時，資訊安全中心會顯示警示，並選擇性地透過電子郵件傳送給訂用帳戶管理員。 這些警示包括可疑活動的詳細資料，以及如何調查和修復威脅的建議。 

> [!NOTE]
> 此服務目前無法在 Azure 政府和主權雲端區域中使用。

如需 Azure Key Vault 警示的清單，請參閱[警示的參考資料表](alerts-reference.md#alerts-azurekv)。
