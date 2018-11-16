---
title: 使用 Azure Digital Twins 啟用多租用戶應用程式 | Microsoft Docs
description: 了解如何向 Azure Digital Twins 註冊客戶的 Azure Active Directory 租用戶
author: mavoge
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: mavoge
ms.openlocfilehash: a2d9ece119003c341f49ee03d735d5636b179a32
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2018
ms.locfileid: "51259882"
---
# <a name="enable-multitenant-applications-with-azure-digital-twins"></a>使用 Azure Digital Twins 啟用多租用戶應用程式

使用 Azure Digital Twins 的開發人員通常會想要建置多租用戶應用程式。 「多租用戶應用程式」是可支援多個客戶的單一佈建執行個體。 每個客戶都各自擁有自己的資料和權限。

本文件將詳細說明如何建立支援數個 Azure Active Directory (Azure AD) 租用戶和客戶的 Azure Digital Twins 多租用戶應用程式。

## <a name="scenario-summary"></a>案例摘要

在此案例中，我們假設有開發人員 D 和客戶 C：

- 開發人員 D 具有內含 Azure AD 租用戶的 Azure 訂用帳戶。
- 開發人員 D 會將 Azure Digital Twins 執行個體部署到他們的 Azure 訂用帳戶。
- 開發人員 D 的 Azure AD 租用戶內的使用者可以取得 Azure Digital Twins 服務的權杖，因為 Azure AD 已在開發人員 D 的 Azure AD 租用戶中建立服務主體。
- 開發人員 D 現在會建立與 Azure Digital Twins 管理 API 直接整合的行動應用程式。
- 開發人員 D 允許客戶 C 使用該行動應用程式。
- 客戶 C 必須獲得授權，才能在開發人員 D 的應用程式內使用 Azure Digital Twins 管理 API。

  > [!IMPORTANT]
  > - 當客戶 C 登入開發人員 D 的應用程式時，該應用程式無法取得讓客戶 C 的使用者與管理 API 通訊的權杖。
  > - Azure AD 會擲回錯誤，指出無法在 C 客戶的目錄中辨識出 Azure Digital Twins。

## <a name="solution"></a>解決方法

若要解決上述案例的問題，必須執行下列動作，在客戶 C 的 Azure AD 租用戶內建立 Azure Digital Twins 服務主體：

- 如果客戶 C 還沒有內含 Azure AD 租用戶的 Azure 訂用帳戶：

  - 客戶 C 的 Azure AD 租用戶管理員必須取得[隨用隨付的 Azure 訂用帳戶](https://azure.microsoft.com/offers/ms-azr-0003p/)。
  - 客戶 C 的 Azure AD 租用戶管理員隨後必須[將其租用戶與新的訂用帳戶連結](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect)。

- 客戶 C 的 Azure AD 租用戶管理員會在 [Azure 入口網站](https://portal.azure.com)中採取下列步驟：

  1. 開啟 [訂用帳戶]。
  1. 選取含有 Azure AD 租用戶的訂用帳戶，以便在開發人員 D 的應用程式中使用。
  1. 選取 [資源提供者]。
  1. 搜尋 **Microsoft.IoTSpaces**。
  1. 選取 [註冊]。
  
## <a name="next-steps"></a>後續步驟

若要深入了解如何將使用者定義函式與 Azure Digital Twins 搭配使用，請參閱 [Azure Digital Twins UDF](how-to-user-defined-functions.md)。

若要了解如何使用角色型存取控制進一步保護具有角色指派的應用程式，請參閱 [Azure Digital Twins 的角色型存取控制](security-create-manage-role-assignments.md)。
