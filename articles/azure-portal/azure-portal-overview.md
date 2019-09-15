---
title: Azure 入口網站概觀 | Microsoft Docs
description: 瞭解如何流覽 Azure 入口網站並使用它來管理服務
services: azure-portal
keywords: ''
author: kfollis
ms.author: kfollis
ms.date: 05/24/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 6e176a8b16129cd35fc011e14fcb36038f7c0144
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2019
ms.locfileid: "71000348"
---
# <a name="azure-portal-overview"></a>Azure 入口網站概觀

本文介紹 Azure 入口網站、識別入口網站頁面元素，並協助您熟悉 Azure 入口網站管理體驗。

## <a name="what-is-the-azure-portal"></a>Azure 入口網站是什麼？

Azure 入口網站是以網頁為基礎的統一主控台，可提供命令列工具的替代方法。 使用 Azure 入口網站，您可以使用圖形化使用者介面來管理 Azure 訂用帳戶。 您可以建立、管理及監視從簡單 web 應用程式到複雜雲端部署的所有專案、建立組織中資源的自訂儀表板，以及設定最佳體驗的協助工具選項。

Azure 入口網站是針對復原和持續可用性所設計。 它在每個 Azure 資料中心都有存在，因此可彈性地進行個別的資料中心失敗，也可避免接近使用者的網路變慢。 Azure 入口網站會持續更新，而且不需要停機維護活動。

## <a name="azure-home"></a>Azure 首頁

身為 Azure 服務的新訂閱者，您在登[入入口網站](https://portal.azure.com)之後看到的第一件事是**azure 首頁**。 此頁面會編譯資源，協助您充分利用您的 Azure 訂用帳戶。 我們已包含免費線上課程、檔、核心服務和實用網站的連結，可讓您保持最新狀態並管理貴組織的變更。 為了讓您快速且輕鬆地存取進行中的工作，我們也會顯示您最近流覽過的資源清單。 您無法自訂此頁面，但可以選擇是否要看到**Azure 首頁**或**azure 儀表板**做為預設的視圖。 第一次登入時，頁面頂端會出現提示，您可以在其中儲存您的喜好設定。

![顯示預設視圖選取器的螢幕擷取畫面](./media/azure-portal-overview/azure-portal-default-view.png)

## <a name="azure-dashboard"></a>Azure 儀表板

儀表板會針對您的訂用帳戶中最重要的資源，提供焦點的觀點。 我們為您提供了預設儀表板，可讓您開始使用。 您可以自訂此儀表板，將您最常使用的資源帶入單一視圖中。 您對預設視圖進行的任何變更都會影響您的體驗。 不過，您可以建立其他儀表板供自己使用，或發佈自訂儀表板，並與組織中的其他使用者共用。 如需詳細資訊，請參閱在[Azure 入口網站中建立和共用儀表板](../azure-portal/azure-portal-dashboards.md)。

## <a name="getting-around-the-portal"></a>開始流覽入口網站

瞭解基本入口網站配置，以及如何與它互動是很有説明的。 在這裡，我們將介紹使用者介面的元件，以及我們用來提供指示的一些術語。 如需更詳細的入口網站導覽，請參閱課程：[流覽入口網站](https://docs.microsoft.com/learn/modules/tour-azure-portal/3-navigate-the-portal)。

[Azure 入口網站] 提要欄位和 [頁首] 是一律存在的全域元素。 這些持續性功能是與每個個別服務或功能相關聯之使用者介面的「shell」，而標頭則提供對全域控制項的存取權。 資源的設定頁面（有時稱為「分頁」）可能也有左窗格可協助您在功能之間移動。

下圖示示 Azure 入口網站的基本專案，下表將說明每個元素。

![螢幕擷取畫面：顯示全螢幕入口網站視圖和 UI 元素的按鍵](./media/azure-portal-overview/azure-portal-fullscreen-map.png)

|Key|描述
|:---:|---|
|1|頁首。 會出現在每個入口網站頁面的頂端，並保留全域元素。|
|2| 全域搜尋。 使用 [搜尋] 列來快速尋找特定的資源、服務或檔。|
|3|全域控制項。 就像所有的全域元素一樣，這些功能會在入口網站中持續存在，包括：Cloud Shell、訂用帳戶篩選、通知、入口網站設定、說明及支援，並將意見反應傳送給我們。|
|4|您的帳戶。 請參閱您帳戶的相關資訊、切換目錄、登出，或使用不同的帳戶登入。|
|5|資訊看板. 提要欄位是一個全域元素，可協助您在服務之間流覽。 提要欄位可以折迭，讓工作窗格擁有更多焦點。|
|6|用來在目前訂用帳戶中建立新資源的主要控制項。 搜尋或流覽您想要建立之資源類型的 Azure Marketplace。|
|7|[我的最愛] 清單。 新增或移除 [**所有服務**] 頁面中的 [我的最愛]。|
|8|左窗格。 許多服務都包含左窗格功能表，可協助您管理服務。|
|9|命令列。 命令列上的控制項會與您目前的焦點相關。|
|10|快捷. 您可以使用階層連結連結，在工作流程中向後移動一層。|
|11|工作窗格。  顯示目前處於焦點之資源的詳細資料。|

## <a name="get-started-with-services"></a>開始使用服務

如果您是新的訂閱者，您必須先建立資源，才能管理任何專案。 選取 [ **+ 建立資源**] 以查看 Azure Marketplace 中可用的服務。 您會在這裡找到數百個提供者的應用程式和服務，全都經過認證可在 Azure 上執行。

我們已在提要欄位中預先填入我的最愛，並提供常用服務的連結。  若要查看所有可用的服務，請從側邊欄選取 [**所有服務**]。

> [!TIP]
> 尋找資源、服務或檔的最快方式是使用全域標頭中的 [*搜尋*]。 使用階層連結來返回先前的頁面。
>
觀看這段影片以取得如何在 Azure 入口網站中使用全域搜尋的示範。


> [!VIDEO https://www.youtube.com/embed/nZ7WwTZcQbo]

[如何在 Azure 入口網站中使用全域搜尋](https://www.youtube.com/watch?v=nZ7WwTZcQbo)

## <a name="next-steps"></a>後續步驟

* 深入瞭解在[支援的瀏覽器和裝置](../azure-portal/azure-portal-supported-browsers-devices.md)中執行 Azure 入口網站的位置

* 透過[Azure 行動應用程式](https://azure.microsoft.com/features/azure-portal/mobile-app/)隨時保持連線
