---
title: 如何提供資料意見反應給 Azure 地圖服務 |Microsoft Docs
description: 使用 Azure 地圖服務意見反應工具提供資料意見反應。
author: walsehgal
ms.author: v-musehg
ms.date: 08/19/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 076f98cb240014bcc88a395902203413e31fe0f1
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642119"
---
# <a name="provide-data-feedback-to-azure-maps"></a>提供資料意見反應給 Azure 地圖服務

Azure 地圖服務自2018年5月起正式推出, 提供最新的地圖資料、容易使用的 REST Api 和強大的 Sdk, 以支援企業客戶在各種不同的商務使用案例上。 真實世界正在每秒進行變更, 因此為我們的客戶提供實際的數位代表非常重要。 打算開啟或關閉設施的客戶必須確保我們的地圖能夠立即更新, 讓他們可以有效率地規劃在適當設施的傳遞、維護或客戶服務。 我們建立了 Azure 地圖服務資料意見反應工具, 讓我們的客戶能夠提供直接的資料意見反應。 客戶的資料意見反應會直接傳送給我們的資料提供者及其地圖編輯器, 他們可以快速評估並併入我們的對應產品中的意見反應。  

[Azure 地圖服務資料意見反應工具](https://feedback.azuremaps.com)提供簡單的方式, 讓我們的客戶提供地圖資料的意見反應, 特別是在感關注的業務點和住家住址。 本文會引導您瞭解如何使用 Azure 地圖服務意見反應工具來提供不同類型的意見反應。

## <a name="add-a-business-place-or-a-residential-address"></a>新增公司場所或住家住址 

您可能想要在地圖上提供缺少的感點或住家住址的意見反應。 有兩種方式可執行此動作, 請開啟 Azure 地圖資料意見反應工具, 然後搜尋遺漏位置的座標, 再按一下 [新增位置]

  ![搜尋缺少的位置](./media/how-to-use-feedback-tool/search-poi.png)

或者, 您可以與地圖互動, 並按一下位置以在座標處放置釘選, 然後按一下 [新增位置]。 

  ![新增 pin](./media/how-to-use-feedback-tool/add-poi.png)

按一下時, 系統會將您導向至表單, 以提供位置的對應詳細資料。

  ![新增位置](./media/how-to-use-feedback-tool/add-a-place.png)

## <a name="fix-a-business-place-or-a-residential-address"></a>修正公司場所或住家住址 

意見反應工具也可讓您搜尋並找出公司地點或位址, 並提供意見反應來修正位址或 pin 位置 (如果不正確的話)。 若要提供意見反應以修正位址, 請使用搜尋列來搜尋營業地點或住家住址。 從 [結果] 清單中, 按一下您感關注的位置, 然後按一下 [修正此位置]。

  ![要修正的搜尋位置](./media/how-to-use-feedback-tool/fix-place.png)

若要提供意見反應以修正位址, 請填寫 [修正位置] 表單, 然後按一下 [提交] 按鈕。

  ![修正表單](./media/how-to-use-feedback-tool/fix-form.png)

如果位置的 pin 位置錯誤, 請核取 [修正位置] 表單上指出「pin 位置不正確」的核取方塊, 並將釘選移到正確的位置, 然後按一下 [提交] 按鈕。

  ![移動釘選位置](./media/how-to-use-feedback-tool/move-pin.png)

## <a name="add-a-comment"></a>新增註解 

除了可讓您搜尋位置之外, 意見反應工具也可讓您新增自由格式文字批註, 以取得與位置相關的詳細資料。 若要新增位置的批註搜尋, 或按一下位置, 然後按一下 [新增批註], 書寫批註, 然後按一下 [提交]。 

  ![新增批註](./media/how-to-use-feedback-tool/add-comment.png)

## <a name="track-status"></a>追蹤狀態 

您也可以選取 [我想要追蹤狀態] 方塊, 並在提出要求時提供您的電子郵件, 以追蹤要求的狀態。 您會在電子郵件中收到追蹤連結, 為您的要求提供最新狀態。 

  ![意見反應狀態](./media/how-to-use-feedback-tool/feedback-status.png)


## <a name="next-steps"></a>後續步驟

若要張貼與 Azure 地圖服務相關的任何技術問題, 請造訪:

* [Azure 地圖服務 Stack Overflow](https://stackoverflow.com/questions/tagged/azure-maps)
* [Azure 地圖服務意見反應論壇](https://feedback.azure.com/forums/909172-azure-maps)