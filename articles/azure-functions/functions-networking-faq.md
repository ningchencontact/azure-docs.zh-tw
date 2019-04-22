---
title: 在 Azure Functions 中的網路功能的相關常見問題的解答
description: 最常見的問題和案例的網路功能搭配 Azure Functions 的一些解答。
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.service: azure-functions
ms.topic: troubleshooting
ms.date: 4/11/2019
ms.author: alkarche, glenga
ms.openlocfilehash: 3cf6a0d080e2d8cafcab8e69a614b59a470c7aba
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2019
ms.locfileid: "59682197"
---
# <a name="frequently-asked-questions-about-networking-in-azure-functions"></a>在 Azure Functions 中的網路功能的相關常見問題的解答

本文列出 Azure Functions 中的網路功能的相關常見問題的解答。 如需更完整的概觀，請參閱[函式網路功能選項](functions-networking-options.md)。

## <a name="how-do-i-set-a-static-ip-in-functions"></a>如何設定函式中的靜態 ip 位址？

部署 App Service Environment 中的函式是目前唯一的方式有您的函式的輸入和輸出靜態 IP。 如需使用 App Service Environment 的詳細資訊，開始使用發行項[建立及使用內部負載平衡與 App Service Environment](../app-service/environment/create-ilb-ase.md)。

## <a name="how-do-i-restrict-internet-access-to-my-function"></a>如何在我的函式來限制網際網路存取？

您可以限制網際網路存取數種方式：

* [IP 限制](../app-service/app-service-ip-restrictions.md):函式應用程式限制輸入的流量的 IP 範圍。
* 移除所有的 HTTP 觸發程序。 對於某些應用程式，就已足夠只要避免 HTTP 觸發程序，並觸發您的函式中使用任何其他的事件來源。

請記住 Azure 入口網站的編輯器，需要您執行的函式的直接存取。 您用來瀏覽入口網站，將其 IP 允許清單中的裝置時，會需要變更任何程式碼，透過 Azure 入口網站。 但您仍然可以使用平台功能 索引標籤底下的任何項目使用中適當的網路限制。

## <a name="how-do-i-restrict-my-function-app-to-a-virtual-network"></a>如何在虛擬網路限制我的函式應用程式？

完全限制函式，使所有的流量流經虛擬網路的唯一方式是使用內部負載平衡 App Service 環境。 此選項會部署您的網站上的虛擬網路內的專用基礎結構，並將傳送所有的觸發程序和透過虛擬網路的流量。 

如需使用 App Service Environment 的詳細資訊，開始使用發行項[建立及使用內部負載平衡與 App Service Environment](../app-service/environment/create-ilb-ase.md)。

## <a name="how-can-i-access-resources-in-a-virtual-network-from-a-function-app"></a>如何存取函數應用程式中的虛擬網路中的資源？

您可以使用虛擬網路整合，從執行的函式存取中的虛擬網路的資源。 如需詳細資訊，請參閱 <<c0> [ 虛擬網路整合](functions-networking-options.md#virtual-network-integration)。

## <a name="how-do-i-access-resources-protected-by-service-endpoints"></a>如何存取受保護的服務端點資源？

（目前處於預覽階段） 中使用虛擬網路整合，您可以從執行的函式來存取服務端點保護的資源。 如需詳細資訊，請參閱 <<c0> [ 預覽的虛擬網路整合](functions-networking-options.md#preview-version-of-virtual-network-integration)。

## <a name="how-can-i-trigger-a-function-from-a-resource-in-a-virtual-network"></a>如何觸發的函式，從虛擬網路中的資源？

只要將您的函式應用程式部署至 App Service Environment，您可以觸發的函式，從虛擬網路中的資源。 如需使用 App Service Environment 的詳細資訊，請參閱 <<c0> [ 建立及使用內部負載平衡與 App Service Environment](../app-service/environment/create-ilb-ase.md)。


## <a name="how-can-i-deploy-my-function-app-in-a-virtual-network"></a>如何部署我的虛擬網路中的函式應用程式？

部署至 App Service Environment 是唯一的方法來建立完全是在虛擬網路內的函式應用程式。 如需在 App Service 環境中使用內部負載平衡器的詳細資訊，文章開始[建立及使用內部負載平衡與 App Service Environment](https://docs.microsoft.com/azure/app-service/environment/create-ilb-ase)。

您需要只單向存取虛擬網路資源，或小於完整的網路隔離的情況下，請參閱[函式網路功能概觀](functions-networking-options.md)。

## <a name="next-steps"></a>後續步驟

若要深入了解網路功能和函式： 

* [在本教學課程，解如何開始使用虛擬網路整合](./functions-create-vnet.md)
* [深入了解在 Azure Functions 中網路功能選項](./functions-networking-options.md)
* [深入了解與 App Service 及 Functions 的虛擬網路整合](../app-service/web-sites-integrate-with-vnet.md)
* [深入了解在 Azure 中的虛擬網路](../virtual-network/virtual-networks-overview.md)
* [啟用更多的網路功能和使用 App Service 環境的控制](../app-service/environment/intro.md)
