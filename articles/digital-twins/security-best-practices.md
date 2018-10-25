---
title: 了解 Azure Digital Twins 安全性最佳做法 | Microsoft Docs
description: Azure Digital Twins 安全性最佳做法
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: adgera
ms.openlocfilehash: 28eb8b5dc0f75b5e031070803d35c8a1ceb1f000
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2018
ms.locfileid: "49364332"
---
# <a name="security-best-practices"></a>安全性最佳做法

Azure Digital Twins 安全性可讓您精確地存取 IoT 圖表中的特定資源和動作。 這是透過稱為「角色型存取控制」的細微角色和權限管理來達成的。

Azure Digital Twins 也會利用 Azure IoT 上的其他安全性功能，包括 Azure Active Directory。 基於這個理由，設定 Azure Digital Twins 應用程式的作業牽涉到使用目前建議的許多相同 [Azure IoT 安全性做法](https://docs.microsoft.com/azure/iot-fundamentals/iot-security-best-practices?context=azure/iot-hub/)。

本文摘要說明要遵循的主要最佳做法。

> [!IMPORTANT]
> 請檢閱其他安全性資源 (包括裝置廠商)，以確保 IoT 空間能獲得最大安全性。

## <a name="iot-security-best-practices"></a>IoT 安全性最佳做法

可以安全地保護 IoT 裝置的一些主要做法包括：

> [!div class="checklist"]
> * 以防竄改的方式保護連線到 IoT 空間的每個裝置。
> * 限制 IoT 空間內每個裝置、感應器及人員的角色。 如果遭到入侵，影響可降至最低。
> * 可能會使用裝置 IP 位址篩選。
> * 限制 I/O 和裝置頻寬，以改善效能。 速率限制可以藉由防止拒絕服務的攻擊來改善安全性。

可以安全地保護 IoT 空間的一些主要做法包括：

> [!div class="checklist"]
> * 加密已儲存或永續性的資料。
> * 需要定期變更或重新整理密碼或金鑰。
> * 謹慎地依據角色限制存取權和權限 (請參閱下方的「角色型存取控制」最佳做法)。
> * 使用功能強大的加密。 這表示需要很長的密碼、使用安全的通訊協定，以及雙因素驗證。

您可以透過 Azure 分析來管理 IoT 資源的監視作業，此作業可讓您監看落在經常性作業範圍外的極端值、威脅或資源參數。

> [!NOTE]
> 如需事件處理和監視的詳細資訊，請參閱關於[事件 > 路由](./concepts-events-routing.md)的文章。

## <a name="azure-active-directory-best-practices"></a>Azure Active Directory 最佳做法

Azure Digital Twins 會使用 Azure Active Directory 來驗證使用者及保護應用程式。 Azure Active Directory 支援各種新型架構的驗證，它們全都以產業標準通訊協定 (例如 OAuth 2.0 或 OpenID Connect) 為基準。 若要保護 Azure Active Directory 的 IoT 空間，其中的一些主要做法包括：

> [!div class="checklist"]
> * 將 Azure Active Directory 應用程式祕密和金鑰儲存在安全的位置，例如 [Key Vault](https://azure.microsoft.com/services/key-vault/)。
> * 使用受信任[憑證授權單位](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-certificate-based-authentication-get-started)所發行的憑證 (而非應用程式祕密) 來進行驗證。
> * 限制權杖的 OAuth 2.0 存取範圍。
> * 確認權杖的有效時間長度以及權杖是否依然有效。
> * 設定適當的權杖有效時間長度。
> * 更新過期權杖。

## <a name="role-based-access-control-best-practices"></a>角色型存取控制最佳做法

[!INCLUDE [digital-twins-rbac-best-practices](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="next-steps"></a>後續步驟

若要深入了解 Azure IoT 最佳做法，請參閱 [IoT 安全性最佳做法](https://docs.microsoft.com/azure/iot-fundamentals/iot-security-best-practices?context=azure/iot-hub/)。

若要了解角色型存取控制，請參閱[角色型存取控制](./security-role-based-access-control.md)。

關於驗證，請參閱[使用 API 進行驗證](./security-authenticating-apis.md)。
