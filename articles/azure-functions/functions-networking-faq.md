---
title: 關於在 Azure Functions 中的網路功能的常見問題集
description: 最常見的問題和案例的網路功能搭配 Azure Functions 的一些解答。
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.service: azure-functions
ms.topic: troubleshooting
ms.date: 4/11/2019
ms.author: alkarche, glenga
ms.openlocfilehash: 126b9ccefedee1f5cefdac8a8666a58e7a4a1fef
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2019
ms.locfileid: "59548624"
---
# <a name="frequently-asked-questions-about-networking-in-azure-functions"></a>在 Azure Functions 中的網路功能的相關常見問題的解答

以下是網路相關常見問題的清單。 如需更完整的概觀，請參閱[函式網路功能選項的文件](functions-networking-options.md)

## <a name="how-do-i-set-a-static-ip-in-functions"></a>如何設定函式中的靜態 ip 位址？

部署 App Service 環境 (ASE) 中的函式是目前有靜態的輸入和輸出 IP 函式的唯一方式。 如需使用 ASE 的詳細資訊，請使用以下的文章啟動：[建立和使用 ILB ASE](../app-service/environment/create-ilb-ase.md)。

## <a name="how-do-i-restrict-internet-access-to-my-function"></a>如何在我的函式來限制網際網路存取？

您可以限制在數種方式下, 面所列的網際網路存取。

* [IP 限制](../app-service/app-service-ip-restrictions.md)： 限制函式應用程式的 IP 範圍的流量。
* 移除所有的 HTTP 觸發程序。 對於某些應用程式，便足以只要避免 HTTP 觸發程序，並觸發您的函式中使用任何其他的事件來源。

請記住，Azure 入口網站的編輯器會要求您執行的函式使用的直接存取可這麼做時，最重要的考量。 您用來瀏覽入口網站，將其 IP 允許清單中的裝置時，會需要變更任何程式碼，透過 Azure 入口網站。 您仍然可以不過，使用平台功能 索引標籤底下的任何項目使用中適當的網路限制。

## <a name="how-do-i-restrict-my-function-app-to-a-vnet"></a>如何在 vnet 限制我的函式應用程式？

完全限制函式，使所有的流量流經 VNET 的唯一方式是使用內部負載平衡 (ILB) App Service Environment (ASE)。 此選項會部署您的網站，在 VNET 內的專用基礎結構上，並將傳送所有的觸發程序和透過 VNET 的流量。 如需使用 ASE 的詳細資訊，請使用以下的文章啟動：[建立和使用 ILB ASE](../app-service/environment/create-ilb-ase.md)。

## <a name="how-can-i-access-resources-in-a-vnet-from-a-function-app"></a>如何從函式應用程式存取 VNET 中的資源？

從執行的函式，使用 VNET 整合，您可以存取 VNET 中的資源。 如需詳細資訊，請參閱[VNET 整合](functions-networking-options.md#vnet-integration)

## <a name="how-do-i-access-resources-protected-by-service-endpoints"></a>如何存取受保護的服務端點資源？

您可以使用新的 VNET 整合 （目前處於預覽狀態），來存取受保護資源從執行的函式的服務端點。 如需詳細資訊，請參閱 <<c0> [ 預覽 VNET 整合](functions-networking-options.md#preview-vnet-integration)。

## <a name="how-can-i-trigger-a-function-from-a-resource-in-a-vnet"></a>如何觸發的函式，從 VNET 中的資源？

您只可以將函式應用程式部署至 App Service 環境，以觸發從 VNET 中資源的函式。 如需使用 ASE 的詳細資訊，請參閱[建立和使用 ILB ASE](../app-service/environment/create-ilb-ase.md)。


## <a name="how-can-i-deploy-my-function-app-in-a-vnet"></a>如何部署我的 VNET 中的函式應用程式？

部署至 App Service Environment 是唯一的辦法建立完全位於 VNET 的詳細資料上使用 ILB ASE 中的函式應用程式，請從以下的文章開始：[建立和使用 ILB ASE](https://docs.microsoft.com/azure/app-service/environment/create-ilb-ase)。

只需要單向存取 VNET 的資源，或小於完整的網路隔離的情況下，請參閱[函式網路功能概觀](functions-networking-options.md)。

## <a name="next-steps"></a>後續步驟

若要深入了解網路和函式： 

* [請依照下列快速入門開始使用的 VNET 整合教學課程](./functions-create-vnet.md)
* [深入了解網路功能選項，在此處的函式](./functions-networking-options.md)
* [在此處深入了解與 App Service / Functions 的 VNET 整合](../app-service/web-sites-integrate-with-vnet.md)
* [深入了解 Azure 中的 VNET](../virtual-network/virtual-networks-overview.md)
* [啟用更多的網路功能和使用 App Service 環境的控制](../app-service/environment/intro.md)
