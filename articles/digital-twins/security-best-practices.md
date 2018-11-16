---
title: 了解 Azure Digital Twins 安全性最佳做法 | Microsoft Docs
description: Azure Digital Twins 安全性最佳做法
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: adgera
ms.openlocfilehash: 6ca01523744dbce15f8fdb3bbe2d5a9b44510f3f
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/02/2018
ms.locfileid: "50959472"
---
# <a name="security-best-practices"></a>安全性最佳作法

Azure Digital Twins 安全性可讓您精確地存取 IoT 圖表中的特定資源和動作。 這會透過名為角色型存取控制的細微角色和權限管理來達成。

Azure Digital Twins 也會使用 Azure IoT 上的其他安全性功能，包括 Azure Active Directory (Azure AD)。 基於這個理由，設定 Azure Digital Twins 應用程式的作業牽涉到使用目前建議的許多相同 [Azure IoT 安全性做法](https://docs.microsoft.com/azure/iot-fundamentals/iot-security-best-practices?context=azure/iot-hub/)。

本文摘要說明要遵循的主要最佳做法。

> [!IMPORTANT]
> 若要確保 IoT 空間能獲得最大安全性，請檢閱其他安全性資源。 請務必包含您的裝置廠商。

## <a name="iot-security-best-practices"></a>IoT 安全性最佳做法

可以安全地保護 IoT 裝置的一些主要做法包括：

> [!div class="checklist"]
> * 以防竄改的方式保護連線到 IoT 空間的每個裝置。
> * 限制 IoT 空間內每個裝置、感應器及人員的角色。 如果遭到入侵，可將影響降至最低。
> * 請考量可能會使用裝置 IP 位址篩選和連接埠限制。
> * 限制 I/O 和裝置頻寬，以改善效能。 速率限制可以藉由防止拒絕服務的攻擊來改善安全性。
> * 讓裝置韌體保持最新狀態。

可以安全地保護 IoT 空間的一些主要做法包括：

> [!div class="checklist"]
> * 加密已儲存或永續性的資料。
> * 需要定期變更或重新整理密碼或金鑰。
> * 謹慎地依角色來限制存取權和權限。 請參閱「角色型存取控制最佳做法」一節。
> * 使用功能強大的加密。 需要長密碼，以及使用安全的通訊協定和雙因素驗證。

監視 IoT 資源，以監看落在經常性作業範圍外的極端值、威脅或資源參數。 使用 Azure 分析來管理監視。

> [!NOTE]
> 如需事件處理和監視的詳細資訊，請參閱[使用 Azure Digital Twins 路由傳送事件和訊息](./concepts-events-routing.md)。

## <a name="azure-active-directory-best-practices"></a>Azure Active Directory 最佳做法

Azure Digital Twins 會使用 Azure AD 來驗證使用者及保護應用程式。 Azure AD 支援各種現代架構的驗證。 全部都以 OAuth 2.0 或 OpenID Connect 之類的業界標準通訊協定為基礎。 有一些可用來保護 Azure AD 之 IoT 空間的主要做法，包括：

> [!div class="checklist"]
> * 將 Azure AD 應用程式祕密和金鑰儲存在安全的位置，例如 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)。
> * 使用受信任[憑證授權單位](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-certificate-based-authentication-get-started)所發出的憑證 (而非應用程式祕密) 進行驗證。
> * 限制權杖的 OAuth 2.0 存取範圍。
> * 確認權杖的有效時間長度以及權杖是否依然有效。
> * 設定適當的權杖有效時間長度。
> * 更新過期權杖。

## <a name="role-based-access-control-best-practices"></a>角色型存取控制最佳做法

[!INCLUDE [digital-twins-rbac-best-practices](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="next-steps"></a>後續步驟

若要深入了解 Azure IoT 最佳做法，請參閱 [IoT 安全性最佳做法](https://docs.microsoft.com/azure/iot-fundamentals/iot-security-best-practices?context=azure/iot-hub/)。

若要了解角色型存取控制，請參閱[角色型存取控制](./security-role-based-access-control.md)。

若要了解驗證，請參閱[使用 API 進行驗證](./security-authenticating-apis.md)。
