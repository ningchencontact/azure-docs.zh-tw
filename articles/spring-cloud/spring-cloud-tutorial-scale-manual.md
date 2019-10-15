---
title: 教學課程：在 Azure Spring Cloud 中調整應用程式 | Microsoft Docs
description: 在此教學課程中，您將了解如何在 Azure 入口網站的 Azure Spring Cloud 中調整應用程式
services: spring-cloud
ms.service: spring-cloud
ms.topic: tutorial
ms.reviewer: jeconnoc
ms.author: v-vasuke
author: v-vasuke
ms.date: 10/06/2019
ms.openlocfilehash: bca88cac45e1ba8117eb4e10141e32d621434b86
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038630"
---
# <a name="tutorial-scale-an-application-in-azure-spring-cloud"></a>教學課程：在 Azure Spring Cloud 中調整應用程式

此教學課程示範如何使用 Azure 入口網站中的 Azure Spring Cloud 儀表板來調整任何微服務應用程式。 修改虛擬 CPU (vCPU) 數目和記憶體數量，以相應增加或減少您的應用程式。 修改應用程式執行個體的數目，將您的應用程式相應縮小或放大。 當您完成時，您會知道如何對服務中的每個應用程式進行快速的手動調整。 調整能在數秒內生效，且不需要進行任何程式碼變更或重新部署。

## <a name="prerequisites"></a>必要條件

若要完成本教學課程，您需要：
* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
* 已部署的 Azure Spring Cloud 服務執行個體。  請遵循我們的[快速入門](spring-cloud-quickstart-launch-app-cli.md)以開始使用。
* 至少有一個應用程式已在該服務執行個體中建立。


## <a name="navigate-to-the-scale-page-in-the-azure-portal"></a>在 Azure 入口網站中，瀏覽至 [調整] 頁面

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 瀏覽至您的 Azure Spring Cloud 的 [概觀]  頁面。

1. 移至左側功能表中 [應用程式]  索引標籤的 [設定]  標題底下。

1. 選取您要調整的應用程式。 在此範例中，我們將會調整名為 "account-service" 的應用程式。 這應該會帶您前往應用程式的 [概觀]  頁面。

1. 移至左側功能表中 [調整]  索引標籤的 [設定]  標題底下。 您應該會看到一份表單，其中包含我們稍早提到的每個調整屬性行。

## <a name="scale-your-application"></a>調整您的應用程式

您可以修改調整屬性。 請記住下列注意事項。

* **CPU**：每個應用程式執行個體所允許的 CPU 數目上限為 4。 應用程式的 CPU 總數將會是此處所設定的值乘以應用程式執行個體的數目。

* **記憶體/GB**：每個應用程式執行個體所允許的最大記憶體數量為 8GB。  應用程式的記憶體總數將會是此處所設定的值乘以應用程式執行個體的數目。

* **執行個體計數**：在標準層中，您可以擴充至 20 個執行個體。 此值會變更微服務應用程式個別執行中執行個體的數目。

請務必按一下 [儲存]  按鈕以套用您的調整設定。

幾秒鐘之後，您所做的變更將會顯示在 [概觀]  頁面中，並在 [應用程式執行個體]  索引標籤中提供更多詳細資料。調整不需要進行任何程式碼變更或重新部署。

## <a name="next-steps"></a>後續步驟

在此教學課程中，您已了解如何手動調整 Azure Spring Cloud 應用程式。  若要了解如何監控應用程式，請繼續進行下一個教學課程。

> [!div class="nextstepaction"]
> [了解如何監控應用程式](spring-cloud-tutorial-distributed-tracing.md)
