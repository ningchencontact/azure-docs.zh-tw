---
title: 在 Azure IoT Central 應用程式中管理使用者和角色 |Microsoft Docs
description: 身為系統管理員，如何在您的 Azure IoT Central 應用程式中管理使用者和角色
author: v-krghan
ms.author: v-krghan
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 937c4a4dbf976564cbf8dc562bdec3b328fc2bc0
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2019
ms.locfileid: "72950089"
---
# <a name="manage-users-and-roles-in-your-iot-central-application"></a>管理 IoT Central 應用程式中的使用者和角色

本文說明如何以系統管理員的身分，在 Azure IoT Central 應用程式中新增、編輯及刪除使用者，以及如何管理 Azure IoT Central 應用程式中的角色。

若要存取並使用 [管理] 區段，您必須屬於 Azure IoT Central 應用程式的**管理員**角色。 如果您建立 Azure IoT Central 應用程式，就會自動將您指派給該應用程式的**管理員**角色。


## <a name="add-users"></a>新增使用者

每個使用者都必須具有使用者帳戶，才能登入並存取 Azure IoT Central 應用程式。 Azure IoT Central 支援 Microsoft 帳戶 (MSA) 和 Azure Active Directory (Azure AD) 帳戶。 Azure IoT Central 目前不支援 Azure Active Directory 群組。

如需詳細資訊，請參閱 [Microsoft 帳戶說明](https://support.microsoft.com/products/microsoft-account?category=manage-account)和[快速入門：將使用者新增至 Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)。

1. 若要將使用者新增至 IoT Central 應用程式，請前往 [管理] 區段中的 [使用者] 頁面。

    ![使用者清單](media/howto-administer/image1.png)

1. 若要新增使用者，在 [使用者] 頁面上，選擇 [+ 新增使用者]。

1. 從 [角色] 下拉式清單中選擇使用者的角色。 請在本文的[管理角色](#manage-roles)一節中深入了解角色。

    ![角色選取](media/howto-administer/image3.png)

    > [!NOTE]
    >  若要大量新增使用者，請針對所有想要新增的使用者，輸入以分號分隔的使用者識別碼。 從 [角色] 下拉式清單中選擇角色。 然後選取 [儲存]。

### <a name="edit-the-roles-that-are-assigned-to-users"></a>編輯指派給使用者的角色

角色一經指派之後，就無法變更。 若要變更指派給使用者的角色，請刪除該使用者，然後以不同的角色再次新增該使用者。

> [!NOTE]
> 指派的角色專屬於 IoT Central 應用程式，無法從 Azure 入口網站進行管理。

## <a name="delete-users"></a>刪除使用者

若要刪除使用者，請選取 [使用者] 頁面上的一或多個核取方塊。 然後選取 [刪除]。

## <a name="manage-roles"></a>管理角色

角色可讓您控制組織中的哪些人員可以在 IoT Central 中執行各種不同的工作。 有三個角色您可以指派給應用程式的使用者。

### <a name="administrator"></a>管理員

擔任 [系統管理員] 角色的使用者能夠存取應用程式中的所有功能。

系統會將建立應用程式的使用者自動指派給**管理員**角色。 必須至少有一個使用者擔任 [系統管理員] 角色。

### <a name="application-builder"></a>應用程式建置者

擔任 [應用程式建置者] 角色的使用者除了管理應用程式之外，可以執行應用程式中的所有工作。 構建者可以建立、編輯和刪除裝置範本和裝置、管理裝置集合，以及執行分析和作業。 建置者無法存取應用程式的 [管理] 區段。

### <a name="application-operator"></a>應用程式操作員

擔任 [應用程式操作員] 角色的使用者無法對裝置範本進行變更，也無法管理應用程式。 操作員可以新增和刪除裝置、管理裝置集合，以及執行分析和作業。 操作員無法存取應用程式的 [應用程式建置者] 和 [管理] 頁面。

## <a name="next-steps"></a>後續步驟

既然您已瞭解如何在 Azure IoT Central 中管理使用者和角色，建議的下一個步驟是瞭解如何在 Azure IoT Central 中[查看您的帳單](howto-view-bill.md)。
