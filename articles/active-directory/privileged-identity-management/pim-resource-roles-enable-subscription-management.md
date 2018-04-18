---
title: 適用於 Azure 資源的 Privileged Identity Management - 啟用訂用帳戶管理 | Microsoft Docs
description: 了解全域管理員如何管理租用戶中的訂用帳戶。
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/27/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 4c6ae3da34fe5157314b8ea422591f7ecbd2a667
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2018
---
# <a name="enable-subscription-management"></a>啟用訂用帳戶管理

身為您目錄的全域管理員，您可能沒有您租用戶中所有訂用帳戶資源的預設存取權。 本文將概述取得您租用戶中所有訂用帳戶存取權的步驟，以及在收到存取權之後，繼續符合貴組織所需任何安全性控制的建議方法。

## <a name="who-can-enable-management-of-subscriptions-in-my-directory"></a>有誰可以啟用我目錄中訂用帳戶的管理？

指派給全域管理員角色的每位使用者都必須遵循下列步驟來啟用訂用帳戶管理。 一旦您自行啟用了訂用帳戶管理，您就可以新增其他也可能需要資源存取權的全域管理員。 沒有任何目錄設定可為全域管理員角色的所有成員啟用存取權。

## <a name="log-on-to-the-azure-portal"></a>登入 Azure 入口網站

首先，使用屬於全域管理員角色合格或有效成員的帳戶來登入 Azure 入口網站。 如果此帳戶是合格的全域管理員，您必須先啟用該角色，再繼續下一個步驟。

## <a name="access-the-azure-ad-admin-center"></a>存取 Azure AD 系統管理中心

既然您已使用全域管理員身分登入 Azure 入口網站，您可以編輯將提供 Azure 訂用帳戶存取權的設定。 巡覽至 Azure AD 系統管理中心，找出並選取左導覽功能表中的 [屬性] 索引標籤。

![](media/azure-pim-resource-rbac/aad_properties.png)

在屬性清單中，將 [全域管理員可管理 Azure 訂用帳戶] 選項切換為 [是]。

![](media/azure-pim-resource-rbac/aad_properties_save.png)

## <a name="navigate-to-azure-ad-pim"></a>巡覽至 Azure AD PIM

啟用此選項後，您的帳戶會自動新增至租用戶中每個訂用帳戶資源的「使用者存取權管理員」角色。 從這裡巡覽至 Azure AD PIM，然後在左導覽功能表的 [管理] 區段下選取 Azure 資源。

![](media/azure-pim-resource-rbac/aadpim_manage_azure_resources.png)

## <a name="manage-and-discover-resources"></a>管理及探索資源

如果貴組織已經使用 Azure AD PIM 來保護 Azure Active Directory 中的管理員，您會在刀鋒視窗載入時看到訂用帳戶清單。

![](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

> [!NOTE]
> 如果看不到任何資源，請檢查：
>- 您的全域管理員角色並未過期 
>- 您的組織具有 Azure 訂用帳戶

![](media/azure-pim-resource-rbac/aadpim_rbac_empty_resource_list.png)

## <a name="configure-assignments"></a>設定指派

選取清單中的訂用帳戶，然後將您自己 (或您所隸屬的群組) 指派為資源的合格擁有者。 
[深入了解指派角色](pim-resource-roles-assign-roles.md)。

繼續下一個步驟前，請針對每個資源重複此程序。

## <a name="clean-up-standing-access"></a>清除常設存取權

既然您具有貴組織中重要訂用帳戶的合格指派，您可藉由停用目錄屬性中的選項來清除常設存取權：

![](media/azure-pim-resource-rbac/aad_properties_no.png)

## <a name="next-steps"></a>後續步驟

[探索資源](pim-resource-roles-discover-resources.md)

[配置角色設定](pim-resource-roles-configure-role-settings.md)








