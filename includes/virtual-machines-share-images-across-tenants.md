---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 499aeccdf00980eeb66ac6ee06e45267fd515143
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/23/2019
ms.locfileid: "66145893"
---
共用映像組件庫可讓您使用 RBAC 共用映像。 您可以使用 RBAC 外部租用戶內您的租用戶，或甚至個人共用影像。 但是，如果您想要共用您的 Azure 租用戶，大規模的情況下，外部的映像，您就應該建立以促進共用應用程式註冊。  使用應用程式註冊，可以啟用更複雜的共享案例，例如： 

* 管理共用映像，當某家公司取得另一個與 Azure 基礎結構則會分散到不同的租用戶。 
* Azure 合作夥伴代表客戶管理 Azure 基礎結構。 合作夥伴租用戶內完成自訂映像，但基礎結構部署會在客戶的租用戶中。 


## <a name="create-the-app-registration"></a>建立應用程式註冊

建立將用於這兩個租用戶共用的映像資源庫資源的應用程式註冊。
1. 開啟[在 Azure 入口網站中的應用程式註冊 （預覽）](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType//sourceType/)。    
1. 選取 **新的註冊**從頁面頂端的功能表。
1. 在 **名稱**，型別*myGalleryApp*。
1. 在 **支援的帳戶類型**，選取**任何組織的目錄和個人 Microsoft 帳戶中的帳戶**。
1. 在 **重新導向 URI**，型別*https://www.microsoft.com* ，然後選取**註冊**。 建立應用程式註冊之後，會開啟 [概觀] 頁面。
1. 在 [概觀] 頁面上複製**應用程式 （用戶端） 識別碼**和稍後儲存以供使用。   
1. 選取 **憑證與祕密**，然後選取**新的用戶端祕密**。
1. 在 **描述**，型別*共用映像資源庫跨租用戶應用程式祕密*。
1. 在  **Expires**，保留預設值是**1 年**，然後選取**新增**。
1. 複製的祕密的值，並將它儲存到安全的地方。 離開頁面之後，您無法擷取它。


提供使用共用的映像庫的應用程式註冊權限。
1. 在 Azure 入口網站中，選取您想要分享另一個租用戶共用映像庫。
1. 選取 **選取 存取控制 (IAM)**，然後在**新增角色指派**選取*新增*。 
1. 底下**角色**，選取**讀取器**。
1. 底下**存取權指派給：**，，請將此做**Azure AD 使用者、 群組或服務主體**。
1. 底下**選取 **，型別*myGalleryApp*並加以選取，當它出現在清單中。 當您完成時，選取**儲存**。


## <a name="give-tenant-2-access"></a>授與租用戶 2 的存取權

讓應用程式的租用戶 2 存取，藉由使用瀏覽器登入要求。 取代*<Tenant2 ID>* 與您想要共用您的圖像藝廊與租用戶的租用戶識別碼。 取代 *< 應用程式 （用戶端） 識別碼 >* 與您所建立的應用程式註冊的應用程式識別碼。 完成取代項目，請將此 URL 貼入瀏覽器，並遵循登入提示登入租用戶 2。

```
https://login.microsoftonline.com/<Tenant 2 ID>/oauth2/authorize?client_id=<Application (client) ID>&response_type=code&redirect_uri=https%3A%2F%2Fwww.microsoft.com%2F 
```

在  [Azure 入口網站](https://portal.azure.com)租用戶 2 身分登入，並讓您想要用來建立 VM 的資源群組的應用程式註冊存取權。

1. 選取資源群組，然後選取**存取控制 (IAM)**。 底下**新增的角色指派**選取**新增**。 
1. 底下**角色**，型別**參與者**。
1. 底下**存取權指派給：**，，請將此做**Azure AD 使用者、 群組或服務主體**。
1. 底下**選取 **型別*myGalleryApp*然後當它出現在清單中選取它。 當您完成時，選取**儲存**。

> [!NOTE]
> 您需要等待映像版本，才能完全完成建置和複寫之前，您可以使用相同的受控映像來建立另一個映像版本。

