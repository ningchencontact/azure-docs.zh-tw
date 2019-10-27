---
title: 在 Azure IoT Central 應用程式中管理您的帳單並將試用版轉換為隨用隨付 |Microsoft Docs
description: 身為系統管理員，瞭解如何在 Azure IoT Central 應用程式中管理您的帳單並從試用版轉換為隨用隨付
author: v-krghan
ms.author: v-krghan
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: a0d12d762b675141035bebb9308a25cc71360c5c
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2019
ms.locfileid: "72949855"
---
# <a name="manage-your-bill-in-an-iot-central-application"></a>在 IoT Central 應用程式中管理您的帳單

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

本文說明如何以系統管理員身分，在 [管理] 區段中管理 Azure IoT Central 應用程式中的帳單，以及如何將您的試用版轉換成隨用隨付。

若要存取並使用 [管理] 區段，您必須屬於 Azure IoT Central 應用程式的**管理員**角色。 如果您建立 Azure IoT Central 應用程式，就會自動將您指派給該應用程式的**管理員**角色。

## <a name="view-your-bill"></a>檢視您的帳單

若要檢視帳單，請移至 [管理] 區段中的 [帳單] 頁面。 Azure 帳單頁面即會在新的索引標籤中開啟，您可在其中看見每個 Azure IoT Central 應用程式的帳單。

## <a name="convert-your-trial-to-pay-as-you-go"></a>將您的試用版轉換為隨用隨付

- **試用版**應用程式可以免費試用七天。 此類應用程式可在到期前隨時轉換成隨用隨付。
- 隨用**隨付**應用程式會依每個裝置計費，每個訂用帳戶的前五個裝置免費。

您可以在 [Azure IoT Central 定價頁面](https://azure.microsoft.com/pricing/details/iot-central/)上深入了解定價。

在 [帳單] 區段中，您可以將試用版應用程式轉換成隨用隨付。

若要完成此自助程序，請遵循這些步驟：

1. 請移至 [管理] 區段中的 [帳單] 頁面。

    ![試用狀態](media/howto-view-bill-pnp/freetrialbilling.png)

1. 選取 [**轉換為隨用隨付**]。

    ![轉換試用](media/howto-view-bill-pnp/convert.png)

1. 選取適當的 Azure Active Directory，然後選取要用於隨用隨付應用程式的 Azure 訂用帳戶。

1. 在您選取 [**轉換**] 之後，您的應用程式現在是隨用隨付應用程式，而您會開始計費。

## <a name="next-steps"></a>後續步驟

既然您已瞭解如何在 Azure IoT Central 應用程式中管理您的帳單，建議的下一個步驟是瞭解如何在 Azure IoT Central 中[自訂應用程式 UI](howto-customize-ui-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) 。