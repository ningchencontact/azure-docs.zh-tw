---
title: 管理使用者
description: 描述如何管理使用者
author: uhabiba04
ms.topic: article
ms.date: 12/02/2019
ms.author: v-umha
ms.openlocfilehash: 37dacf0adfc9e3dcbab963cef50e2ee5209df314
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2019
ms.locfileid: "74852350"
---
# <a name="manage-users"></a>管理使用者

Azure FarmBeats 包含屬於您的 Azure Active Directory （Azure AD）之人員的使用者管理。 您將能夠將使用者新增至您的 Azure FarmBeats 實例以存取 Api、從伺服器陣列中查看產生的對應和感應器遙測

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

若要將使用者新增至 Azure FarmBeats， 
1.  登入加速器，然後按一下 [設定] 圖示
2.  按一下 存取控制

    ![專案伺服器陣列的節拍](./media/create-farms/settings-users-1.png)

3.  輸入您想要授與存取權之使用者的電子郵件識別碼
4.  選取所需的角色– [系統管理員] 或 [唯讀]
5.  按一下 [新增角色]

新增的使用者現在將能夠存取 Azure FarmBeats （資料中樞和加速器）。

## <a name="delete-user-from-azure-farmbeats"></a>從 Azure FarmBeats 刪除使用者

若要從 Azure FarmBeats 系統中移除使用者，您可以
1.  登入加速器，然後按一下 [設定] 圖示
2.  按一下 存取控制
3.  按一下您要移除之使用者的電子郵件識別碼附近的 [刪除] 圖示

使用者已從系統中移除。 您將會收到下列訊息，以確認操作成功


![專案伺服器陣列的節拍](./media/create-farms/manage-users-2.png)


## <a name="add-azure-ad-users"></a>新增 Azure AD 使用者

> [!NOTE]
> 只有當您嘗試提供 Azure FarmBeats 存取權的使用者不存在於 Azure AD 租使用者時，您才需要執行下列步驟。 如果使用者已存在，則不需要執行下列步驟

FarmBeats 使用者必須存在於 Azure AD 租使用者中，您才能將它們指派給應用程式和角色。 若要將使用者新增至 Azure AD，請使用下列步驟：
1.  登入 [Azure 入口網站](https://portal.azure.com/)。
2.  在右上角選取您的帳戶，並切換至與 FarmBeats 相關聯的 Azure AD 租使用者
3.  選取 [Azure Active Directory] > [使用者]。 您會在目錄中看到使用者清單。
4.  若要將使用者新增至目錄，請選取 [新增使用者]。 如果是外部使用者，請選取 [新增來賓使用者]。

    ![專案伺服器陣列的節拍](./media/create-farms/manage-users-3.png)

5.  完成新使用者的必要欄位。 選取 [建立]。

如需如何在 Azure AD 中管理使用者的詳細資訊，請瀏覽 [Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/add-users-azure-active-directory/) 文件。

## <a name="next-steps"></a>後續步驟

您已部署 Azure FarmBeats。 現在，請瞭解如何[建立伺服器](manage-farms.md#create-farms)陣列。

