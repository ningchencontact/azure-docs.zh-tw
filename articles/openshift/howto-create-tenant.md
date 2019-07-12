---
title: 建立 Azure AD 租用戶的 Azure Red Hat OpenShift |Microsoft Docs
description: 以下是如何建立 Azure Active Directory (Azure AD) 租用戶來裝載您的 Microsoft Azure 的 Red Hat OpenShift 叢集。
author: jimzim
ms.author: jzim
ms.service: container-service
manager: jeconnoc
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: 560cdcf8a99a486c7f5177b675cff327c6fb6a41
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66306451"
---
# <a name="create-an-azure-ad-tenant-for-azure-red-hat-openshift"></a>建立 Azure AD 租用戶的 Azure Red Hat OpenShift

需要 Microsoft Azure 的 Red Hat OpenShift [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)租用戶，在其中建立您的叢集。 A*租用戶*是當它們與 Microsoft 建立關聯性來註冊 Azure、 Microsoft Intune 或 Microsoft 365 時，會收到有組織或應用程式的開發人員的 Azure AD 的專用執行個體。 每個 Azure AD 租用戶是不同，而且不同於其他 Azure AD 租用戶都有自己的工作和學校身分識別和應用程式註冊。

如果您還沒有 Azure AD 租用戶，請遵循下列指示來建立一個。

## <a name="create-a-new-azure-ad-tenant"></a>建立新的 Azure AD 租用戶

若要建立租用戶：

1. 登入[Azure 入口網站](https://portal.azure.com/)使用您想要與 Azure 的 Red Hat OpenShift 叢集相關聯的帳戶。
2. 開啟[Azure Active Directory 刀鋒視窗](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory)建立新的租用戶 (也就是新*Azure Active Directory*)。
3. 提供**組織名稱**。
4. 提供**初始網域名稱**。 這會造成*onmicrosoft.com*附加到它。 您可以重複使用的值*組織名稱*這裡。
5. 選擇國家/地區或區域將會在當中建立租用戶。
6. 按一下 [建立]  。
7. 建立 Azure AD 租用戶之後，請選取**按一下這裡管理您的新目錄**連結。 在 Azure 入口網站右上方，應該會顯示您新的租用戶名稱：  

    ![在右上方顯示租用戶名稱的入口網站的螢幕擷取畫面][tenantcallout]  

8. 請記下的*租用戶識別碼*以便您稍後可以指定要建立您的 Azure Red Hat OpenShift 叢集的位置。 在入口網站中，您現在應該會看到 Azure Active Directory 概觀 刀鋒視窗的 新的租用戶。 選取 **屬性**，並將複製的值為您**Directory 識別碼**。 我們會將此值作為`TENANT`中[建立 Azure 的 Red Hat OpenShift 叢集](tutorial-create-cluster.md)教學課程。

[tenantcallout]: ./media/howto-create-tenant/tenant-callout.png

## <a name="resources"></a>資源

請參閱[Azure Active Directory 文件](https://docs.microsoft.com/azure/active-directory/)如需詳細資訊[Azure AD 租用戶](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)。

## <a name="next-steps"></a>後續步驟

了解如何建立服務主體，產生用戶端密碼和驗證回呼 URL，並測試您的 Azure Red Hat OpenShift 叢集上的應用程式建立新的 Active Directory 使用者。

[建立 Azure AD 應用程式物件和使用者](howto-aad-app-configuration.md)