---
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 07/10/2019
ms.author: mbaldwin
ms.openlocfilehash: df11493fa9663d3fcbf0a2f74a5acbead55a25fb
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67800272"
---
## <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 靜態加密 （例如伺服器端加密，使用客戶管理的金鑰，伺服器端加密和其他加密功能） | 是 | 請參閱[如何加密 Azure 中的 Linux 虛擬機器](/azure/virtual-machines/linux/encrypt-disks.md)並[加密 Windows VM 上的虛擬磁碟](/azure/virtual-machines/windows/encrypt-disks.md)。 |
| （例如 ExpressRoute 加密，在 VNet 加密和 VNet 對 VNet 加密） 的傳輸中加密| 是 | Azure 虛擬機器支援[ExpressRoute](/azure/expressroute)和 VNET 加密。 請參閱[中的傳輸中的加密 Vm](/azure/security/security-azure-encryption-overview.md#in-transit-encryption-in-vms)。 |
| 加密金鑰處理 （CMK、 BYOK）| 是 | 客戶管理的金鑰是受支援的 Azure 加密案例;請參閱[Azure 加密概觀](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms.md)。|
| 資料行層級加密 (Azure Data Services)| N/A | |
| API 呼叫加密| 是 | 透過 HTTPS 和 SSL。 |

## <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 是 | |
| VNet 插入支援| 是 | 。 |
| 網路隔離和 Firewalling 支援| 是 |  |
| 強制通道的支援| 是 | 請參閱[設定強制通道使用 Azure Resource Manager 部署模型](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm.md)。 |

## <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 （Log analytics、 App insights）| 是 | 請參閱[監視和更新 Azure 中的 Linux 虛擬機器](/azure/virtual-machines/linux/tutorial-monitoring.md)並[監視和更新 Azure 中的 Windows 虛擬機器](/azure/virtual-machines/windows/tutorial-monitoring.md)。 |

## <a name="identity-and-access-management"></a>身分識別和存取管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 驗證| 是 |  |
| Authorization| 是 |  |


## <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制和管理平面記錄與稽核| 是 |  |
| 資料平面記錄與稽核 | 否 |  |

## <a name="configuration-management"></a>設定管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 組態管理支援 （版本設定的組態等）。| 是 |  | 
