---
title: 包含檔案
description: 包含檔案
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 08/12/2019
ms.author: v-adgera
ms.custom: include file
ms.openlocfilehash: fa88bf7f8db9b4fea63429dbfd18f84ee84ccda1
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2019
ms.locfileid: "69012073"
---
角色型存取控制是以繼承為導向、用來管理存取、權限和角色的安全性策略。 子系角色會繼承父角色的權限。 權限也可以另行指派，而不繼承自父角色。 此外也可以視需要指派權限以自訂角色。

例如，空間管理員可能需要全域存取權，以針對指定的空間執行所有作業。 存取包括空間底下或空間中的所有節點。 裝置安裝程式可能只需要裝置和感應器的「讀取」和「更新」權限。

無論是哪種情況，為角色授與的存取權均應遵循最低權限原則，正好足以完成其工作。 根據此原則，身分識別應「只」授與：

* 要完成其工作所需的存取權。
* 適當且只能執行其作業的角色。

>[!IMPORTANT]
> 一律遵循最低權限原則。

此外也應遵循兩個重要的角色型存取控制準則：

> [!div class="checklist"]
> * 定期稽核角色指派，以確認每個角色都具有正確的權限。
> * 當個人變更角色或指派時，清除角色和指派。