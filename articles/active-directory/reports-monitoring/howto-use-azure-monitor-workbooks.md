---
title: 使用 Azure 監視器的活頁簿，針對 Azure Active Directory 報告 |Microsoft Docs
description: 了解如何使用 Azure 監視器的活頁簿做為 Azure Active Directory 報告。
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: 4066725c-c430-42b8-a75b-fe2360699b82
ms.service: active-directory
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 6ae14ec152975717af5d55780bcc39aa87c4b01a
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2019
ms.locfileid: "65406602"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>如何使用 Azure 監視器的活頁簿，針對 Azure Active Directory 報告

您想要：

- 了解的效果您[條件式存取原則](../conditional-access/overview.md)上您的使用者登入體驗？

- 登入針對失敗進行疑難排解以取得較佳的組織的登入健全狀況檢視，並快速解決問題嗎？

- 知道誰在使用舊版驗證來登入您的環境？ (由[封鎖舊版驗證](../conditional-access/block-legacy-authentication.md)，您可以改善您的租用戶保護。)

為了協助您解決這些問題，Active Directory 提供活頁簿監視。 [Azure 監視器活頁簿](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)結合豐富的互動式報表中的文字、 分析查詢、 度量和參數。 

本文：

- 假設您已熟悉如何[藉由監視活頁簿中建立互動式報表](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)。

- 說明如何使用監視活頁簿來了解您的條件式存取原則，以登入失敗進行疑難排解並找出傳統驗證的效果。
 


## <a name="prerequisites"></a>必要條件

若要使用監視器活頁簿，您需要：

- Premium （P1 或 P2） 授權 Active Directory 租用戶。 了解如何[取得進階授權](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-get-started-premium)。

- A [Log Analytics 工作區](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)。

## <a name="workbook-access"></a>活頁簿存取 

若要存取活頁簿：

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 在左側的導覽窗格中，選取**Azure Active Directory**。

3. 在 **監視**區段中，選取**Insights**。 

    ![選取 [Insights]](./media/howto-use-azure-monitor-workbooks/41.png)

4. 選取報表或範本，或在工具列上選取**開啟**。 

    ![選取 [開啟]](./media/howto-use-azure-monitor-workbooks/42.png)


## <a name="sign-in-analysis"></a>登入分析

若要存取登入分析活頁簿中，在**使用量**區段中，選取**登入**。 

此活頁簿會顯示下列登入趨勢：

- 所有登入

- 成功

- 等候使用者動作

- 失敗

您可以篩選每個趨勢，依下列類別：

- 時間範圍

- App

- 使用者

![登入分析](./media/howto-use-azure-monitor-workbooks/43.png)


每個趨勢，您取得明細依下列類別：

- 位置

    ![依位置的登入](./media/howto-use-azure-monitor-workbooks/45.png)

- 裝置

    ![裝置的登入](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>使用傳統驗證的登入次數 


若要存取使用的登入的活頁簿[舊有的驗證](../conditional-access/block-legacy-authentication.md)，請在**使用量**區段中，選取**登入使用舊版驗證**。 

此活頁簿會顯示下列登入趨勢：

- 所有登入

- 成功


您可以篩選每個趨勢，依下列類別：

- 時間範圍

- App

- 使用者

- 通訊協定

![登入的舊有的驗證](./media/howto-use-azure-monitor-workbooks/47.png)


針對每個趨勢，您可以取得的細目-依應用程式和通訊協定。

![舊版驗證登入的應用程式和通訊協定](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>條件式存取的登入 


若要存取的登入的活頁簿[條件式存取原則](../conditional-access/overview.md)，請在**條件式存取**區段中，選取**被條件式存取的登入**。 

此活頁簿會顯示已停用的登入的趨勢。您可以篩選每個趨勢，依下列類別：

- 時間範圍

- App

- 使用者

![登入使用條件式存取](./media/howto-use-azure-monitor-workbooks/49.png)


對已停用登入，您可以取得細目依條件式存取狀態。

![條件式存取狀態](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)








## <a name="sign-ins-by-grant-controls"></a>授與控制項的登入

若要存取的登入的活頁簿[授與控制項](../conditional-access/controls.md)，請在**條件式存取**區段中，選取**授與控制項的登入**。 

此活頁簿會顯示下列已停用的登入趨勢：

- 需要 MFA
 
- 需要使用規定

- 需要隱私權聲明

- 其他


您可以篩選每個趨勢，依下列類別：

- 時間範圍

- App

- 使用者

![授與控制項的登入](./media/howto-use-azure-monitor-workbooks/50.png)


針對每個趨勢，您可以取得的細目-依應用程式和通訊協定。

![最近的登入的細項](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>登入失敗分析

使用**登入失敗分析**活頁簿，以針對使用下列的錯誤進行疑難排解：

- 登入
- 條件式存取原則
- 舊版驗證 


若要存取登入的條件式存取資料，在**疑難排解**區段中，選取**登入使用舊版驗證**。 

此活頁簿會顯示下列登入趨勢：

- 所有登入

- 成功

- 擱置動作

- 失敗


您可以篩選每個趨勢，依下列類別：

- 時間範圍

- App

- 使用者

![疑難排解登入](./media/howto-use-azure-monitor-workbooks/52.png)


為了協助您疑難排解登入，Azure 監視器提供細目依下列類別：

- 最上層錯誤

    ![最上層錯誤的摘要](./media/howto-use-azure-monitor-workbooks/53.png)

- 登入等候使用者動作

    ![登入等候使用者動作的摘要](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>後續步驟

[使用監視活頁簿來建立互動式報表](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)。