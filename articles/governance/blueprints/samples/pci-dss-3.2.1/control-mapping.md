---
title: 範例-PCI DSS v3.2.1 藍圖-控制項對應
description: Azure 原則和 RBAC 的支付卡產業資料安全標準 v3.2.1 blueprint （藍圖） 範例控制項對應。
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 38b1cc6249da98e11167416c8e18d06de1645679
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/03/2019
ms.locfileid: "67540938"
---
# <a name="control-mapping-of-the-pci-dss-v321-blueprint-sample"></a>PCI DSS v3.2.1 blueprint （藍圖） 範例的控制項對應

下列文件詳細說明如何將 Azure 藍圖 PCI-DSS v3.2.1 藍圖範例對應至 PCI DSS v3.2.1 控制項。 如需控制項的詳細資訊，請參閱[PCI DSS v3.2.1](https://www.pcisecuritystandards.org/documents/PCI_DSS_v3-2-1.pdf)。

下列的對應**PCI DSS v3.2.1:2018**控制項。 使用右側的導覽區可直接跳到特定的控制項對應。 許多對應的控制項都是以 [Azure 原則](../../../policy/overview.md)方案進行實作的。 若要檢閱完整方案，請在 Azure 入口網站中開啟 [原則]  ，然後選取 [定義]  頁面。 然後，尋找並選取 **[預覽]稽核 PCI v3.2.1:2018 控制及部署特定的 VM 擴充功能，以支援稽核需求**內建的原則計畫。

## <a name="132-and-134-boundary-protection"></a>1.3.2 和 1.3.4 界限保護

此藍圖可協助您管理和控制網路指派[Azure 原則](../../../policy/overview.md)監視寬鬆的規則的網路安全性群組的定義。 太寬鬆的規則可能會允許非預期的網路存取，您應加以檢閱。 此藍圖會指派一個監視未受保護的端點、 應用程式和儲存體帳戶的 Azure 原則定義。 不受防火牆保護的端點和應用程式，以及存取不受限制的儲存體帳戶，都可能允許資訊系統內所包含的資訊受到非預期的存取。

- 稽核不受限制的儲存體帳戶網路存取
- 透過網際網路面向端點的存取應該限制

## <a name="34a-41-41g-41h-and-653-cryptographic-protection"></a>3.4.a，4.1 4.1.g、 4.1.h 和 6.5.3 密碼編譯保護

此藍圖可協助您強制執行密碼編譯控制項的使用原則指派[Azure 原則](../../../policy/overview.md)定義強制執行特定的密碼編譯控制項和稽核其使用的弱式密碼編譯設定。 了解您的 Azure 資源在哪些層面可能使用非最佳化的密碼編譯組態，有助於您採取更正措施，以確保資源會根據您的資訊安全性原則進行設定。 具體而言，此藍圖所指派的原則會要求在 SQL database; 上的透明資料加密稽核儲存體帳戶和自動化帳戶的變數上遺漏加密。 另外還有哪些位址稽核儲存體帳戶、 函式應用程式、 WebApp，API 應用程式和 Redis 快取不安全的連線和稽核未加密的 Service Fabric 通訊的原則。

- 函式應用程式應只可經由 HTTPS 存取
- Web 應用程式應只可經由 HTTPS 存取
- API 應用程式應只可透過 HTTPS
- 在 Azure 資訊安全中心中監視未加密的 SQL 資料庫
- 應該在虛擬機器上套用磁碟加密
- 自動化帳戶的變數應該加密
- 應該啟用只安全連線至 Redis 快取
- 應該啟用安全傳輸至儲存體帳戶
- Service Fabric 叢集應該有 ClusterProtectionLevel 屬性設定為 EncryptAndSign
- 應該啟用 SQL database 上的透明資料加密
- 部署 SQL DB 透明資料加密

## <a name="51-62-66-and-1121-vulnerability-scanning-and-system-updates"></a>5.1、 6.2、 6.6 和 11.2.1 弱點掃描及系統更新

此藍圖可協助您指派來管理資訊系統弱點[Azure 原則](../../../policy/overview.md)監視遺漏系統更新、 作業系統弱點、 SQL 弱點，以及虛擬機器的定義Azure 資訊安全中心中的弱點。 Azure 資訊安全中心提供報告功能，可讓您即時深入檢視已部署 Azure 資源的安全性狀態。

- 在 Azure 資訊安全中心中監視缺少的 Endpoint Protection
- 適用於 Windows Server 部署預設的 Microsoft IaaSAntimalware 延伸模組
- 部署 SQL Server 上的威脅偵測
- 應該在機器上安裝系統更新
- 應該修復您的電腦上的安全性設定中的弱點
- 應該修復您的 SQL database 中的弱點
- 應該修復弱點的弱點評量解決方案

## <a name="711-712-and-713-separation-of-duties"></a>7.1.1. 7.1.3 7.1.2 和以下版本的責任區隔

只有一個 Azure 訂用帳戶擁有者時，無法建立系統管理備援。 相反地，若有過多 Azure 訂用帳戶擁有者，因擁有者帳戶遭到入侵而產生缺口的可能性就會增加。 此藍圖可協助您維護適當數目的 Azure 訂用帳戶擁有者指派[Azure 原則](../../../policy/overview.md)定義稽核的 Azure 訂用帳戶的擁有者數目。 管理訂用帳戶擁有者權限可協助您實作適當的權責區分。

- 應該有一個以上的擁有者指派給您的訂用帳戶
- 應獲指定最多 3 個擁有者的訂用帳戶 

## <a name="32-721-831a-and-831b-management-of-privileged-access-rights"></a>3.2、 7.2.1，8.3.1.a 8.3.1.b 管理特殊權限的存取權限

此藍圖可協助您限制及控制特殊權限的存取權指派[Azure 原則](../../../policy/overview.md)稽核外部帳戶的擁有者，以定義撰寫和/或讀取權限和員工帳戶擁有者及/或寫入不需要啟用多重要素驗證的權限。 Azure 會實作角色型存取控制 (RBAC)，以管理有權存取 Azure 資源的人員。 了解自訂 RBAC 規則的實作之處，有助於您確認需求和適當的實作，因為自訂的 RBAC 規則很容易發生錯誤。 這個藍圖也會指派[Azure 原則](../../../policy/overview.md)稽核的定義使用的 SQL Server 的 Azure Active Directory 驗證。 使用 Azure Active Directory 驗證簡化權限管理並集中管理的資料庫使用者與其他 Microsoft 的身分識別管理  
服務。
 
- 應該從您的訂用帳戶中移除具有擁有者權限的外部帳戶
- 應從訂用帳戶移除具有寫入權限的外部帳戶
- 應該從您的訂用帳戶中移除具有讀取權限的外部帳戶
- 應該與您的訂用帳戶的擁有者權限帳戶啟用 MFA
- MFA 應該具有寫入權限已啟用訂用帳戶的帳戶
- 應該與您的訂用帳戶的讀取權限帳戶啟用 MFA
- Azure Active Directory 系統管理員應該佈建 SQL server
- 稽核自訂 RBAC 規則的使用方式

## <a name="812-and-815-least-privilege-and-review-of-user-access-rights"></a>8.1.2 8.1.5 最少權限和檢閱使用者的存取權限

Azure 會實作角色型存取控制 (RBAC 來協助您管理可在 Azure 中的資源的存取權的人員)。 您可以使用 Azure 入口網站，檢閱可存取 Azure 資源的人員及其權限。 此藍圖指派[Azure 原則](../../../policy/overview.md)定義稽核應該優先處理供檢閱，包括折舊的帳戶和外部帳戶，以更高權限的帳戶。

- 請移除已被取代的帳戶，從您的訂用帳戶
- 應該從您訂用帳戶中移除具有擁有者權限已被取代的帳戶
- 應該從您的訂用帳戶中移除具有擁有者權限的外部帳戶
- 應從訂用帳戶移除具有寫入權限的外部帳戶
- 應該從您的訂用帳戶中移除具有讀取權限的外部帳戶

## <a name="813-removal-or-adjustment-of-access-rights"></a>8.1.3 移除或調整的存取權限

Azure 會實作角色型存取控制 (RBAC) 可協助您管理誰能夠存取在 Azure 中的資源。 使用 Azure Active Directory 與 RBAC，您可以更新使用者角色，以反映組織的變更。 必要時可以封鎖帳戶的登入 (或移除帳戶)，而立即移除對 Azure 資源的存取權限。 此藍圖指派[Azure 原則](../../../policy/overview.md)定義稽核折舊應視為要移除的帳戶。

- 請移除已被取代的帳戶，從您的訂用帳戶
- 應該從您訂用帳戶中移除具有擁有者權限已被取代的帳戶

## <a name="823ab-824ab-and-825-password-based-authentication"></a>8.2.3.a,b、 8.2.4.a,b 和 8.2.5 密碼型驗證

此藍圖可協助您指派來強制執行強式密碼[Azure 原則](../../../policy/overview.md)稽核不會強制執行最小的強度和其他密碼需求的 Windows Vm 的定義。 確知有哪些 VM 違反密碼強度原則可協助您採取更正措施，以確保所有 VM 使用者帳戶的密碼均符合原則。

- [預覽]: Audit Windows VMs that do not have a maximum password age of 70 days
- [預覽]: Deploy requirements to audit Windows VMs that do not have a maximum password age of 70 days
- [預覽]: Audit Windows VMs that do not restrict the minimum password length to 14 characters
- [預覽]: Deploy requirements to audit Windows VMs that do not restrict the minimum password length to 14 characters
- [預覽]: Audit Windows VMs that allow re-use of the previous 24 passwords
- [預覽]: Deploy requirements to audit Windows VMs that allow re-use of the previous 24 passwords

## <a name="103-and-1054-audit-generation"></a>10.3 和 10.5.4 稽核產生

此藍圖可協助您確保系統事件所記錄的指派[Azure 原則](../../../policy/overview.md)稽核記錄檔設定 Azure 資源上的定義。
診斷記錄能讓您了解 Azure 資源內所執行的作業。 Azure 記錄會依賴已同步處理的內部時鐘，來對所有資源內的事件建立與時間相互關聯的記錄。

- 監視未稽核的 SQL server，Azure 資訊安全中心
- 稽核診斷設定
- 稽核 SQL 伺服器層級稽核設定
- 部署 SQL server 上的稽核
- 儲存體帳戶應該移轉至新的 Azure Resource Manager 資源
- 虛擬機器應該移轉至新的 Azure Resource Manager 資源

## <a name="1236-and-1237-information-security"></a>12.3.6 和 12.3.7 資訊安全性

此藍圖可協助您管理及控制您的網路指派[Azure 原則](../../../policy/overview.md)稽核的可接受的網路位置和已核准的公司產品的定義允許的環境。 這些是由每個公司內的每個這些原則的原則參數透過可自訂。

- 允許的位置
- 允許的資源群組的位置

## <a name="next-steps"></a>後續步驟

既然您已檢閱過的 PCI DSS v3.2.1 blueprint （藍圖） 的控制項對應，請瀏覽下列文章以深入了解的概觀，以及如何部署此範例：

> [!div class="nextstepaction"]
> [PCI DSS v3.2.1 藍圖-概觀](./index.md)
> [PCI DSS v3.2.1 藍圖-部署步驟](./deploy.md)

## <a name="addition-articles-about-blueprints-and-how-to-use-them"></a>有關藍圖及其使用方式的其他文件：

- 了解[藍圖生命週期](../../concepts/lifecycle.md)。
- 了解如何使用[靜態與動態參數](../../concepts/parameters.md)。
- 了解如何自訂[藍圖排序順序](../../concepts/sequencing-order.md)。
- 了解如何使用[藍圖資源鎖定](../../concepts/resource-locking.md)。
- 了解如何[更新現有的指派](../../how-to/update-existing-assignments.md)。
