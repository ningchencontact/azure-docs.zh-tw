---
title: 針對 Azure Active Directory 報表使用 Azure 監視器活頁簿 |Microsoft Docs
description: 瞭解如何使用 Azure 監視器活頁簿來 Azure Active Directory 報表。
services: active-directory
author: cawrites
manager: daveba
ms.assetid: 4066725c-c430-42b8-a75b-fe2360699b82
ms.service: active-directory
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: chadam
ms.reviewer: dhanyahk
ms.openlocfilehash: 288fa54a1a6dd4eb05f953a4490bf7736d6d7ff8
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/12/2019
ms.locfileid: "70931255"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>如何使用 Azure Active Directory 報表 Azure 監視器活頁簿

您要：

- 瞭解您的條件式[存取原則](../conditional-access/overview.md)對於使用者登入體驗的影響為何？

- 針對登入失敗進行疑難排解，以進一步瞭解組織的登入健康情況並快速解決問題嗎？

- 知道誰使用舊版驗證來登入您的環境？ （藉由[封鎖舊版驗證](../conditional-access/block-legacy-authentication.md)，您可以改善租使用者的保護）。

為了協助您解決這些問題，Active Directory 提供用於監視的活頁簿。 [Azure 監視器活頁簿](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)將文字、分析查詢、計量和參數結合成豐富的互動式報表。 

本文：

- 假設您已經熟悉如何使用 [[監視活頁簿] 建立互動式報表](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)。

- 說明如何使用監視活頁簿來瞭解條件式存取原則的效果、對登入失敗進行疑難排解，以及識別舊版驗證。
 


## <a name="prerequisites"></a>必要條件

若要使用監視器活頁簿，您需要：

- 具有 premium （P1 或 P2）授權的 Active Directory 租使用者。 瞭解如何[取得 premium 授權](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-get-started-premium)。

- [Log Analytics 工作區](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)。

## <a name="roles"></a>角色
您必須是下列其中一種角色，而且可以[存取基礎 Log Analytics](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/manage-access#manage-access-using-azure-permissions)工作區來管理活頁簿：
-   全域管理員
-   安全性系統管理員
-   安全性讀取者
-   報告讀取者
-   應用程式管理員


## <a name="workbook-access"></a>活頁簿存取 

若要存取活頁簿：

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 在左側導覽窗格中，選取 [ **Azure Active Directory**]。

3. 在 [**監視**] 區段中，選取 [活頁**簿**]。 

    ![選取深入解析](./media/howto-use-azure-monitor-workbooks/41.png)

4. 選取報表或範本，或在工具列上選取 [**開啟**]。 

    ![選取 [開啟]](./media/howto-use-azure-monitor-workbooks/42.png)


## <a name="sign-in-analysis"></a>登入分析

若要存取登入分析活頁簿，請在 [**使用**方式] 區段中選取 [登**入**]。 

此活頁簿會顯示下列登入趨勢：

- 所有登入

- 成功

- 等候使用者動作

- 失敗

您可以依下列類別篩選每個趨勢：

- 時間範圍

- App

- 使用者人數

![登入分析](./media/howto-use-azure-monitor-workbooks/43.png)


針對每個趨勢，您會獲得下列類別的明細：

- Location

    ![依位置登入](./media/howto-use-azure-monitor-workbooks/45.png)

- 裝置

    ![依裝置登入](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>使用傳統驗證登入 


若要存取使用[舊版驗證](../conditional-access/block-legacy-authentication.md)的登入活頁簿，請在 [**使用**方式] 區段中，選取 [**使用舊版驗證登入**]。 

此活頁簿會顯示下列登入趨勢：

- 所有登入

- 成功


您可以依下列類別篩選每個趨勢：

- 時間範圍

- App

- 使用者人數

- 通訊協定

![依舊版驗證的登入](./media/howto-use-azure-monitor-workbooks/47.png)


針對每個趨勢，您會依應用程式和通訊協定來取得細目。

![舊版-依應用程式和通訊協定的驗證登入](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>依條件式存取登入 


若要透過[條件式存取原則](../conditional-access/overview.md)存取登入的活頁簿，請在 [**條件式存取**] 區段中，選取 [**依條件式存取登入**]。 

此活頁簿會顯示已停用登入的趨勢。您可以依下列類別篩選每個趨勢：

- 時間範圍

- App

- 使用者人數

![使用條件式存取登入](./media/howto-use-azure-monitor-workbooks/49.png)


若為已停用的登入，您會依條件式存取狀態取得細目。

![條件式存取狀態](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)








## <a name="sign-ins-by-grant-controls"></a>藉由授與控制項來登入

若要透過[授與控制項](../conditional-access/controls.md)存取登入的活頁簿，請在 [**條件式存取**] 區段中，選取 **[依授與控制項登入**]。 

此活頁簿會顯示下列已停用的登入趨勢：

- 需要 MFA
 
- 需要使用規定

- 需要隱私權聲明

- 其他


您可以依下列類別篩選每個趨勢：

- 時間範圍

- App

- 使用者人數

![藉由授與控制項來登入](./media/howto-use-azure-monitor-workbooks/50.png)


針對每個趨勢，您會依應用程式和通訊協定來取得細目。

![最近登入的明細](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>登入失敗分析

使用 [登**入失敗分析**] 活頁簿，針對下列錯誤進行疑難排解：

- 登入
- 條件式存取原則
- 舊版驗證 


若要透過條件式存取資料存取登入，請在 [**疑難排解**] 區段中，選取 [**使用舊版驗證登入**]。 

此活頁簿會顯示下列登入趨勢：

- 所有登入

- 成功

- 擱置動作

- 失敗


您可以依下列類別篩選每個趨勢：

- 時間範圍

- App

- 使用者人數

![對登入進行疑難排解](./media/howto-use-azure-monitor-workbooks/52.png)


為協助您針對登入進行疑難排解，Azure 監視器提供下列類別的明細：

- 前幾個錯誤

    ![前幾個錯誤的摘要](./media/howto-use-azure-monitor-workbooks/53.png)

- 登入正在等候使用者動作

    ![登入等候使用者動作的摘要](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>後續步驟

[使用 [監視活頁簿] 建立互動式報表](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)。
