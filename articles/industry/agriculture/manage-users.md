---
title: 管理使用者
description: 本文說明如何管理使用者。
author: uhabiba04
ms.topic: article
ms.date: 12/02/2019
ms.author: v-umha
ms.openlocfilehash: f58db00e598fd08972adb60cbd8aae0d3bf3391d
ms.sourcegitcommit: b5ff5abd7a82eaf3a1df883c4247e11cdfe38c19
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2019
ms.locfileid: "74942291"
---
# <a name="manage-users"></a>管理使用者

Azure FarmBeats 包含屬於您的 Azure Active Directory （Azure AD）之人員的使用者管理。 您可以將使用者新增至 Azure FarmBeats 實例，以存取 Api、查看產生的對應，以及來自伺服器陣列的感應器遙測。

## <a name="prerequisites"></a>必要條件

- 需要 Azure FarmBeats 部署。 若要深入瞭解如何設定 Azure FarmBeats，請參閱[部署 FarmBeats](prepare-for-deployment.md) 。
- 您想要在 Azure FarmBeats 實例中新增或移除之使用者的電子郵件識別碼。

## <a name="manage-azure-farmbeats-users"></a>管理 Azure FarmBeats 使用者

Azure FarmBeats 會使用 Azure AD 進行驗證、存取控制和角色。 Azure AD 租使用者中的使用者可以新增為 Azure FarmBeats 中的使用者。

> [!NOTE]
> 如果您嘗試新增的使用者不存在於 Azure AD 租使用者中，請遵循**新增 Azure AD 使用者**一節中的指示來完成安裝，然後再繼續將其設定為 Azure FarmBeats 使用者。

**角色**

Azure FarmBeats 目前支援兩種類型的使用者角色：

 - 系統**管理員**-所有 Azure FarmBeats 資料中樞 api 的存取權。 此角色中的使用者可以查詢所有 Azure FarmBeats 資料中樞物件，並從 FarmBeats 加速器執行所有作業。
 - **唯讀**： FarmBeats 資料中樞 api 的唯讀存取權。 使用者可以查看資料中樞 Api、加速器儀表板和地圖。 具有「唯讀」角色的使用者將無法執行任何作業，例如產生對應、關聯裝置或建立伺服器陣列。


## <a name="add-user-to-azure-farmbeats"></a>將使用者新增至 Azure FarmBeats

遵循下列步驟，將使用者新增至 Azure FarmBeats：

1.  登入加速器，然後選取 [**設定**] 圖示。
2.  選取 [**存取控制**]。

    ![專案伺服器陣列的節拍](./media/create-farms/settings-users-1.png)

3.  輸入您想要提供存取權之使用者的電子郵件識別碼。
4.  選取所需的角色– [系統管理員] 或 [唯讀]。
5.  選取 [**新增角色**]。

新增的使用者現在將能夠存取 Azure FarmBeats （資料中樞和加速器）。

## <a name="delete-user-from-azure-farmbeats"></a>從 Azure FarmBeats 刪除使用者

請遵循下列步驟，從 Azure FarmBeats 系統中移除使用者：

1.  登入加速器，然後選取 [**設定**] 圖示。
2.  選取 [**存取控制**]。
3.  選取 [**刪除**] 以刪除使用者。

使用者已從系統中刪除。 您會收到下列訊息，以確認操作成功。

![專案伺服器陣列的節拍](./media/create-farms/manage-users-2.png)

## <a name="add-azure-ad-users"></a>新增 Azure AD 使用者

> [!NOTE]
> 請遵循下列步驟，如果使用者不存在 Azure AD 租使用者中，則提供 Azure FarmBeats 的存取權。 如果使用者存在於 Azure AD 的租使用者中，您可以略過下列步驟。
>

FarmBeats 使用者必須存在於 Azure AD 租使用者中，您才能將它們指派給應用程式和角色。 若要將使用者新增至 Azure AD，請使用下列步驟：

1.  登入 [Azure 入口網站](https://portal.azure.com/)。
2.  在右上角選取您的帳戶，並切換至與 FarmBeats 相關聯的 Azure AD 租使用者。
3.  選取 [Azure Active Directory] > [使用者]。
    您會在目錄中看到使用者清單。
4.  若要將使用者新增至目錄，請選取 [新增使用者]。 如果是外部使用者，請選取 [新增來賓使用者]。

    ![專案伺服器陣列的節拍](./media/create-farms/manage-users-3.png)

5.  完成新使用者的必要欄位。 選取 [建立]。

如需如何在 Azure AD 中管理使用者的詳細資訊，請瀏覽 [Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/add-users-azure-active-directory/) 文件。

## <a name="next-steps"></a>後續步驟

您已成功將使用者新增至 Azure FarmBeats 實例。 現在，請瞭解如何[建立伺服器](manage-farms.md#create-farms)陣列。
