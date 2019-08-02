---
title: 存取 Azure Container Registry 的防火牆規則
description: 設定規則, 以從防火牆後方存取 Azure container registry。
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 07/17/2019
ms.author: danlep
ms.openlocfilehash: 285d5592ce667f7b4d84f26d67a00af0698c2967
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2019
ms.locfileid: "68620957"
---
# <a name="configure-rules-to-access-an-azure-container-registry-behind-a-firewall"></a>設定規則以存取防火牆後方的 Azure container registry

本文說明如何在防火牆上設定規則, 以允許存取 Azure container registry。 例如, 位於防火牆或 proxy 伺服器後方的 Azure IoT Edge 裝置可能需要存取容器登錄來提取容器映射。 或者, 在內部部署網路中鎖定的伺服器可能需要存取以推送映射。

如果您想要將容器登錄上的輸入網路存取規則設定為只允許在 Azure 虛擬網路或公用 IP 位址範圍內進行存取, 請參閱[限制從虛擬網路存取 azure container registry](container-registry-vnet.md)。

## <a name="about-registry-endpoints"></a>關於登錄端點

若要將映射或其他成品提取或推送至 Azure container registry, 用戶端 (例如 Docker daemon) 必須透過 HTTPS 與兩個不同的端點進行互動。

* 登錄**REST API 端點**-驗證和登錄管理作業會透過登錄的公用 REST API 端點來處理。 此端點是登錄的登入伺服器 URL, 或相關聯的 IP 位址範圍。 

* **儲存體端點**-azure 會代表每個登錄來配置 azure 儲存體帳戶中的[blob 儲存體](container-registry-storage.md), 以管理容器映射和其他成品。 當用戶端存取 Azure container registry 中的映射層時, 它會使用登錄所提供的儲存體帳戶端點來提出要求。

如果您的登錄是[異地](container-registry-geo-replication.md)複寫的, 用戶端可能需要與特定區域或多個複寫區域中的 REST 和儲存體端點互動。

## <a name="allow-access-to-rest-and-storage-urls"></a>允許存取 REST 和儲存體 Url

* **REST 端點**-允許存取登錄伺服器 URL, 例如`myregistry.azurecr.io`
* **儲存體端點**-允許使用萬用字元存取所有 Azure blob 儲存體帳戶`*.blob.core.windows.net`


## <a name="allow-access-by-ip-address-range"></a>允許依 IP 位址範圍存取

如果您需要允許存取特定的 IP 位址, 請下載[AZURE IP 範圍和服務標籤–公用雲端](https://www.microsoft.com/download/details.aspx?id=56519)。

若要尋找 ACR REST 端點 IP 範圍, 請在 JSON 檔案中搜尋**AzureContainerRegistry** 。

> [!IMPORTANT]
> Azure 服務的 IP 位址範圍可能會變更, 並每週發佈更新。 定期下載 JSON 檔案, 並在您的存取規則中進行必要的更新。 如果您的案例牽涉到在 Azure 虛擬網路中設定網路安全性群組規則來存取 Azure Container Registry, 請改用**AzureContainerRegistry** [服務標記](#allow-access-by-service-tag)。
>

### <a name="rest-ip-addresses-for-all-regions"></a>所有區域的 REST IP 位址

```json
{
  "name": "AzureContainerRegistry",
  "id": "AzureContainerRegistry",
  "properties": {
    "changeNumber": 10,
    "region": "",
    "platform": "Azure",
    "systemService": "AzureContainerRegistry",
    "addressPrefixes": [
      "13.66.140.72/29",
    [...]
```

### <a name="rest-ip-addresses-for-a-specific-region"></a>特定區域的 REST IP 位址

搜尋特定的區域, 例如**AzureContainerRegistry. AustraliaEast**。

```json
{
  "name": "AzureContainerRegistry.AustraliaEast",
  "id": "AzureContainerRegistry.AustraliaEast",
  "properties": {
    "changeNumber": 1,
    "region": "australiaeast",
    "platform": "Azure",
    "systemService": "AzureContainerRegistry",
    "addressPrefixes": [
      "13.70.72.136/29",
    [...]
```

### <a name="storage-ip-addresses-for-all-regions"></a>所有區域的儲存體 IP 位址

```json
{
  "name": "Storage",
  "id": "Storage",
  "properties": {
    "changeNumber": 19,
    "region": "",
    "platform": "Azure",
    "systemService": "AzureStorage",
    "addressPrefixes": [
      "13.65.107.32/28",
    [...]
```

### <a name="storage-ip-addresses-for-specific-regions"></a>特定區域的儲存體 IP 位址

搜尋特定的區域, 例如**AustraliaCentral**。

```json
{
  "name": "Storage.AustraliaCentral",
  "id": "Storage.AustraliaCentral",
  "properties": {
    "changeNumber": 1,
    "region": "australiacentral",
    "platform": "Azure",
    "systemService": "AzureStorage",
    "addressPrefixes": [
      "52.239.216.0/23"
    [...]
```

## <a name="allow-access-by-service-tag"></a>允許依服務標記存取

在 Azure 虛擬網路中, 使用網路安全性規則來篩選來自資源 (例如虛擬機器) 至容器登錄的流量。 若要簡化 Azure 網路規則的建立, 請使用**AzureContainerRegistry** [服務標記](../virtual-network/security-overview.md#service-tags)。 服務標籤代表一組 IP 位址首碼, 可存取全域或每個 Azure 區域的 Azure 服務。 當地址變更時, 會自動更新標記。 

例如, 建立具有目的地**AzureContainerRegistry**的輸出網路安全性群組規則, 以允許 Azure container registry 的流量。 若只要在特定區域中允許存取服務標籤, 請以下列格式指定區域:**AzureContainerRegistry**。[*區功能變數名稱稱*]。

## <a name="next-steps"></a>後續步驟

* 瞭解[適用于網路安全性的 Azure 最佳作法](../security/azure-security-network-security-best-practices.md)

* 深入瞭解 Azure 虛擬網路中的[安全性群組](https://docs.microsoft.com/en-us/azure/virtual-network/security-overview.md)



<!-- IMAGES -->

<!-- LINKS - External -->

<!-- LINKS - Internal -->

