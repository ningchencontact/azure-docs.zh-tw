---
title: Sprint 3 2018 年 1 月的 Azure ML Workbench 版本資訊
description: 本文件詳述 Azure ML 的 Sprint 3 版本更新
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 01/22/2018
ms.openlocfilehash: fa209ba2259ae82796556fc1229cd6944c7a2ae1
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/17/2018
---
# <a name="sprint-3---january-2018"></a>Sprint 3 - 2018 年 1 月 

#### <a name="version-number-01171218263"></a>版本號碼：0.1.1712.18263

>此處提供如何[尋找版本號碼](known-issues-and-troubleshooting-guide.md)的說明。

歡迎使用 Azure Machine Learning Workbench 的第四次更新。 以下是此 Sprint 中的更新和增強功能。 以下的更新有許多是直接來自使用者的意見反應。 

## <a name="notable-new-features-and-changes"></a>重要新功能和變更
- 更新驗證堆疊會強制啟動時登入和選擇帳戶

## <a name="detailed-updates"></a>詳細的更新
以下是 Azure Machine Learning 在此 Sprint 中各元件區域的詳細更新清單。

### <a name="workbench"></a>Workbench
- 從 [新增/移除程式] 安裝/解除安裝應用程式的能力
- 更新驗證堆疊會強制啟動時登入和選擇帳戶
- 改善 Windows 上的單一登入 (SSO) 體驗
- 隸屬於多個租用戶且有不同認證的使用者，現在將可登入到 Workbench

#### <a name="ui"></a>UI
- 一般增強功能和 Bug 修正

### <a name="notebooks"></a>筆記本
- 一般增強功能和 Bug 修正

### <a name="data-preparation"></a>資料準備 
- 改善執行 By Example 轉換時的自動建議
- 改善模式頻率偵測器的演算法
- 執行 By Example 轉換時傳送範例資料和意見反應的能力 ![在衍生資料行轉換時傳送意見反應連結的影像](media/release-notes-sprint-3/SendFeedbackFromDeriveColumn.png)
- Spark 執行階段增強功能
- Scala 已取代 Pyspark
- 修正無法關閉時間序列偵測器不適用資料的問題 
- 修正針對 HDI 執行資料準備的擱置時間

### <a name="model-management-cli-updates"></a>模型管理 CLI 更新 
  - 佈建資源不再需要訂用帳戶的擁有權。 有資源群組的參與者存取權，就足以設定部署環境。
  - 針對免費訂用帳戶啟用本機環境設定 
