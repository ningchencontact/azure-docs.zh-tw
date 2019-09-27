---
title: 管理單位管理 (預覽) - Azure Active Directory | Microsoft Docs
description: 使用管理單位在 Azure Active Directory 中進行更細微的權限委派
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 08/01/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58b61186a876af90c812ec7faf41fa9f5b14bf4e
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/27/2019
ms.locfileid: "71336927"
---
# <a name="administrative-units-management-in-azure-active-directory-preview"></a>Azure Active Directory 中的系統管理單位管理（預覽）

本文說明 Azure Active Directory （Azure AD）中的管理單位。 系統管理單位是 Azure AD 資源，可以是其他 Azure AD 資源的容器。 在此預覽版本中，這些資源只可以是使用者。 例如，管理單位範圍的使用者帳戶管理員可以更新設定檔資訊、重設密碼，以及僅在其管理單位指派授權給使用者。

您可以使用系統管理單位，將系統管理許可權委派給使用者的子集，並將原則套用到使用者的子集。 您可以使用系統管理單位，將許可權委派給地區系統管理員，或在細微層級設定原則。

## <a name="deployment-scenario"></a>部署案例

管理單位在具有獨立部門的組織中很有用。 假設有一個大型大學的範例，它是由許多自發學校（企業營運、學校工程等等）所組成，每個人都有自己的 IT 系統管理員來控制存取、管理使用者，以及為學校設定原則。 中央系統管理員可以為企業營運建立管理單位，並只將商業學校的學生和員工填入其中。 然後，中央系統管理員可以將商業學校 IT 人員新增至限定範圍的角色，只授與商業學校管理單位中 Azure AD 使用者的系統管理許可權。

## <a name="license-requirements"></a>授權需求

若要使用管理單位，每個管理單位系統管理員都需要 Azure Active Directory Premium 授權。如需詳細資訊，請參閱〈 [開始使用 Azure AD Premium](../fundamentals/active-directory-get-started-premium.md)〉。

## <a name="managing-administrative-units"></a>管理管理單位

在此預覽版本中，您可以建立和管理管理單位的唯一方式，就是使用適用于 Windows PowerShell Cmdlet 的 Azure Active Directory 模組，如使用系統[管理單位](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0)中所述

如需有關軟體需求和安裝 Azure AD 模組的詳細資訊，以及用於管理管理單位之 Azure AD 模組 Cmdlet 的參考資訊，包括語法、參數說明和範例，請參閱[Azure Active目錄 PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)。

## <a name="next-steps"></a>後續步驟

[Azure Active Directory 版本](../fundamentals/active-directory-whatis.md)
