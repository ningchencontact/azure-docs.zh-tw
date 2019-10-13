---
title: 當您開啟 Microsoft Azure Automation 的案例時所要收集的資料 |Microsoft Docs
description: 本文說明在開啟案例 Azure 自動化以 Microsoft Azure 支援之前，您應該先收集的一些基本資訊。
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/23/2019
ms.author: v-miegge
ms.openlocfilehash: 83ee78d369af7fe99de8e7236fe1eb0bc63a942f
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301746"
---
# <a name="data-to-collect-when-you-open-a-case-for-microsoft-azure-automation"></a>當您開啟 Microsoft Azure 自動化案例時要收集的資料

本文說明在開啟案例 Azure 自動化以 Microsoft Azure 支援之前，您應該先收集的一些基本資訊。 開啟案例不需要這種資訊。 不過，它可以協助 Microsoft 更快速地解決您的問題。 此外，在您開啟案例之後，支援工程師可能會要求您提供這項資料。

## <a name="collect-more-information"></a>收集詳細資訊

在您開啟 Microsoft Azure Automation 支援的案例之前，建議您先收集下列資訊。

### <a name="basic-data-collection"></a>基本資料收集

收集下列知識庫文章中所述的資料：

* [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)如何捕獲 Azure 自動化腳本的診斷

## <a name="collect-data-depending-on-issue"></a>依據問題收集資料
 
### <a name="for-issues-that-affect-update-management-on-linux"></a>針對會影響 Linux 上更新管理的問題

1. 除了 KB [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)中所列的專案，請執行下列記錄收集工具：

   [OMS Linux 代理程式記錄檔收集器](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)
 
2. 壓縮下列資料夾的內容，然後將壓縮檔案傳送至 Azure 支援：

   ``/var/opt/microsoft/omsagent/run/automationworker/``
 
3. 確認 OMS Linux 代理程式所報告的工作區識別碼，與受監視更新的工作區相同。

### <a name="for-issues-that-affect-update-management-on-windows"></a>對於影響 Windows 更新管理的問題

除了[4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)中列出的專案，請執行下列動作：

1. 將下列事件記錄檔匯出為 .EVTX 格式：

   * 系統
   * 應用程式
   * 安全性
   * Operations Manager
   * Microsoft-SMA/營運

2. 確認代理程式所報告的工作區識別碼與 Windows 更新所監視的工作區相同。

### <a name="for-issues-that-affect-a-job"></a>針對會影響作業的問題

除了[4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)中列出的專案以外，請收集下列資訊：

1. 收集**JobID**號碼（針對發生問題的工作）：

   1. 在 Azure 入口網站上，移至 [**自動化帳戶**] 分頁。
   2. 選取您要進行疑難排解的**自動化帳戶**。
   3. 選取 [**作業**]。
   4. 選取您要進行疑難排解的作業。
   5. 在 [**作業摘要**] 下，尋找**作業識別碼**（GUID）。

   ![作業摘要窗格中的作業識別碼](media/collect-data-microsoft-azure-automation-case/job-summary-job-id.png)

2. 收集帳戶名稱：

   1. 在 Azure 入口網站上，移至 [**自動化帳戶**] 分頁。
   2. 取得您要進行疑難排解之**自動化帳戶**的名稱。

3. 收集您正在執行之腳本的範例。

4. 收集記錄檔：

   1. 在 Azure 入口網站上，移至 [**自動化帳戶**] 分頁。
   2. 選取您要進行疑難排解的**自動化帳戶**。
   3. 選取 [**作業**]。
   4. 選取您要進行疑難排解的作業。
   5. 選取 [**所有記錄**]。
   6. 在產生的分頁上，收集資料。

   ![列在 [所有記錄] 底下的資料](media/collect-data-microsoft-azure-automation-case/all-logs-data.png)

### <a name="for-issues-that-affect-modules"></a>適用于影響模組的問題

除了 [基本資料收集] 底下的專案，請收集下列資訊：

* 您所遵循的步驟，可以重現問題。
* 任何錯誤訊息的螢幕擷取畫面。
* 目前模組及其版本號碼的螢幕擷取畫面。

## <a name="next-steps"></a>後續步驟

如果您在本文中有任何需要協助的地方，請洽詢[MSDN azure 和 Stack Overflow 論壇](https://azure.microsoft.com/support/forums/)上的 azure 專家。

或者，將 Azure 支援事件提出。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。