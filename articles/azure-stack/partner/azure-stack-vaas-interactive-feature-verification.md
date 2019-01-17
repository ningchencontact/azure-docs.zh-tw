---
title: Azure Stack 驗證即服務中的互動式功能驗證測試 | Microsoft Docs
description: 了解如何使用驗證即服務為 Azure Stack 建立互動式功能驗證測試。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/07/2019
ms.author: mabrigg
ms.reviewer: johnhas
ROBOTS: NOINDEX
ms.openlocfilehash: 5b20e170388aa6e9b73b7c5c26a286ec51b52209
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2019
ms.locfileid: "54245832"
---
# <a name="interactive-feature-verification-testing"></a>互動式功能驗證測試  

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

您可以使用互動式功能驗證測試架構來要求測試您的系統。 當您要求測試時，Microsoft 會使用該架構來準備需要手動執行互動步驟的測試。 Microsoft 可以使用該架構將數個獨立的自動化測試鏈結在一起。

本文將說明簡單的手動案例。 此測試會檢查 Azure Stack 中的磁碟更換。 而架構會收集每個步驟的診斷記錄。 您可以在找到問題時，對問題進行偵錯。 此架構也允許共用由其他工具或程序產生的記錄，並讓您針對案例提供意見反應。

## <a name="overview-of-a-test-pass"></a>測試進行的概觀

磁碟更換的測試是常見案例。 在此範例中，測試會有七個步驟：

1.  建立新的**測試進行**工作流程。
2.  選取 [磁碟識別測試]。
3.  開始測試。
4.  選擇互動式驗證案例中的動作。
5.  檢查案例的結果。
6.  將測試結果傳送給 Microsoft。
7.  在 VaaS 入口網站中檢查狀態。

## <a name="create-a-new-test-pass"></a>建立新的測試進行

1.  瀏覽至 [Azure Stack 驗證入口網站](https://www.azurestackvalidation.com)並登入。

2.  建立新的解決方案或選擇現有解決方案。

3.  選取 [測試進行] 圖格中的 [開始]。

    ![替代文字](media/azure-stack-vaas-interactive-feature-verification/image1.png)

4.  輸入**測試進行**工作流程的名稱。

5.  遵循 [Azure Stack 驗證即服務的工作流程通用參數](azure-stack-vaas-parameters.md)文章中的指示，輸入環境和通用測試參數。

6.  診斷連接字串必須遵循的格式列在[工作流程通用參數](azure-stack-vaas-parameters.md)文章的[測試參數](azure-stack-vaas-parameters.md#test-parameters)一節中。

7.  輸入用於測試的必要參數。

8.  選取要執行互動式測試回合的代理程式。

> [!Note]  
> 在磁碟識別互動式功能驗證測試中，必須指定網域管理使用者和密碼。

![替代文字](media/azure-stack-vaas-interactive-feature-verification/image2.png)

## <a name="select-the-test"></a>選取測試

1.  選取 [磁碟識別測試 \<版本>]。

    > [!Note]  
    > 測試版本會在測試附屬項目有所改善時進行累加。 除非 Microsoft 另有指定，否則應該一律使用最高的版本。

    ![替代文字](media/azure-stack-vaas-interactive-feature-verification/image4.png)

2.  選取 [編輯]，以提供網域管理使用者名稱和密碼。

3.  選取適當的測試執行代理程式/DVM 來作為啟動測試的位置。

    ![替代文字](media/azure-stack-vaas-interactive-feature-verification/image5.png)

4.  選取 [提交] 來啟動測試。

![替代文字](media/azure-stack-vaas-interactive-feature-verification/image6.png)

## <a name="start-the-test"></a>開始測試

電腦上會顯示執行 VaaS 代理程式的磁碟識別測試提示。 這通常是 Azure Stack 執行個體的 DVM 或 Jumpbox。

![替代文字](media/azure-stack-vaas-interactive-feature-verification/image8.png)

## <a name="choose-the-actions"></a>選擇動作

1.  請遵循**文件**和**驗證**連結，以檢閱 Microsoft 上有關如何執行此案例的指示。

    ![替代文字](media/azure-stack-vaas-interactive-feature-verification/image9.png)

2.  選取 [下一步] 。

    ![替代文字](media/azure-stack-vaas-interactive-feature-verification/image10.png)

3.  請依照指示來執行前置檢查指令碼。

    ![替代文字](media/azure-stack-vaas-interactive-feature-verification/image11.png)

4.  成功完成前置檢查指令碼之後，請依照 [資訊] 索引標籤上的**文件**和**驗證**連結執行手動案例 (磁碟更換)。

    ![替代文字](media/azure-stack-vaas-interactive-feature-verification/image12.png)

5.  當您執行手動案例時，請不要關閉對話方塊。

6.  當您完成執行手動案例後，請依照指示執行後置檢查指令碼。

    ![替代文字](media/azure-stack-vaas-interactive-feature-verification/image13.png)

7.  當手動案例 (更換磁碟) 成功完成時，選取 [下一步]。

    ![替代文字](media/azure-stack-vaas-interactive-feature-verification/image14.png)

> [!Important]  
> 如果您關閉視窗，則測試會在完成之前停止。

## <a name="check-the-status"></a>檢查狀態

1.  測試完成時，系統會要求您提供意見反應。

    ![替代文字](media/azure-stack-vaas-interactive-feature-verification/image15.png)

2.  這些問題將有助於 Microsoft 評估案例的成功率和發行品質。

## <a name="send-the-test-result"></a>傳送測試結果

1.  附加任何您想要提交給 Microsoft 的記錄。

    ![替代文字](media/azure-stack-vaas-interactive-feature-verification/image16.png)

2.  接受意見反應提交的 EULA。

3.  選取 [提交] 來將結果傳送給 Microsoft。

## <a name="next-steps"></a>後續步驟

- [監視和管理 VaaS 入口網站中的測試](azure-stack-vaas-monitor-test.md)
