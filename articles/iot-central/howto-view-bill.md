---
title: 在 Azure IoT Central 應用程式中, 查看您的帳單並將試用版轉換為隨用隨付 |Microsoft Docs
description: 身為系統管理員, 瞭解如何在 Azure IoT Central 應用程式中, 查看您的帳單並從試用版轉換為隨用隨付
author: v-krghan
ms.author: v-krghan
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 16a58bfc3fa245ed1ede19b0439419ab4590234e
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/01/2019
ms.locfileid: "68729265"
---
# <a name="view-your-bill-in-iot-central-application"></a>在 IoT Central 應用程式中查看您的帳單

本文說明如何以系統管理員的身分, 在 [管理] 區段中查看 Azure IoT Central 應用程式中的帳單, 以及如何將您的試用版轉換成隨用隨付。

若要存取並使用 [管理] 區段，您必須屬於 Azure IoT Central 應用程式的**管理員**角色。 如果您建立 Azure IoT Central 應用程式，就會自動將您指派給該應用程式的**管理員**角色。

## <a name="view-your-bill"></a>檢視您的帳單

若要檢視帳單，請移至 [管理] 區段中的 [帳單] 頁面。 Azure 帳單頁面即會在新的索引標籤中開啟，您可在其中看見每個 Azure IoT Central 應用程式的帳單。

## <a name="convert-your-trial-to-pay-as-you-go"></a>將試用版轉換為隨用隨付

- **試用版**應用程式會在到期前七天免費。 此類應用程式可在到期前隨時轉換成隨用隨付。
- 隨用**隨付**應用程式會依每個裝置計費, 每個訂用帳戶的前五個裝置免費。

在 [Azure IoT Central 定價頁面](https://azure.microsoft.com/pricing/details/iot-central/)上深入了解定價。

在 [帳單] 區段中, 您可以將試用版應用程式轉換成隨用隨付。

若要完成此自助程序，請遵循這些步驟：

1. 請移至 [管理] 區段中的 [帳單] 頁面。

    ![試用狀態](media/howto-administer/freetrialbilling.png)

1. 選取 [**轉換為隨用隨付**]。

    ![轉換試用](media/howto-administer/convert.png)

1. 選取適當的 Azure Active Directory，然後選取要用於隨用隨付應用程式的 Azure 訂用帳戶。

1. 在您選取 [**轉換**] 之後, 您的應用程式現在是隨用隨付應用程式, 而您會開始計費。

## <a name="next-steps"></a>後續步驟

既然您已瞭解如何在 Azure IoT Central 應用程式中查看帳單, 建議的下一個步驟是瞭解如何自訂 Azure IoT Central 中的[應用程式 UI](howto-customize-ui.md) 。