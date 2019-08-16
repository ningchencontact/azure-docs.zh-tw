---
title: 範例-PCI-DSS v 3.2.1 藍圖-控制項對應
description: 將付款卡產業資料安全標準 v 3.2.1 藍圖範例的控制項對應到 Azure 原則和 RBAC。
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: c2dbfa5f6c9d679582a1834f2ff645c5ff79c51e
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515698"
---
# <a name="control-mapping-of-the-pci-dss-v321-blueprint-sample"></a>PCI-DSS 3.2.1 藍圖範例的控制項對應

下列文章將詳細說明 Azure 藍圖 PCI-DSS v 3.2.1 藍圖範例如何對應至 PCI-DSS 的3.2.1 控制項。 如需控制項的詳細資訊, 請參閱[PCI-DSS 3.2.1](https://www.pcisecuritystandards.org/documents/PCI_DSS_v3-2-1.pdf)。

以下是**PCI-DSS 3.2.1: 2018**控制項的對應。 使用右側的導覽區可直接跳到特定的控制項對應。 許多對應的控制項都是以 [Azure 原則](../../../policy/overview.md)方案進行實作的。 若要檢閱完整方案，請在 Azure 入口網站中開啟 [原則]，然後選取 [定義] 頁面。 然後, 尋找並選取 [  **\[預覽\] ] [audit PCI v 3.2.1: 2018 控制及部署特定的 VM 延伸**模組], 以支援內建原則計畫。

## <a name="132-and-134-boundary-protection"></a>1.3.2 和1.3.4 界限保護

此藍圖藉由指派[Azure 原則](../../../policy/overview.md)定義來監視具有寬鬆規則的網路安全性群組, 協助您管理和控制網路。 太寬鬆的規則可能會允許非預期的網路存取，您應加以檢閱。 此藍圖會指派一個 Azure 原則定義來監視未受保護的端點、應用程式和儲存體帳戶。 不受防火牆保護的端點和應用程式，以及存取不受限制的儲存體帳戶，都可能允許資訊系統內所包含的資訊受到非預期的存取。

- 稽核不受限制的儲存體帳戶網路存取
- 應限制透過網際網路面向端點存取

## <a name="34a-41-41g-41h-and-653-cryptographic-protection"></a>3.4. a、4.1、4.1. g、4.1 和6.5.3 密碼編譯保護

此藍圖藉由指派[Azure 原則](../../../policy/overview.md)定義來強制執行您的原則, 以強制使用特定的如何控制項, 並使用弱式密碼編譯設定來進行審核。 了解您的 Azure 資源在哪些層面可能使用非最佳化的密碼編譯組態，有助於您採取更正措施，以確保資源會根據您的資訊安全性原則進行設定。 具體而言, 此藍圖所指派的原則需要 SQL 資料庫上的透明資料加密;在儲存體帳戶和自動化帳戶變數上審核遺失的加密。 另外還有一些原則, 可處理與儲存體帳戶、函式應用程式、WebApp、API Apps 和 Redis 快取之間的不安全連線, 以及未加密 Service Fabric 通訊的審核。

- 函數應用程式應只可經由 HTTPS 存取
- Web 應用程式應只可經由 HTTPS 存取
- API 應用程式應只可經由 HTTPS 存取
- 應在 SQL 資料庫上啟用透明資料加密
- 應在虛擬機器上套用磁碟加密
- 應加密自動化帳戶變數
- 應該只允許對 Redis Cache 的安全連線
- 應啟用儲存體帳戶的安全傳輸
- Service Fabric 叢集應將 ClusterProtectionLevel 屬性設定為 EncryptAndSign
- 應在 SQL 資料庫上啟用透明資料加密
- 部署 SQL DB 透明資料加密

## <a name="51-62-66-and-1121-vulnerability-scanning-and-system-updates"></a>5.1、6.2、6.6 和11.2.1 弱點掃描和系統更新

此藍圖藉由指派在 Azure 中監視遺失的系統更新、作業系統弱點、SQL 弱點和虛擬機器弱點的[Azure 原則](../../../policy/overview.md)定義, 協助您執行資訊系統弱點資訊安全中心。 Azure 資訊安全中心提供報告功能，可讓您即時深入檢視已部署 Azure 資源的安全性狀態。

- 在 Azure 資訊安全中心內監視缺少的 Endpoint Protection
- 為 Windows Server 部署預設 Microsoft IaaSAntimalware 延伸模組
- 在 SQL 伺服器上部署威脅偵測
- 系統更新應該安裝在您的電腦上
- 應補救您電腦上安全性設定中的弱點
- 應修復 SQL 資料庫的弱點
- 弱點評量解決方案應修復弱點

## <a name="711-712-and-713-separation-of-duties"></a>7.1.1. 7.1.2 和7.1.3 職責分離

只有一個 Azure 訂用帳戶擁有者時，無法建立系統管理備援。 相反地，若有過多 Azure 訂用帳戶擁有者，因擁有者帳戶遭到入侵而產生缺口的可能性就會增加。 此藍圖會指派[Azure 原則](../../../policy/overview.md)定義來審核 azure 訂用帳戶的擁有者數目, 以協助您維護適當數目的 azure 訂用帳戶擁有者。 管理訂用帳戶擁有者權限可協助您實作適當的權責區分。

- 應將一個以上的擁有者指派給您的訂用帳戶
- 應針對您的訂用帳戶指定最多 3 位擁有者 

## <a name="32-721-831a-and-831b-management-of-privileged-access-rights"></a>3.2、7.2.1、8.3.1 和8.3.1 許可權存取權的管理

此藍圖藉由指派[Azure 原則](../../../policy/overview.md)定義來審查外部帳戶的擁有者、寫入和/或讀取權限, 以及具有擁有者和/或寫入權限的員工帳戶, 以限制及控制特殊許可權存取權限已啟用多重要素驗證。 Azure 會實作角色型存取控制 (RBAC)，以管理有權存取 Azure 資源的人員。 了解自訂 RBAC 規則的實作之處，有助於您確認需求和適當的實作，因為自訂的 RBAC 規則很容易發生錯誤。 此藍圖也會指派[Azure 原則](../../../policy/overview.md)定義, 以審核 SQL server Azure Active Directory 驗證的使用。 使用 Azure Active Directory 驗證可簡化版權管理, 並集中管理資料庫使用者和其他 Microsoft 的身分識別  
伺服器.
 
- 具有擁有者權限的外部帳戶應該從您的訂用帳戶中移除
- 應從訂用帳戶移除具有寫入權限的外部帳戶
- 具有讀取權限的外部帳戶應該從您的訂用帳戶中移除
- 應在您訂用帳戶上具有擁有者權限的帳戶上啟用 MFA
- MFA 應啟用具有您訂用帳戶寫入權限的帳戶
- 應在您訂用帳戶上具有讀取權限的帳戶上啟用 MFA
- 應針對 SQL 伺服器佈建 Azure Active Directory 管理員
- 稽核自訂 RBAC 規則的使用方式

## <a name="812-and-815-least-privilege-and-review-of-user-access-rights"></a>8.1.2 和8.1.5 最低許可權和使用者存取權限檢查

Azure 會實行角色型存取控制 (RBAC), 以協助您管理可存取 Azure 資源的人員。 您可以使用 Azure 入口網站，檢閱可存取 Azure 資源的人員及其權限。 此藍圖會指派[Azure 原則](../../../policy/overview.md)定義來審查應優先進行審查的帳戶, 包括以較高的許可權來折舊帳戶和外部帳戶。

- 已取代帳戶應該從您的訂用帳戶中移除
- 具有擁有者權限的已取代帳戶應該從您的訂用帳戶中移除
- 具有擁有者權限的外部帳戶應該從您的訂用帳戶中移除
- 應從訂用帳戶移除具有寫入權限的外部帳戶
- 具有讀取權限的外部帳戶應該從您的訂用帳戶中移除

## <a name="813-removal-or-adjustment-of-access-rights"></a>8.1.3 移除或調整存取權限

Azure 會實行角色型存取控制 (RBAC), 以協助您管理可存取 Azure 資源的人員。 您可以使用 Azure Active Directory 和 RBAC 來更新使用者角色, 以反映組織的變更。 必要時可以封鎖帳戶的登入 (或移除帳戶)，而立即移除對 Azure 資源的存取權限。 此藍圖會指派[Azure 原則](../../../policy/overview.md)定義來審查應視為移除的折舊帳戶。

- 已取代帳戶應該從您的訂用帳戶中移除
- 具有擁有者權限的已取代帳戶應該從您的訂用帳戶中移除

## <a name="823ab-824ab-and-825-password-based-authentication"></a>8.2.3, b, 8.2.4, b, 8.2.5 密碼型驗證

此藍圖藉由指派[Azure 原則](../../../policy/overview.md)定義來協助您強制執行強式密碼, 以審核不會強制執行最小強度和其他密碼需求的 Windows vm。 確知有哪些 VM 違反密碼強度原則可協助您採取更正措施，以確保所有 VM 使用者帳戶的密碼均符合原則。

- \[預覽\]：稽核最大密碼存留期不是 70 天的 Windows VM
- \[預覽\]：部署需求以稽核最大密碼存留期不是 70 天的 Windows VM
- \[預覽\]：稽核最小密碼長度未限制為 14 個字元的 Windows 虛擬機器
- \[預覽\]：部署需求以稽核最小密碼長度未限制為 14 個字元的 Windows VM
- \[預覽\]：稽核允許重複使用前 24 個舊密碼的 Windows 虛擬機器
- \[預覽\]：部署需求以稽核允許重複使用前 24 個舊密碼的 Windows VM

## <a name="103-and-1054-audit-generation"></a>10.3 和 10.5.4 Audit 產生

此藍圖指派了 [Azure 原則](../../../policy/overview.md)定義，稽核 Azure 資源的記錄設定，以協助您確保會記錄系統事件。
診斷記錄能讓您了解 Azure 資源內所執行的作業。 Azure 記錄會依賴已同步處理的內部時鐘，來對所有資源內的事件建立與時間相互關聯的記錄。

- 應該在 SQL Server 上的 advanced data security 設定上啟用審核
- 稽核診斷設定
- 稽核 SQL 伺服器層級稽核設定
- 在 SQL 伺服器上部署稽核
- 儲存體帳戶應遷移至新的 Azure Resource Manager 資源
- 虛擬機器應該遷移至新的 Azure Resource Manager 資源

## <a name="1236-and-1237-information-security"></a>12.3.6 和12.3.7 資訊安全性

此藍圖藉由指派[Azure 原則](../../../policy/overview.md)定義來管理和控制您的網路, 以審核可接受的網路位置和環境所允許的核准公司產品。 這些可透過每個原則中的原則參數, 自訂每個公司。

- 允許的位置
- 允許資源群組的位置

## <a name="next-steps"></a>後續步驟

既然您已複習過「PCI-DSS 3.2.1」藍圖的控制項對應, 請流覽下列文章以瞭解此範例的總覽和部署方式:

> [!div class="nextstepaction"]
> [Pci-dss 3.2.1 藍圖-總覽](./index.md)
>  [pci-dss 3.2.1 藍圖-部署步驟](./deploy.md)

## <a name="addition-articles-about-blueprints-and-how-to-use-them"></a>有關藍圖及其使用方式的其他文件：

- 了解[藍圖生命週期](../../concepts/lifecycle.md)。
- 了解如何使用[靜態與動態參數](../../concepts/parameters.md)。
- 了解如何自訂[藍圖排序順序](../../concepts/sequencing-order.md)。
- 了解如何使用[藍圖資源鎖定](../../concepts/resource-locking.md)。
- 了解如何[更新現有的指派](../../how-to/update-existing-assignments.md)。
