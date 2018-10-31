---
title: 包含檔案
description: 包含檔案
services: azure-digital-twins
author: adamgerard
ms.service: azure-digital-twins
ms.topic: include
ms.date: 09/27/2018
ms.author: adgera
ms.custom: include file
ms.openlocfilehash: 075587df445b46c8b03c5448cebf8cd8b12f1179
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323719"
---
角色型存取控制是以繼承為導向、用來管理存取、權限和角色的安全性策略。 子系角色會繼承父角色的權限。 權限也可以另行指派，而不繼承自父角色。 此外也可以視需要指派權限以自訂角色。

例如，**空間管理員**可能需要可對指定的空間 (包括其下或其中所有的節點) 執行所有作業的全域存取權，而**裝置安裝人員**可能只需要裝置與感應器的*讀取*和*更新*權限。

無論是哪種情況，為角色授與的存取權均應遵循**最低權限原則**，**正好足以**完成其工作，而*僅*為身分識別授與：

* 要完成其工作所需的存取權。
* 適當且只能執行其作業的角色。

>[!IMPORTANT]
> **一律遵循最低權限原則**。

此外也應遵循兩個重要的角色型存取控制準則：

> [!div class="checklist"]
> * 定期稽核角色指派，以確認每個角色都具有正確的權限。
> * 應在個人變更角色或指派時清除角色和指派。