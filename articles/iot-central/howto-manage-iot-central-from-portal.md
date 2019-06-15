---
title: 從 Azure 入口網站管理 IoT Central | Microsoft Docs
description: 從 Azure 入口網站管理 IoT Central。
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 01/14/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: c72de0ef874659a5d7840689e38bd7857c25b840
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65464166"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>從 Azure 入口網站管理 IoT Central

[!INCLUDE [iot-central-selector-manage](../../includes/iot-central-selector-manage.md)]

您可以使用 [Azure 入口網站](https://portal.azure.com)來管理您的應用程式，而非從 IoT Central [Application Manager](https://aka.ms/iotcentral) 頁面建立和管理 IoT Central 應用程式。

## <a name="create-iot-central-applications"></a>建立 IoT Central 應用程式

若要建立應用程式，瀏覽至[Azure 入口網站](https://ms.portal.azure.com)，然後選取**建立資源**左側主導覽功能表中。

![管理入口網站：導覽功能表](media/howto-manage-iot-central-from-portal/image0.png)

在搜尋列中輸入 **IoT Central**。

![管理入口網站：搜尋](media/howto-manage-iot-central-from-portal/image0a1.png)

選取  **IoT Central 應用程式**明細項目在搜尋結果中。

![管理入口網站：搜尋結果](media/howto-manage-iot-central-from-portal/image0b1.png)

現在，選取 [建立]  。

![管理入口網站：IoT 中心資源](media/howto-manage-iot-central-from-portal/image0c1.png)

填寫表單中的所有欄位。 此表單類似於您填妥以在 IoT Central [Application Manager](https://aka.ms/iotcentral) 頁面上建立應用程式的表單。 如需詳細資訊，請參閱[建立 IoT Central 應用程式](quick-deploy-iot-central.md)快速入門。

![管理入口網站：建立 IoT Central 資源](media/howto-manage-iot-central-from-portal/image1a.png)  

之後填寫所有欄位，選取**建立**。

## <a name="manage-existing-iot-central-applications"></a>管理現有的 IoT Central 應用程式

如果您已有 Azure IoT Central 應用程式，則可以將其刪除，或移至 Azure 入口網站中的其他訂用帳戶或資源群組。

> [!NOTE]
> 您無法查看 Azure 入口網站中的試用版應用程式，因為這類應用程式並未與您的訂用帳戶相關聯。

若要開始使用，請選取**的所有資源**左側主導覽功能表中。 使用搜尋方塊鍵入您應用程式的名稱，以在您的資源清單中找到該名稱。 然後選取您想要管理的 IoT Central 應用程式。

![管理入口網站：資源管理](media/howto-manage-iot-central-from-portal/image2a.png)

若要瀏覽至應用程式，選取 IoT Central 應用程式 URL。

![管理入口網站：資源管理](media/howto-manage-iot-central-from-portal/image3.png)

若要移動到不同的資源群組的應用程式，請選取**變更**旁邊的資源群組。 在 [移動資源]  頁面上，挑選要將此應用程式遷移至的資源群組。

![管理入口網站：資源管理](media/howto-manage-iot-central-from-portal/image4a.png)

若要移動到不同的訂用帳戶的應用程式，請選取**變更**訂用帳戶旁的連結。 在出現的對話方塊中，選擇要將此應用程式遷移至的訂用帳戶。

![管理入口網站：資源管理](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>後續步驟

您現在已了解如何從 Azure 入口網站中管理 Azure IoT Central 應用程式，以下是建議的後續步驟：

> [!div class="nextstepaction"]
> [管理您的應用程式](howto-administer.md)