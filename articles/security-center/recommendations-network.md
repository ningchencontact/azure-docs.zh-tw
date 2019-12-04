---
title: Azure 資訊安全中心網路的建議
description: 本文列出 Azure 資訊安全中心的安全性建議，協助您保護您的網路資源。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2019
ms.author: memildin
ms.openlocfilehash: 1b8d84286d14949c007d1ece3d089a7606464aaf
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2019
ms.locfileid: "74781965"
---
# <a name="network-recommendations---reference-guide"></a>網路建議-參考指南

本文提供您可能會在 Azure 資訊安全中心中看到有關您的網路拓撲和網際網路面向端點的完整建議清單。

如需如何找出這些專案以及如何解決這些問題的說明，請參閱[這裡](security-center-network-recommendations.md)。

## <a name="network-recommendations"></a>網路建議

|建議名稱|描述|嚴重性|安全分數|資源類型|
|----|----|----|----|----|----|
|應啟用子網層級上的網路安全性群組|啟用網路安全性群組，以控制子網中所部署資源的網路存取。|高/中|30|子網路|
|虛擬機器應該與網路安全性群組相關聯|啟用網路安全性群組以控制虛擬機器的網路存取。|高/中|30|虛擬機器|
|針對具有網際網路對向 Vm 的寬鬆網路安全性群組，應限制存取|藉由限制現有允許規則的存取，強化網際網路對應 Vm 的網路安全性群組。|高|20|虛擬機器|
|應強化 IaaS Nsg 上 web 應用程式的規則|強化執行 web 應用程式之虛擬機器的網路安全性群組（NSG），並使用對 web 應用程式埠而言過於寬鬆的 NSG 規則。|高|20|虛擬機器|
|應限制應用程式服務的存取|藉由變更網路設定來限制對應用程式服務的存取，以拒絕範圍太廣的輸入流量。|高|10|App Service|
|應關閉虛擬機器上的管理埠|強化虛擬機器的網路安全性群組，以限制對管理埠的存取。|高|10|虛擬機器|
應啟用 DDoS 保護標準|啟用 DDoS 保護服務標準，以保護包含具有公用 Ip 之應用程式的虛擬網路。 DDoS 保護可降低網路體積型和通訊協定攻擊的風險。|高|10|虛擬網路|
|應停用虛擬機器上的 IP 轉送|停用 IP 轉送。 在虛擬機器的 NIC 上啟用 IP 轉送時，機器可以接收定址到其他目的地的流量。 IP 轉送很少需要（例如，使用 VM 作為網路虛擬裝置），因此，這應該由網路安全性小組加以檢查。|中|10|虛擬機器|
|Web 應用程式應只可經由 HTTPS 存取|啟用 web 應用程式的「僅限 HTTPS」存取權。 使用 HTTPS 可確保伺服器/服務驗證，並保護傳輸中的資料免于網路層的竊聽攻擊。|中|20|Web 應用程式|
|即時網路存取控制應套用在虛擬機器上|套用即時（JIT）虛擬機器（VM）存取控制，以永久鎖定所選埠的存取權，並讓已授權的使用者只能在有限的時間內透過 JIT 開啟它們。|高|20|虛擬機器|
|函數應用程式只能透過 HTTPS 存取|啟用函數應用程式的「僅限 HTTPS」存取。 使用 HTTPS 可確保伺服器/服務驗證，並保護傳輸中的資料免于網路層的竊聽攻擊。|中|20|函式應用程式|
|應啟用儲存體帳戶的安全傳輸|啟用儲存體帳戶的安全傳輸。 安全傳輸這個選項會強制您的儲存體帳戶僅接受來自安全連線 (HTTPS) 的要求。 使用 HTTPS 可確保伺服器與服務之間的驗證，並保護傳輸中的資料免于網路層的攻擊，例如中間人攻擊、竊聽及會話劫持。|高|20|儲存體帳戶|


## <a name="next-steps"></a>後續步驟
若要深入了解適用於其他 Azure 資源類型的建議，請參閱下列文章︰

* [監視身分識別和存取](security-center-identity-access.md)
* [保護您的機器和應用程式](security-center-virtual-machine-protection.md)
* [保護您的 Azure SQL 服務](security-center-sql-service-recommendations.md)

