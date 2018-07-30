---
title: 教學課程 - 將 Azure Active Directory 記錄封存到 Azure 儲存體帳戶 (預覽) | Microsoft Docs
description: 深入了解如何設定 Azure 診斷以將 Azure Active Directory 記錄推送到儲存體帳戶 (預覽)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 045f94b3-6f12-407a-8e9c-ed13ae7b43a3
ms.service: active-directory
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: ea26da91e2a0b88e8afa54a4210b7a2365e4949b
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/25/2018
ms.locfileid: "39240154"
---
# <a name="tutorial-archive-azure-active-directory-logs-to-an-azure-storage-account-preview"></a>教學課程：將 Azure Active Directory 記錄封存到 Azure 儲存體帳戶 (預覽)

在本教學課程中，您將會了解如何設定 Azure 監視器診斷設定，以將 Azure Active Directory 記錄路由傳送到 Azure 儲存體帳戶。

## <a name="prerequisites"></a>必要條件 

您需要：

* 具有 Azure 儲存體帳戶的 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，您可以註冊[免費試用](https://azure.microsoft.com/free/)。
* Azure Active Directory 租用戶。
* 一個使用者，身分是該租用戶的全域管理員或安全性管理員。

## <a name="archive-logs-to-an-azure-storage-account"></a>將記錄封存到 Azure 儲存體帳戶

1. 登入 [Azure 入口網站](https://portal.azure.com)。 
2. 按一下 [Azure Active Directory]  ->  [活動]  ->  [稽核記錄]。 
3. 按一下 [匯出設定] 以開啟 [診斷設定] 刀鋒視窗。 如果您想要變更現有的設定，按一下 [編輯設定]，或者按一下 [新增診斷設定] 以新增一個設定。 您最多可以有三個設定。 
    ![匯出設定](./media/reporting-azure-monitor-diagnostics-azure-storage-account/ExportSettings.png "匯出設定")

4. 新增設定的易記名稱，用來提醒您它的目的。 例如，「傳送到 Azure 儲存體帳戶」。 
5. 請勾選 [封存到儲存體帳戶] 核取方塊，然後按一下 [儲存體帳戶] 以選擇 Azure 儲存體帳戶。 
6. 選取您想要將記錄路由傳送到其中的 Azure 訂用帳戶和儲存體帳戶，然後按一下 [確定] 以關閉設定。
7. 勾選 [AuditLogs] 核取方塊，以將稽核記錄傳送到儲存體帳戶。 
8. 勾選 [SignInLogs] 核取方塊，以將登入記錄傳送到儲存體帳戶。
9. 使用滑桿來設定記錄資料的保留期。 根據預設，這個值是 "0"，記錄會無限期保留在儲存體帳戶中。 或者，您可以設定值，系統會自動將超過所選天數的事件清除。
10. 按一下 [儲存] 來儲存這些設定。
    ![診斷設定](./media/reporting-azure-monitor-diagnostics-azure-storage-account/DiagnosticSettings.png "診斷設定")

11. 大約 15 分鐘之後，確認記錄是否已推送到您的儲存體帳戶。 移至 Azure 入口網站，按一下 [儲存體帳戶]，選擇您稍早使用的儲存體帳戶，然後按一下 [Blob]。 
12. 針對 [稽核記錄] 按一下 [insights-log-audit]。 針對 [登入記錄] 按一下 [insights-logs-signin]。
    ![儲存體帳戶](./media/reporting-azure-monitor-diagnostics-azure-storage-account/StorageAccount.png "儲存體帳戶")

## <a name="next-steps"></a>後續步驟

* [解譯 Azure 監視器中的稽核記錄結構描述](reporting-azure-monitor-diagnostics-audit-log-schema.md)
* [解譯 Azure 監視器中的登入記錄結構描述](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)
* [常見問題集和已知問題](reporting-azure-monitor-diagnostics-overview.md#frequently-asked-questions)