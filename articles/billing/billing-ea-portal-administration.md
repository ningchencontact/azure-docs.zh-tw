---
title: Azure EA 入口網站系統管理員
description: 本文將說明系統管理員在 Azure EA 入口網站中完成的一般工作。
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/09/2019
ms.topic: conceptual
ms.service: billing
manager: boalcsva
ms.openlocfilehash: 7b594cef2fefa164ef900cbfd65fcf95d5d47b94
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2019
ms.locfileid: "70901129"
---
# <a name="azure-ea-portal-administration"></a>Azure EA 入口網站系統管理員

本文將說明系統管理員在 Azure EA 入口網站 (https://ea.azure.com) 中完成的一般工作。 Azure EA 入口網站是一個線上管理入口網站，可協助客戶管理其 Azure EA 服務的成本。 如需 Azure EA 入口網站的簡介資訊，請參閱[開始使用 Azure EA 入口網站](billing-ea-portal-get-started.md)一文。

## <a name="add-a-new-enterprise-administrator"></a>新增企業系統管理員

在管理 Azure EA 註冊上，企業系統管理員擁有最多權限。 設定 EA 合約時會建立最初的 Azure EA 系統管理員。 不過，您可以隨時新增或移除系統管理員。 新的系統管理員只會由現有的系統管理員新增。 如需新增其他企業系統管理員的詳細資訊，請參閱[建立另一個企業系統管理員](billing-ea-portal-get-started.md#create-another-enterprise-admin)。如需有關帳單設定檔角色和工作的詳細資訊，請參閱[帳單設定檔角色和工作](billing-understand-mca-roles.md#billing-profile-roles-and-tasks)。

## <a name="update-user-state-from-pending-to-active"></a>將使用者狀態從擱置更新為作用中

當新的帳戶擁有者 (AO) 第一次新增至 Azure EA 註冊時，其狀態會顯示為 [擱置]  。 當新的帳戶擁有者收到用於啟用的歡迎電子郵件時，他們就可以登入來啟用其帳戶。 當他們啟用帳戶時，帳戶狀態會從 [擱置]  更新為 [作用中]  。 系統可能會提示新使用者輸入其名字和姓氏，以建立商務帳戶。 若是如此，他們必須新增必要的資訊才能繼續，然後帳戶才會啟用。

## <a name="add-a-department-admin"></a>新增部門系統管理員

Azure EA 系統管理員建立部門之後，Azure 企業系統管理員就可以新增部門系統管理員，並將其與部門產生關聯。 部門系統管理員可以建立新的帳戶。 必須有新帳戶，才能建立 Azure EA 訂用帳戶。

如需有關新增部門的詳細資訊，請參閱＜建立 Azure EA 部門＞。

## <a name="enterprise-user-roles"></a>企業使用者角色

Azure EA 入口網站可協助您管理您的 Azure EA 成本和使用量。 Azure EA 入口網站中有三個主要角色：

- EA 系統管理員
- 部門系統管理員
- 帳戶擁有者

每個角色都有不同層級的存取權和授權。

如需有關使用者角色的詳細資訊，請參閱＜企業使用者角色＞。

## <a name="add-an-azure-ea-account"></a>新增 Azure EA 帳戶

Azure EA 帳戶是 Azure EA 入口網站中用來管理訂用帳戶的組織單位，並且也會用於報告。 若要存取及使用 Azure 服務，您需要建立一個帳戶或讓人為您建立一個帳戶。

如需有關 Azure 帳戶的詳細資訊，請參閱＜新增帳戶＞。

## <a name="transfer-an-enterprise-account-to-a-new-enrollment"></a>將企業帳戶轉移至新的註冊

當您將企業帳戶轉移至新的註冊時，請記住下列幾點：

- 只會轉移要求中指定的帳戶。 如果選擇所有帳戶，則會轉移全部的帳戶。
- 來源註冊會將其狀態保持為 [作用中] 或 [已延長]。 您可以繼續使用註冊，直到到期為止。

### <a name="effective-transfer-date"></a>有效的轉移日期

有效的轉移日期可以是目標註冊的開始日期或之後的日期。 作為轉移目的地的註冊就是「目標註冊」  。 帳戶轉移之後，在有效轉移日期之前，帳戶中的所有使用量資訊都會保留在轉移的來源註冊中。 作為轉移來源的註冊就是「來源註冊」  。  來源註冊使用量會依承諾用量或超額部分收費。 在有效轉移日期之後發生的使用量會轉移至新的註冊，並據以收費。

帳戶轉移最遠可以追溯到目標註冊的開始日期。 或者，追溯到來源註冊的有效開始日期。

### <a name="monetary-commitment"></a>承諾用量

預付金無法在註冊之間轉移。 預付金餘額會依約繫結至收訂的註冊。 預付金不會在帳戶或註冊轉移程序中轉移。

### <a name="services-affected"></a>受影響的服務

帳戶轉移期間不會有任何停機時間。 如果已提供所有必要的資訊，此作業可以在提出要求的同一天完成。

### <a name="prerequisites"></a>必要條件

當您要求帳戶轉移時，請提供下列資訊：


- 要轉移的帳戶名稱和帳戶擁有者識別碼
- 針對來源註冊，須提供要轉移的註冊號碼和帳戶
- 針對目標註冊，須提供轉移目的地的註冊號碼和帳戶
- 針對帳戶轉移有效日期，可以是目標註冊開始日期或其之後的日期

帳戶轉移之前要記住的其他幾點：

- 目標和來源註冊都需要 EA 系統管理員的核准
  - 在某些情況下，Microsoft 可能會向來源註冊的 EA 系統管理員要求額外核准
- 如果帳戶轉移不符合您的需求，請考慮註冊轉移。
- 帳戶轉移會轉移所有服務、訂用帳戶、帳戶、部門和整個註冊結構，包括所有 EA 部門系統管理員。
- 帳戶轉移會將來源註冊狀態設定為 [已轉移]  。 已轉移的帳戶僅限用於報告使用量記錄。
- 您無法將角色或訂用帳戶新增至狀態為已轉移的註冊。 該狀態可防止以該註冊產生其他使用量。
- 來源合約中任何剩餘的預付金餘額都會遺失，其中也包括未來的期限。


## <a name="transfer-enterprise-enrollment-to-a-new-one"></a>將企業註冊轉移至新的註冊

當您要求將整個企業註冊轉移至另一個註冊時，會發生下列動作：

- 所有 Azure 服務、訂用帳戶、帳戶、部門和整個註冊結構，包括所有 EA 部門系統管理員都會轉移。
- 註冊狀態會設定為 [已轉移]  。 已轉移的註冊僅限用於報告使用量記錄。
- 您無法將角色或訂用帳戶新增至已轉移的註冊。 已轉移狀態可防止以該註冊產生其他使用量。
- 合約中任何剩餘的預付金餘額都會遺失，其中也包括未來的期限。

### <a name="effective-transfer-date"></a>有效的轉移日期

有效的轉移日期可以是要轉移至目標註冊的來源註冊開始日期或之後的日期。

來源註冊使用量會依承諾用量或超額部分收費。 在有效轉移日期之後發生的使用量會轉移至新的註冊，並據以收費。

### <a name="effective-transfer-date-in-the-past"></a>過去的有效轉移日期

帳戶轉移最遠可以追溯到目標註冊的開始日期。 或者，追溯到來源註冊的有效開始日期。

### <a name="monetary-commitment"></a>承諾用量

預付金無法在註冊之間轉移。 預付金餘額會依約繫結至收訂的註冊。 預付金不會在帳戶或註冊轉移程序中轉移。

### <a name="services-affected"></a>受影響的服務

帳戶轉移期間不會有任何停機時間。 如果已提供所有必要的資訊，此作業可以在提出要求的同一天完成。

### <a name="prerequisites"></a>必要條件

當您要求註冊轉移時，請提供下列資訊：

- 針對來源註冊，須提供要轉移的註冊號碼和帳戶
- 針對目標註冊，須提供轉移目的地的註冊號碼和帳戶
- 針對註冊轉移有效日期，可以是目標註冊開始日期或其之後的日期。 選擇的日期無法影響已發出之超額發票的使用量。

註冊轉移之前要記住的其他幾點：

- 目標和來源註冊都需要 EA 系統管理員的核准
  - 在某些情況下，Microsoft 可能會向來源註冊的 EA 系統管理員要求額外核准
- 如果註冊轉移不符合您的需求，請考慮帳戶轉移。
- 只有您指定的帳戶會轉移。 您可以要求轉移所有帳戶。
- 來源註冊會將其狀態保持為 [作用中]/[已擴充]。 您可以繼續使用註冊，直到到期為止。

## <a name="change-account-owner"></a>變更帳戶擁有者

Azure EA 入口網站可以將訂用帳戶從一個帳戶擁有者轉移至另一個帳戶擁有者。 如需詳細資訊，請參閱[變更帳戶擁有者](billing-ea-portal-get-started.md#change-account-owner)。

## <a name="subscription-transfer-effects"></a>訂用帳戶轉移效果

當 Azure 訂用帳戶轉移到相同 Azure Active Directory 租用戶中的帳戶時，具有[角色型存取控制 (RBAC)](../role-based-access-control/overview.md) 可管理資源的所有使用者、群組和服務主體都會保留其存取權。

若要檢視訂用帳戶上具有 RBAC 存取權的使用者：

1. 在 Azure 入口網站中，開啟**訂用帳戶**。
2. 選取要檢視的訂用帳戶，然後選取 [存取控制 (IAM)]  。
3. 選取 [角色指派]  。 [角色指派] 頁面會列出在訂用帳戶上具有 RBAC 存取權的所有使用者。

如果訂用帳戶轉移到不同 Azure AD 租用戶中的帳戶，則具有 [RBAC](../role-based-access-control/overview.md) 可管理資源的所有使用者、群組和服務主體都會「失去」  其存取權。 雖然 RBAC 存取不存在，但您可以透過安全性機制取得訂用帳戶的存取權，包括：

- 可將使用者管理權限授與給訂用帳戶資源的管理憑證。 如需詳細資訊，請參閱[建立和上傳 Azure 的管理憑證](../cloud-services/cloud-services-certs-create.md)。
- 服務 (例如儲存體) 的存取金鑰。 如需詳細資訊，請參閱 [Azure 儲存體帳戶概觀](../storage/common/storage-account-overview.md)。
- 服務 (例如 Azure 虛擬機器) 的遠端存取認證。

如果接受者需要限制對其 Azure 資源的存取權，則應考慮更新所有與服務相關聯的密碼。 您可以使用下列步驟來更新大部分的資源：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 在 [中樞] 功能表中，選取 [所有資源]  。
3. 選取資源。
4. 在資源頁面上按一下 [設定]  ，即可檢視並更新現有的秘密。



## <a name="close-an-azure-enterprise-enrollment"></a>關閉 Azure 企業註冊

如果您想要關閉 Azure EA 註冊，您可以：

- 在 Azure 入口網站上取消與您 Azure EA 相關聯的所有訂用帳戶。
- 洽詢您的客戶軟體顧問或合作夥伴，並要求他們關閉您的 Azure Enterprise 合約。

## <a name="update-notification-settings"></a>更新通知設定

企業系統管理員會自動註冊，以接收與其註冊相關聯的使用通知。 每個企業系統管理員都可以變更個別通知的間隔，或完全關閉這些通知。

通知連絡人會顯示在 Azure EA 入口網站中的 [通知連絡人]  區段。 管理通知連絡人可確保組織中的適當人員會收到 Azure EA 通知。

若要檢視目前的通知設定：

1. 在 Azure EA 入口網站中，瀏覽至 [管理]   > [通知連絡人]  。
2. 電子郵件地址 – 可接收通知且與企業系統管理員的 Microsoft 帳戶、公司或學校帳戶相關聯的電子郵件地址。
3. 未開立帳單餘額通知頻率 – 傳送給每位個別企業系統管理員的通知頻率。

若要新增連絡人：

1. 按一下 [+ 新增連絡人]  。
2. 輸入電子郵件地址，然後確認。
3. 按一下 [檔案]  。

新的通知連絡人會顯示在 [通知連絡人]  區段中。 若要變更通知頻率，請選取通知連絡人，然後按一下所選資料列右側的鉛筆符號。 將頻率設定為**每日**、**每週**、**每月**或**無**。

您可以隱藏「逼近範圍期間的結束日期」  及「停用和取消佈建日期逼近」  的生命週期通知。 停用生命週期通知會隱藏範圍期間和合約結束日期的相關通知。

## <a name="manage-partner-notifications"></a>管理合作夥伴通知

合作夥伴系統管理員可以管理接收其註冊使用通知的頻率。 他們會自動收到未開立帳單餘額的每週通知。 他們可以將個別通知的頻率變更為每月、每週、每日，或完全關閉。

如果使用者未收到通知，請使用下列步驟來確認使用者的通知設定是否正確。

1. 以合作夥伴系統管理員身分登入 Azure EA 入口網站。
2. 按一下 [管理]  ，然後按一下 [合作夥伴]  索引標籤。
3. 在 [系統管理員]  區段下，檢視系統管理員清單。
4. 若要編輯通知喜好設定，請將滑鼠停留在適當的系統管理員上，然後按一下鉛筆符號。
5. 視需要設定通知頻率和生命週期通知。
6. 視需要新增連絡人，然後按一下 [新增]  。
7. 按一下 [檔案]  。

![顯示新增連絡人以加入連絡人的範例 ](./media/billing-ea-portal-administration/create-ea-manage-partner-notification.png)

## <a name="azure-sponsorship-offer"></a>Azure 贊助供應項目
Azure 贊助供應項目是有贊助限制的 Microsoft Azure 帳戶。 只有 Microsoft 所選的限定客戶，才能收到電子郵件邀請。 若您獲選參加 Microsoft Azure 贊助供應項目，您將會收到屬於您帳戶識別碼的電子郵件邀請。
如需詳細資訊，請參閱

- 贊助供應項目概觀 - https://azure.microsoft.com/en-us/offers/ms-azr-0143p/
- 贊助餘額入口網站 - https://www.microsoftazuresponsorships.com/balance  
- 贊助外部常見問題集 - https://azuresponsorships-staging.azurewebsites.net/faq
- 贊助啟用的支援要求 - http://aka.ms/azrsponsorship

## <a name="next-steps"></a>後續步驟
- 了解[虛擬機器保留](billing-ea-portal-vm-reservations.md)如何協助您節省成本。
- 如果您需要 Azure EA 入口網站問題的疑難排解協助，請參閱[針對 Azure EA 入口網站的存取進行疑難排解](billing-ea-portal-troubleshoot.md)。
