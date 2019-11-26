---
title: 如何使用 Azure Machine Learning 的資料標籤工具
title.suffix: Azure Machine Learning
description: 本文會指導您如何在 Azure Machine Learning 標籤專案中使用資料標記工具。
author: lobrien
ms.author: laobri
ms.service: machine-learning
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 9cbebb905ba39dabaf905be2fa741702bd1a0088
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838948"
---
# <a name="how-to-tag-images-in-a-labeling-project"></a>如何標記標籤專案中的影像

當您的專案系統管理員在 Azure Machine Learning 中建立標籤專案之後，您就可以使用標籤工具快速準備機器學習專案的資料。 

> [!div class="checklist"]
> * 如何存取您的標籤專案
> * 標籤工具
> * 如何使用特定標籤工作的工具

## <a name="prerequisites"></a>必要條件

* 執行中資料標籤專案的標籤入口網站 URL
* [Microsoft 帳戶](https://account.microsoft.com/account)或
* 組織和專案的 Azure Active Directory 帳戶

> [!Note]
> 專案系統管理員可以在 [專案詳細資料]  頁面中的 [詳細資料]  索引標籤上找到標籤入口網站 URL。 

## <a name="logging-in-to-the-projects-labeling-portal"></a>登入專案的標籤入口網站

移至專案系統管理員為您提供的標籤入口網站 URL。 

使用系統管理員用來將您新增至小組的電子郵件帳戶進行登入。 對大部分的使用者而言，登入都可透過 Microsoft 帳戶來完成。 如果標籤專案使用 Azure Active Directory，您就會經由此途徑登入。 

## <a name="understanding-the-labeling-task"></a>了解標籤工作

登入之後，您將會進入專案的概觀頁面。 

首要工作是**檢視詳細指示**。 這些指示專屬於您的專案，將說明您將看到的資料類型、您應如何做出決策，以及其他相關資訊。 完成作業後，請返回專案頁面，並選擇 [開始加上標籤]  。

## <a name="common-features-of-the-labeling-task"></a>標籤工作的一般功能

所有的影像標籤工作都牽涉到從專案系統管理員所指定的集合中選擇適當的一或多個標籤。 您可以使用鍵盤上的數字鍵來選取前九個標籤。  

影像分類工作可讓您選擇同時顯示多個影像。 您可以使用影像區域上方的圖示選擇不同的版面配置。 按下 [全選]  按鈕，可同時選取所有顯示的影像。 使用影像區域右上方的圓形選取按鈕，可選取個別相片。 您至少須選取一個影像來套用標籤。 如果您選取了多個影像，則在選取標籤後，該標籤將會套用至每張選取的相片。

在此，我們選擇了 2x2 版面配置，且要將「哺乳類動物」標籤套用至熊和虎鯨的影像。 含有鯊魚的影像已標記為「軟骨魚類」，而鬣蜥尚未標記。

![多個影像版面配置和選取項目](media/how-to-label-images/layouts.png)

> [!Important] 
> 只有在處理全新而未標記的資料頁面時，才可切換版面配置。 切換版面配置會清除頁面上進行中的標記工作。 

當您已標記頁面上所有的影像時，Azure 即會啟用 [提交]  按鈕。 按 [提交]  會儲存您的工作。 

在您提交手邊資料的標籤後，Azure 將會以工作佇列中的一組新影像來重新整理頁面。  

## <a name="tagging-images-for-multi-class-classification"></a>標記影像以進行多類別分類

如果您的專案屬於「影像分類多類別」類型，您將會為整個影像指派單一標籤。 無論何時，您都可以選擇 [指示]  頁面並瀏覽至 [檢視詳細指示]  ，以查看專案特定的指引。 

如前所述，您可以從數個呈現影像的版面配置中擇一使用。 在選取影像並為其指派標籤後，如果您發現過程有誤，可以進行修正。 在顯示於影像下方的標籤中按一下 `X` 目標，就會清除標籤。 或者，如果您在選取影像後選擇另一個類別，標籤就會切換至新選取的值。

## <a name="tagging-images-for-multi-label-classification"></a>標記影像以進行多標籤分類

如果您處理屬於「影像分類多標籤」類型的專案，您將會把_一或多個_標籤套用至影像。 無論何時，您都可以選擇 [指示]  頁面並瀏覽至 [檢視詳細指示]  ，以查看專案特定的指引。 

選取要加上標籤的影像，然後按一下標籤。 選擇標籤後，該標籤將會套用至所有選取的影像，並將其取消選取。 若要套用更多標籤，您必須重新選擇影像。 下方的動畫顯示多標籤的標記頁面：

* 使用 [全選]  套用「海洋」標籤
* 選取單一影像，並將其標記為「特寫」
* 選取三個影像，並將其標記為「廣角」

![顯示多標籤流程的動畫](media/how-to-label-images/multilabel.gif)

若要更正錯誤，您可以按一下 `X` 以清除個別標籤，或是選取影像並選擇標籤，這將會從所有選取的影像中清除標籤。 範例中即顯示此案例，在按一下「陸地」後，即會從兩個選取的影像中清除該標籤。

![顯示取消選取多個項目的螢幕擷取畫面](media/how-to-label-images/multiple-deselection.png)

當您對每個影像套用至少一個標籤後，Azure 才會啟用 [提交]  按鈕。 按 [提交]  可儲存您的工作。

## <a name="tagging-images-and-bounding-boxes-for-object-detection"></a>標記影像和週框方塊以進行物件偵測

如果您的專案屬於「物件識別 (週框方塊)」類型，您將在影像中指定一或多個週框方塊，並將標籤套用至該方塊。 一個影像可以有多個週框方塊，而每個方塊分別有單一標籤。 請使用 [檢視詳細指示]  來判斷您的專案是否適合新增多個週框方塊。

1. 為您要建立的週框方塊選取標籤
1. 選取**矩形方塊**工具 ![矩形方塊工具](media/how-to-label-images/rectangular-box-tool.png)，或按 'R' 
1. 在您的目標上按住滑鼠並往對角拖曳，以建立粗略的週框方塊
    * 按住方塊的邊緣或角落並加以拖曳，以調整週框方塊

![建立基本週框方塊的螢幕擷取畫面](media/how-to-label-images/bounding-box-sequence.png)

如果您想要刪除週框方塊，請在建立之後，按一下出現在週框方塊旁的 X 形目標。

您無法重新指派現有週框方塊的標籤。 如果您的標籤指派有誤，您必須刪除週框方塊，並以正確的標籤建立新的方塊。

根據預設，您可以編輯現有的週框方塊。 **鎖定/解除鎖定區域**工具 ![鎖定/解除鎖定區域工具](media/how-to-label-images/lock-bounding-boxes-tool.png) 或 "L" 會切換該行為。 如果區域已鎖定，您就只能變更新週框方塊的形狀或位置。

使用**區域操作**工具 ![區域操作工具](media/how-to-label-images/regions-tool.png) 或 "M" 可調整現有的週框方塊。 您可以按住邊緣或角落並加以拖曳，以調整形狀。 按住內部，可以拖曳整個週框方塊。 如果您無法編輯區域，表示您可能切換了**鎖定/解除鎖定區域**工具。 

使用**範本型方塊**工具 ![範本方塊工具](media/how-to-label-images/template-box-tool.png) 或 "T"，可建立相同大小的多個週框方塊。 如果影像沒有週框方塊，當您啟用範本型方塊後，此工具將會產生50x50 像素的方塊。 如果您已建立週框方塊，然後啟用了範本型方塊，則新週框方塊的大小將會與您所建立的最後一個方塊相同。 範本型方塊在放置後可調整大小。 調整範本型方塊的大小時，只會調整特定方塊的大小。 

如果您想要刪除目前影像中的_所有_週框方塊，您可以選擇**刪除所有區域**工具 ![刪除區域](media/how-to-label-images/delete-regions-tool.png)工具。 

建立影像的週框方塊後，請按 [提交]  以儲存您的工作。 必須按下 [提交]  按鈕，您的工作才會儲存。 

## <a name="finishing-up"></a>即將完成 

當您提交已標記資料的頁面時，Azure 會為您指派工作佇列中未標記的新資料。 如果沒有其他未標記的資料，您將會看到其效應的訊息，以及返回入口網站首頁的連結。 

如果您確知不會再執行更多標籤工作，請在標籤入口網站的右上角選擇您的名稱，然後選擇 [登出]  。 如果您未登出，Azure 最終將會「使您逾時」，並將您的資料指派給其他標籤人員。 

## <a name="next-steps"></a>後續步驟

* 了解如何[在 Azure 中將影像分類模型定型](https://docs.microsoft.com/azure/machine-learning/service/tutorial-train-models-with-aml)
* 了解如何[使用 Azure 和 Faster R-CNN 技術進行物件偵測](https://www.microsoft.com/developerblog/2017/10/24/bird-detection-with-azure-ml-workbench/)