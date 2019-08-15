---
title: 管理版本-LUIS
titleSuffix: Azure Cognitive Services
description: 版本可讓您建置及發佈不同的模型。 理想的做法是先將目前作用中的模型複製到不同的應用程式版本，再對模型進行變更。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 982dbf3555648c6a7c82da90a62740a584473c1e
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932840"
---
# <a name="use-versions-to-edit-and-test-without-impacting-staging-or-production-apps"></a>使用版本進行編輯和測試而不影響預備或生產應用程式

版本可讓您建置及發佈不同的模型。 理想的做法是先將目前作用中的模型複製到不同的應用程式[版本](luis-concept-version.md)，再對模型進行變更。 

若要使用版本，請在 [我的應用程式] 頁面上選取應用程式的名稱來開啟應用程式，接著選取頂端列中的 [管理]，然後選取左側導覽中的 [版本]。 

版本清單會顯示發行的版本、發行的位置, 以及目前使用中的版本。 

[![管理區段, 版本頁面](./media/luis-how-to-manage-versions/versions-import.png "管理區段, 版本頁面")](./media/luis-how-to-manage-versions/versions-import.png#lightbox)

## <a name="clone-a-version"></a>複製版本

1. 選取您要複製的版本，然後從工具列中選取 [複製]。 

2. 在 [複製版本] 對話方塊中，輸入新版本的名稱，例如 "0.2"。

   ![[複製版本] 對話方塊](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)
 
     > [!NOTE]
     > 版本識別碼只能包含字元、數字或 '.'，且長度不能超過 10 個字元。
 
   隨即會建立具有指定名稱的新版本，並會設定為使用中版本。

## <a name="set-active-version"></a>設定使用中版本

從清單中選取版本，然後從工具列中選取 [設成作用中]。 

[![管理區段, 版本頁面, 使用版本動作](./media/luis-how-to-manage-versions/versions-other.png "管理 區段, 版本頁面, 使用版本動作")](./media/luis-how-to-manage-versions/versions-other.png#lightbox)

## <a name="import-version"></a>匯入版本

1. 從工具列中選取 [匯入版本]。 

2. 在 [匯入新版本] 快顯視窗中，輸入新的版本名稱 (10 個字元)。 如果應用程式中已有 JSON 檔案中的版本，您就只需要設定版本識別碼。

    ![管理區段, 版本頁面, 匯入新版本](./media/luis-how-to-manage-versions/versions-import-pop-up.png)

    匯入版本之後，新版本就會變成使用中的版本。

### <a name="import-errors"></a>匯入錯誤

* Tokenizer 錯誤:如果您在匯入時收到**tokenizer 錯誤**, 表示您嘗試匯入的版本所使用的[tokenizer](luis-language-support.md#custom-tokenizer-versions)與應用程式目前所使用的不同。 若要修正此問題, 請參閱[在 tokenizer 版本之間進行遷移](luis-language-support.md#migrating-between-tokenizer-versions)。

<a name = "export-version"></a>

## <a name="other-actions"></a>其他動作

* 若要**刪除**版本，請從清單中選取版本，然後從工具列中選取 [刪除]。 選取  **Ok**。 
* 若要**重新命名**版本，請從清單中選取版本，然後從工具列中選取 [重新命名]。 輸入新的名稱，然後選取 [完成]。 
* 若要**匯出**版本，請從清單中選取版本，然後從工具列中選取 [匯出應用程式]。 選擇 [JSON] 匯出以進行備份, 選擇 [**匯出容器**] 以[在 LUIS 容器中使用此應用程式](luis-container-howto.md)。  

