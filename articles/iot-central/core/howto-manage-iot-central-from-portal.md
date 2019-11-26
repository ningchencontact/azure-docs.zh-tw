---
title: 從 Azure 入口網站管理 IoT Central | Microsoft Docs
description: This article describes how to create and manage your IoT Central applications from the Azure portal.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 10/02/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 2133819ce7c298e2f73fdc5a68b80b64f9e72ea7
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2019
ms.locfileid: "74480409"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>從 Azure 入口網站管理 IoT Central

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Instead of creating and managing IoT Central applications on the [Azure IoT Central application manager](https://aka.ms/iotcentral) website, you can use the [Azure portal](https://portal.azure.com) to manage your applications.

## <a name="create-iot-central-applications"></a>建立 IoT Central 應用程式

To create an application, navigate to the [Azure portal](https://ms.portal.azure.com) and select **Create a resource** in the main pane on the left.

![管理入口網站：導覽功能表](media/howto-manage-iot-central-from-portal/image0.png)

在搜尋列中輸入 **IoT Central**。

![管理入口網站：搜尋](media/howto-manage-iot-central-from-portal/image0a1.png)

Select the **IoT Central Application** line-item in the search results.

![管理入口網站：搜尋結果](media/howto-manage-iot-central-from-portal/image0b1.png)

現在，選取 [建立]。

![管理入口網站：IoT Central 資源](media/howto-manage-iot-central-from-portal/image0c1.png)

填寫表單中的所有欄位。 This form is similar to the form you fill out to create applications on the [Azure IoT Central application manager](https://aka.ms/iotcentral) website. 如需詳細資訊，請參閱[建立 IoT Central 應用程式](quick-deploy-iot-central.md)快速入門。

You can create IoT Central application with general features by selecting **Sample Contoso**, **Custom application** and **Sample Devkits** as application templates, all the other application templates uses public preview features.

![create IoT Central form](media/howto-manage-iot-central-from-portal/image6a.png)

**Location** is the [geography](https://azure.microsoft.com/global-infrastructure/geographies/) where you’d like to create your application. Typically, you should choose the location that's physically closest to your devices to get optimal performance. Azure IoT Central is currently available in the **United States**, **Australia**, **Asia Pacific**, or in **Europe**.  Once you choose a location, you can't move your application to a different location later.

> [!NOTE]
> The preview application templates are currently only available in the **Europe** and **United States** locations.

![管理入口網站：建立 IoT Central 資源](media/howto-manage-iot-central-from-portal/image1a.png)  

After filling out all fields, select **Create**.

## <a name="manage-existing-iot-central-applications"></a>管理現有的 IoT Central 應用程式

如果您已有 Azure IoT Central 應用程式，則可以將其刪除，或移至 Azure 入口網站中的其他訂用帳戶或資源群組。

> [!NOTE]
> 您無法查看 Azure 入口網站中的試用版應用程式，因為這類應用程式並未與您的訂用帳戶相關聯。

To get started, select **All resources** in the main pane on the left. 使用搜尋方塊鍵入您應用程式的名稱，以在您的資源清單中找到該名稱。 Then select the IoT Central application you'd like to manage.

![管理入口網站：資源管理](media/howto-manage-iot-central-from-portal/image2a.png)

To navigate to the application, select the IoT Central Application URL.

![管理入口網站：資源管理](media/howto-manage-iot-central-from-portal/image3.png)

To move the application to a different resource group, select **change** beside the resource group. 在 [移動資源] 頁面上，挑選要將此應用程式遷移至的資源群組。

![管理入口網站：資源管理](media/howto-manage-iot-central-from-portal/image4a.png)

To move the application to a different subscription, select the **change** link beside the subscription. 在出現的對話方塊中，選擇要將此應用程式遷移至的訂用帳戶。

![管理入口網站：資源管理](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>後續步驟

您現在已了解如何從 Azure 入口網站中管理 Azure IoT Central 應用程式，以下是建議的後續步驟：

> [!div class="nextstepaction"]
> [管理您的應用程式](howto-administer.md)