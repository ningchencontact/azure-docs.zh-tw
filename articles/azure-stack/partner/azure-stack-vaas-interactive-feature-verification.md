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
ms.date: 01/07/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 01/07/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 20a48e67a24763af7bcce9e8831e2a1d1846d094
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/21/2019
ms.locfileid: "56594299"
---
# <a name="interactive-feature-verification-testing"></a>互動式功能驗證測試  

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

您可以使用互動式功能驗證測試架構來要求測試您的系統。 當您要求測試時，Microsoft 會使用該架構來準備需要手動執行互動步驟的測試。 Microsoft 可以使用該架構將數個獨立的自動化測試鏈結在一起。

本文將說明簡單的手動案例。 此測試會檢查 Azure Stack 中的磁碟更換。 而架構會收集每個步驟的診斷記錄。 您可以在找到問題時，對問題進行偵錯。 此架構也允許共用由其他工具或程序產生的記錄，並讓您針對案例提供意見反應。

> [!Important]  
> 本文將參考執行磁碟識別的步驟。 這些內容僅供示範之用，因為測試進行工作流程收集到的任何結果不一定適用於新解決方案的驗證。

## <a name="overview-of-interactive-testing"></a>互動式測試概觀

磁碟更換的測試是常見案例。 在此範例中，測試會有七個步驟：

1. 建立新的**測試進行**工作流程
1. 選取 [磁碟識別測試]
1. 在出現提示時完成手動步驟
1. 檢查案例的結果
1. 將測試結果傳送給 Microsoft

## <a name="create-a-new-test-pass"></a>建立新的測試進行

如果您沒有現有的測試進行可使用，請依照[排程測試](azure-stack-vaas-schedule-test-pass.md)的指示操作。

## <a name="schedule-the-test"></a>排程測試

1. 選取 [磁碟識別測試]。

    > [!Note]  
    > 測試版本會在測試附屬項目有所改善時進行累加。 除非 Microsoft 另有指定，否則應該一律使用最高的版本。

    ![替代文字](media/azure-stack-vaas-interactive-feature-verification/image4.png)

1. 選取 [編輯]，以提供網域管理員的使用者名稱和密碼。

1. 選取適當的測試執行代理程式/DVM 來作為啟動測試的位置。

    ![替代文字](media/azure-stack-vaas-interactive-feature-verification/image5.png)

1. 選取 [提交] 來啟動測試。

    ![替代文字](media/azure-stack-vaas-interactive-feature-verification/image6.png)

1. 從在先前的步驟中選取的代理程式存取互動式測試的 UI。

    ![替代文字](media/azure-stack-vaas-interactive-feature-verification/image8.png)

1. 請遵循**文件**和**驗證**連結，以檢閱 Microsoft 上有關如何執行此案例的指示。

    ![替代文字](media/azure-stack-vaas-interactive-feature-verification/image9.png)

1. 選取 [下一步] 。

    ![替代文字](media/azure-stack-vaas-interactive-feature-verification/image10.png)

1. 請依照指示來執行前置檢查指令碼。

    ![替代文字](media/azure-stack-vaas-interactive-feature-verification/image11.png)

1. 成功完成前置檢查指令碼之後，請依照 [資訊] 索引標籤上的**文件**和**驗證**連結執行手動案例 (磁碟更換)。

    ![替代文字](media/azure-stack-vaas-interactive-feature-verification/image12.png)

    > [!Important]  
    > 當您執行手動案例時，請不要關閉對話方塊。

1. 當您完成執行手動案例後，請依照指示執行後置檢查指令碼。

    ![替代文字](media/azure-stack-vaas-interactive-feature-verification/image13.png)

1. 當手動案例 (更換磁碟) 成功完成時，選取 [下一步]。

    ![替代文字](media/azure-stack-vaas-interactive-feature-verification/image14.png)

    > [!Important]  
    > 如果您關閉視窗，則測試會在完成之前停止。

1. 提供測試體驗的意見反應。 這些問題將有助於 Microsoft 評估案例的成功率和發行品質。

    ![替代文字](media/azure-stack-vaas-interactive-feature-verification/image15.png)

1. 附加任何您想要提交給 Microsoft 的記錄。

    ![替代文字](media/azure-stack-vaas-interactive-feature-verification/image16.png)

1. 接受意見反應提交的 EULA。

1. 選取 [提交] 來將結果傳送給 Microsoft。

## <a name="next-steps"></a>後續步驟

- [監視和管理 VaaS 入口網站中的測試](azure-stack-vaas-monitor-test.md)
