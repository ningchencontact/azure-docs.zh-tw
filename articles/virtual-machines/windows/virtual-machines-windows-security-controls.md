---
title: Azure Windows 虛擬機器的安全性控制
description: 評估 Azure Windows 虛擬機器的安全性控制清單
services: virtual-machines
ms.service: virtual-machines
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: mbaldwin
ms.openlocfilehash: c4ddda164a2d8a4e2a4d106c2d7bb8bfcbae3690
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886360"
---
# <a name="security-controls-for-windows-virtual-machines"></a>Windows 虛擬機器的安全性控制項

本文記載內建在 Windows 虛擬機器中的安全性控制項。

[!INCLUDE [Security controls header](../../../includes/security-controls-header.md)]

## <a name="network"></a>網路

| 安全性控制 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 是 | |
| VNet 插入支援| 是 | |
| 網路隔離和防火牆支援| 是 |  |
| 強制通道支援| 是 | 請參閱[使用 Azure Resource Manager 部署模型設定強制通道](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm)。 |

## <a name="monitoring--logging"></a>監視 & 記錄

| 安全性控制 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 (Log analytics、App insights 等)| 是 | 請參閱[在 azure 中監視和更新 Linux 虛擬機器](/azure/virtual-machines/linux/tutorial-monitoring)和[在 azure 中監視和更新 Windows 虛擬機器](/azure/virtual-machines/windows/tutorial-monitoring)。 |
| 控制和管理平面記錄和審核| 是 |  |
| 資料平面記錄和審核 | 否 |  |

## <a name="identity"></a>身分識別

| 安全性控制 | 是/否 | 注意|
|---|---|--|
| 驗證| 是 |  |
| Authorization| 是 |  |

## <a name="data-protection"></a>資料保護

| 安全性控制 | 是/否 | 注意 |
|---|---|--|
| 待用的伺服器端加密：Microsoft 管理的金鑰 | 是 | 瞭解[如何在 Azure 中將 Linux 虛擬機器加密](/azure/virtual-machines/linux/encrypt-disks), 以及如何[加密 Windows VM 上的虛擬磁片](/azure/virtual-machines/windows/encrypt-disks)。 |
| 傳輸中的加密 (例如 ExpressRoute 加密、VNet 加密中和 VNet VNet 加密)| 是 | Azure 虛擬機器支援[ExpressRoute](/azure/expressroute)和 VNet 加密。 請參閱[Vm 中的傳輸中加密](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms)。 |
| 待用的伺服器端加密：客戶管理的金鑰（BYOK） | 是 | 客戶管理的金鑰是支援的 Azure 加密案例;請參閱[Azure 加密總覽](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms)。|
| 資料行層級加密 (Azure 資料服務)| N/A | |
| API 呼叫加密| 是 | 透過 HTTPS 和 SSL。 |



## <a name="configuration-management"></a>設定管理

| 安全性控制 | 是/否 | 注意|
|---|---|--|
| 設定管理支援 (設定的版本設定等)| 是 |  | 

## <a name="next-steps"></a>後續步驟

- 深入瞭解[跨 Azure 服務的內建安全性控制](../../security/fundamentals/security-controls.md)。
