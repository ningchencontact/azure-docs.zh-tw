---
title: 設定與 Azure 受控應用程式核准-just-in-time 存取
description: 描述 Azure 受控應用程式的取用者在 just-in-time 存取受管理的應用程式所要求的經過。
author: MSEvanhi
ms.service: managed-applications
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: 55fbf7292ab894cad3de3de9e96ddc96fe0b79b3
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481710"
---
# <a name="configure-and-approve-just-in-time-access-for-azure-managed-applications"></a>設定與 Azure 受控應用程式核准-just-in-time 存取

為受管理的應用程式的取用者，您可能不知道如何讓 「 發行者 」 端的永久存取權的受管理的資源群組。 為了讓您更充分地掌控受管理的資源的存取權授與，Azure 受控應用程式會提供稱為在 just-in-time (JIT) 存取，目前為預覽狀態的功能。 它可讓您核准時還有多久 「 發行者 」 的資源群組的存取權。 發行者可以讓必要的更新，在這段時間，但透過該時間時，「 發行者 」 的存取會過期。

存取權授與的工作流程是：

1. 「 發行者 」 新增到 marketplace 的受管理應用程式，並指定使用 JIT 存取的。

1. 在部署期間，您可以啟用 JIT 存取您的受控應用程式執行個體。

1. 在部署後，您可以變更 JIT 存取的設定。

1. 「 發行者 」 傳送要求以進行存取。

1. 在您核准要求。

本文著重於啟用 JIT 存取，並核准要求的取用者採取的動作。 若要深入了解發佈受控的應用程式，與 JIT 存取，請參閱[要求中 Azure 受控應用程式中即時存取](request-just-in-time-access.md)。

> [!NOTE]
> 若要使用-just-in-time 存取時，您必須擁有[Azure Active Directory P2 授權](../active-directory/privileged-identity-management/subscription-requirements.md)。

## <a name="enable-during-deployment"></a>在部署期間啟用

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 尋找與 JIT 啟用受管理的應用程式的 marketplace 項目。 選取 [建立]  。

1. 同時為新的受控應用程式，提供值**JIT 組態**步驟可讓您啟用或停用受管理的應用程式的 JIT 存取權。 選取  **是** for**啟用 JIT 存取**。 針對 JIT 啟用在 marketplace 中所定義的受控應用程式的預設會選取此選項。

   ![設定存取權](./media/approve-just-in-time-access/configure-jit-access.png)

   您只可以在部署期間啟用 JIT 存取權。 如果您選取**No**，「 發行者 」 取得的永久存取至受控的資源群組。 您無法在稍後啟用 JIT 存取權。

1. 若要變更預設核准設定，請選取**來自訂 JIT 組態**。

   ![自訂存取](./media/approve-just-in-time-access/customize-jit-access.png)

   根據預設，啟用 JIT 的受管理應用程式具有下列設定：

   * 核准模式 – 自動
   * 最大的存取權持續時間 – 8 小時
   * 核准者 – 無

   當核准模式設定為**自動**，核准者收到的通知，針對每個要求，但是自動核准要求。 當設定為**手動**、 核准者收到的通知，針對每個要求，和其中一個必須核准它。

   啟用持續時間上限指定的最大的受管理的資源群組的存取權的 「 發行者 」 可以要求的時間量。

   核准者清單是可以核准 JIT 存取要求的 Azure Active Directory 使用者。 若要新增核准者，請選取**新增核准者**並搜尋使用者。

   更新後的設定，請選取**儲存**。

## <a name="update-after-deployment"></a>在部署後更新

您可以變更如何核准要求的值。 不過，如果您未在部署期間啟用 JIT 存取，您無法啟用它更新版本。

若要變更已部署的受管理應用程式的設定：

1. 在入口網站中，選取 管理應用程式。

1. 選取  **JIT 組態**並視需要變更設定。

   ![變更存取設定](./media/approve-just-in-time-access/change-settings.png)

1. 完成時，選取 [儲存]  。

## <a name="approve-requests"></a>核准要求

當 「 發行者 」 要求存取時，要求會通知您。 您可以核准 JIT 存取要求直接透過受控應用程式，或透過 Azure AD Privileged Identity Management 服務的所有受管理應用程式。 若要使用-just-in-time 存取時，您必須擁有[Azure Active Directory P2 授權](../active-directory/privileged-identity-management/subscription-requirements.md)。

核准要求，透過受管理的應用程式：

1. 選取  **JIT 存取**受管理的應用程式，然後選取**核准要求**。

   ![核准要求](./media/approve-just-in-time-access/approve-requests.png)
 
1. 選取要核准的要求。

   ![選取要求](./media/approve-just-in-time-access/select-request.png)

1. 在表單中，提供進行核准，以及選取的原因**核准**。

核准要求透過 Azure AD Privileged Identity Management:

1. 選取 **所有的服務**，並開始搜尋**Azure AD Privileged Identity Management**。 從可用的選項加以選取。

   ![搜尋服務](./media/approve-just-in-time-access/search.png)

1. 選取 **核准要求**。

   ![選取核准要求](./media/approve-just-in-time-access/select-approve-requests.png)

1. 選取  **Azure 受控應用程式**，然後選取要核准的要求。

   ![選取的要求](./media/approve-just-in-time-access/view-requests.png)

## <a name="next-steps"></a>後續步驟

若要深入了解發佈受控的應用程式，與 JIT 存取，請參閱[要求中 Azure 受控應用程式中即時存取](request-just-in-time-access.md)。
