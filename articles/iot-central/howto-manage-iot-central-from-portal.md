---
title: 從 Azure 入口網站管理 IoT Central | Microsoft Docs
description: 從 Azure 入口網站管理 IoT Central。
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 07/11/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 7893c8e8b8d67b4b63bd9d6bb5a71552e95c9125
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850255"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>從 Azure 入口網站管理 IoT Central

[!INCLUDE [iot-central-selector-manage](../../includes/iot-central-selector-manage.md)]

您可以使用 [Azure 入口網站](https://portal.azure.com)來管理您的應用程式，而非從 IoT Central [Application Manager](https://aka.ms/iotcentral) 頁面建立和管理 IoT Central 應用程式。

## <a name="create-iot-central-applications"></a>建立 IoT Central 應用程式

若要建立應用程式, 請流覽至[Azure 入口網站](https://ms.portal.azure.com)並選取左側主導覽功能表中的 [**建立資源**]。

![管理入口網站：導覽功能表](media/howto-manage-iot-central-from-portal/image0.png)

在搜尋列中輸入 **IoT Central**。

![管理入口網站：搜尋](media/howto-manage-iot-central-from-portal/image0a1.png)

在搜尋結果中選取 [ **IoT Central 應用程式**行] 專案。

![管理入口網站：搜尋結果](media/howto-manage-iot-central-from-portal/image0b1.png)

現在，選取 [建立]  。

![管理入口網站：IoT 中心資源](media/howto-manage-iot-central-from-portal/image0c1.png)

填寫表單中的所有欄位。 此表單類似於您填妥以在 IoT Central [Application Manager](https://aka.ms/iotcentral) 頁面上建立應用程式的表單。 如需詳細資訊，請參閱[建立 IoT Central 應用程式](quick-deploy-iot-central.md)快速入門。

![管理入口網站：建立 IoT Central 資源](media/howto-manage-iot-central-from-portal/image1a.png)  

填寫所有欄位之後, 請選取 [**建立**]。

## <a name="manage-existing-iot-central-applications"></a>管理現有的 IoT Central 應用程式

如果您已有 Azure IoT Central 應用程式，則可以將其刪除，或移至 Azure 入口網站中的其他訂用帳戶或資源群組。

> [!NOTE]
> 您無法查看 Azure 入口網站中的試用版應用程式，因為這類應用程式並未與您的訂用帳戶相關聯。

若要開始使用, 請選取左側主導覽功能表中的 [**所有資源**]。 使用搜尋方塊鍵入您應用程式的名稱，以在您的資源清單中找到該名稱。 然後選取您想要管理的 IoT Central 應用程式。

![管理入口網站：資源管理](media/howto-manage-iot-central-from-portal/image2a.png)

若要流覽至應用程式, 請選取 [IoT Central 應用程式 URL]。

![管理入口網站：資源管理](media/howto-manage-iot-central-from-portal/image3.png)

若要將應用程式移至不同的資源群組, 請選取資源群組旁的 [**變更**]。 在 [移動資源]  頁面上，挑選要將此應用程式遷移至的資源群組。

![管理入口網站：資源管理](media/howto-manage-iot-central-from-portal/image4a.png)

若要將應用程式移至不同的訂用帳戶, 請選取訂用帳戶旁邊的**變更**連結。 在出現的對話方塊中，選擇要將此應用程式遷移至的訂用帳戶。

![管理入口網站：資源管理](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>後續步驟

您現在已了解如何從 Azure 入口網站中管理 Azure IoT Central 應用程式，以下是建議的後續步驟：

> [!div class="nextstepaction"]
> [管理您的應用程式](howto-administer.md)