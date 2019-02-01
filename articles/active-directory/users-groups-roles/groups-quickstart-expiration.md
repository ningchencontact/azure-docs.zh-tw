---
title: Azure Active Directory 中的 Office 365 群組到期日原則快速入門 | Microsoft Docs
description: Office 365 群組的到期日 - Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: quickstart
ms.date: 08/07/2018
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: 6d2b5201c41ba9d5c849976f0227e9abadea7658
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2019
ms.locfileid: "55165229"
---
# <a name="quickstart-set-office-365-groups-to-expire-in-azure-active-directory"></a>快速入門：設定 Office 365 群組 Azure Active Directory 中的到期日

在本快速入門中，您將設定 Office 365 群組的到期原則。 當使用者可以設定自己的群組時，未使用的群組可能會逐漸增多。 要管理未使用的群組，其中一個方式是設定這些群組的到期日，以減少手動方式刪除群組的維護作業。

到期原則十分簡單：

* 通知群組擁有者更新即將過期的群組
* 刪除未更新的群組
* 群組擁有者或 Azure AD 系統管理員可在 30 天內還原已刪除的 Office 365 群組

如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisite"></a>必要條件

您必須是租用戶中的全域管理員角色或使用者帳戶管理員，才能設定群組到期日。

## <a name="turn-on-user-creation-for-groups"></a>開啟使用者建立群組的功能

1. 使用具備目錄的全域管理員或使用者帳戶管理員身分的帳戶，登入 [Azure 入口網站](https://portal.azure.com)。

2. 選取 [群組]，然後選取 [一般]。
  
  ![自助式群組設定](./media/groups-quickstart-expiration/self-service-settings.png)

3. 將 [使用者可以建立 Office 365 群組] 設定為 [是]。

4. 完成作業後，選取 [儲存] 以儲存群組設定。

## <a name="set-group-expiration"></a>設定群組到期日

1. 連結至 [Azure 入口網站](https://portal.azure.com)、選取 [Azure Active Directory] > [群組] > [到期]，以開啟到期設定。
  
  ![到期設定](./media/groups-quickstart-expiration/expiration-settings.png)

2. 設定到期間隔。 選取預設值，或輸入 31 天以上的自訂值。 

3. 提供在群組沒有擁有者時，到期通知所應傳送到的電子郵件地址。

4. 在此快速入門中，請將 [啟用這些 Office 365 群組的到期日] 設定為 [全部]。

5. 完成作業後，選取 [儲存] 以儲存到期設定。

就這麼簡單！ 在本快速入門中，您已成功設定所選 Office 365 群組的到期原則。

## <a name="clean-up-resources"></a>清除資源

**移除到期原則**

1. 確實使用具備租用戶全域管理員身分的帳戶來登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取 [Azure Active Directory] > [群組] > [到期]。
3. 將 [啟用這些 Office 365 群組的到期日] 設定為 [無]。

**關閉使用者建立群組的功能**

1. 選取 [Azure Active Directory] > [群組] > [一般]。 
2. 將 [使用者可在 Azure 入口網站中建立 Office 365 群組] 設定為 [否]。

## <a name="next-steps"></a>後續步驟

如需關於到期的詳細資訊，包括技術限制、新增自訂封鎖字組清單，以及 Office 365 應用程式的使用者體驗，請參閱下列包含這些到期原則詳細資料的文章：

> [!div class="nextstepaction"]
> [到期原則的所有詳細資料](groups-lifecycle.md)
