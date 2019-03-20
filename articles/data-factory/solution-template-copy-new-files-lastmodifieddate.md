---
title: 複製新的和變更檔案，藉由使用 Azure Data Factory 的 LastModifiedDate |Microsoft Docs
description: 了解如何使用解決方案範本複製新的和變更檔案，藉由使用 Azure Data Factory 的 LastModifiedDate。
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 3/8/2019
ms.openlocfilehash: cae75f4d64c8b3f74cc40e94a675c0f10a6bd9ec
ms.sourcegitcommit: f596d88d776a3699f8c8cf98415eb874187e2a48
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/16/2019
ms.locfileid: "58111934"
---
# <a name="copy-new-and-changed-files-by-lastmodifieddate-with-azure-data-factory"></a>複製新的和變更檔案，藉由使用 Azure Data Factory 的 LastModifiedDate

此文章說明您可用來將新的和變更的檔案，只能由 LastModifiedDate 從檔案型存放區複製到 目的地存放區的解決方案範本。 

## <a name="about-this-solution-template"></a>關於此解決方案範本

此範本會先選取新的和變更檔案僅供其屬性**LastModifiedDate**，然後將這些選取的檔案從資料來源存放區複製到資料目的地存放區。

範本包含一個活動：
- **複製**只能由 LastModifiedDate 新增和變更的檔案複製到目的地存放區的檔案存放區。

範本會定義四個參數：
-  *FolderPath_Source*是您可以在其中讀取來源存放區檔案的資料夾路徑。 您需要將預設值替換為您自己的資料夾路徑。
-  *FolderPath_Destination*是您要將檔案複製到目的地存放區的資料夾路徑。 您需要將預設值替換為您自己的資料夾路徑。
-  *LastModified_From*用來選取其 LastModifiedDate 屬性後的檔案或等於此日期時間值。  若要選取新的檔案，它尚未複製過去的時間，這個日期時間值可以是管線時觸發上一次的時間。 您可以取代預設值 ' 2019年-02-01T00:00:00Z' 以您預期 LastModifiedDate UTC 時區。 
-  *LastModified_To*用來選取其 LastModifiedDate 屬性是在這個日期時間值之前的檔案。 若要選取新的檔案，它尚未複製過去的時間，這個日期時間值可以是當前的時間。  您可以取代預設值 ' 2019年-02-01T00:00:00Z' 以您預期 LastModifiedDate UTC 時區。 

## <a name="how-to-use-this-solution-template"></a>如何使用此解決方案範本

1. 移至範本**複製新的檔案，只能由 LastModifiedDate**。 建立**新增**連接到您來源儲存體存放區。 來源儲存體存放區是您要複製的檔案。

    ![建立與來源的新連線](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate1.png)
    
2. 第一次選取的儲存體**型別**。 之後，輸入儲存體**帳戶名稱**並**帳戶金鑰**。 最後，選取**完成**。

    ![輸入連接字串](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate2.png)
    
3. 建立**新增**連接至您的目的地存放區。 目的地存放區是您想要將檔案複製到的位置。 您也必須輸入類似的資料目的地存放區的連接資訊，您未在步驟 2。

    ![建立與目的地的新連線](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate3.png)

4. 選取 **使用此範本**。

    ![使用此範本](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate4.png)
    
5. 您會在面板中看見可用的管線，如下列範例所示：

    ![顯示管線](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate5.png)

6. 選取**偵錯**，如將值寫入**參數**，然後選取**完成**。  下圖中設定參數如下所示。
   - **FolderPath_Source** = **/source/**
   - **FolderPath_Destination** = **/destination/**
   - **LastModified_From** =  **2019-02-01T00:00:00Z**
   - **LastModified_To** = **2019-03-01T00:00:00Z**
    
     此範例，代表上次修改之間的時間範圍內的檔案*2019年-02-01T00:00:00Z*並*2019年-03-01T00:00:00Z*將從資料夾複製 */source/* 的資料夾 */destination/*。  您可以取代這些項目與您自己的參數。
    
     ![執行管道](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate6.png)

7. 檢閱結果。 您會看到只將檔案中設定上次修改時間範圍已複製到目的地存放區。

    ![檢閱結果](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate7.png)
    
8. 現在您可以新增自動執行此管線中，輪轉視窗觸發程序，讓管線可以永遠會定期新增和變更的檔案，只能由 LastModifiedDate 複製。  選取 **新增觸發程序**，然後選取**新增/編輯**。

    ![檢閱結果](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate8.png)
    
9. 在 [新增觸發程序] 視窗中，選取 [+ 新增]。

    ![檢閱結果](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate9.png)

10. 選取**輪轉視窗**做為觸發程序類型，設定**每隔 15 分鐘**為 週期性 （您可以變更任何的間隔時間），然後選取**下一步**。

    ![建立觸發程序](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate10.png)    
    
11. 寫入的值**觸發程序執行參數**做為下列程式碼，然後選取**完成**。
    - **FolderPath_Source** = **/source/**。  您可以取代您在來源資料存放區中的資料夾。
    - **FolderPath_Destination** = **/destination/**。  您可以取代您在 目的地資料存放區中的資料夾。
    - **LastModified_From** =  **@trigger（)。 outputs.windowStartTime**。  它是系統變數，從觸發程序判斷管線時觸發上一次的時間。
    - **LastModified_To** = **@trigger（)。 outputs.windowEndTime**。  它是系統變數決定當觸發管線這段時間的觸發程序。
    
    ![輸入參數](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate11.png)
    
12. 選取 [全部發佈]。
    
    ![全部發佈](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate12.png)

13. 您的資料來源存放區的來源資料夾中建立新的檔案。  您現在正在等候自動觸發的管線，並只將新的檔案會複製到目的地存放區。

14. 選取 **監視**在左側的導覽窗格中，索引標籤，然後等待約 15 分鐘的時間，如果已設定觸發程序的週期為每隔 15 分鐘。 

    ![選取 監視](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate14.png)

15. 檢閱結果。 您會看到您的管線會自動觸發每隔 15 分鐘，並只新增或變更檔案從來源存放區將會複製到目的地存放區中每個管線執行。

    ![檢閱結果](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate15.png)
    
## <a name="next-steps"></a>後續步驟

- [Azure Data Factory 簡介](introduction.md)