---
title: Azure Application Insights 智慧偵測：預設通知收件者即將推出的變更 | Microsoft Docs
description: 透過 Azure Application Insights 監控應用程式追蹤，以了解追蹤遙測中的異常模式。
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: harelbr
ms.author: harelbr
ms.date: 03/13/2019
ms.reviewer: mbullwin
ms.openlocfilehash: fa1f98b5a9ee592a4c702e87e365eff7941194d4
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2019
ms.locfileid: "72820619"
---
# <a name="smart-detection-e-mail-notification-change"></a>智慧偵測電子郵件通知變更

基於客戶的意見反應，將於 2019 年 4 月 1 日變更接收智慧偵測服務電子郵件通知的預設角色。

## <a name="what-is-changing"></a>變更內容為何？

智慧偵測電子郵件通知目前預設傳送至訂用帳戶擁有者、訂用帳戶參與者和訂用帳戶讀者角色。 這些角色中常包含並非主動參與監視的使用者，導致許多使用者收到不必要的通知。 為了改善使用體驗，即將進行變更，僅將電子郵件通知寄給[監視讀者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader)和[監視參與者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor)的預設角色。

## <a name="scope-of-this-change"></a>變更範圍

本次變更會影響所有智慧偵測規則，但不包括下列項目：

* 標記為預覽的智慧偵測規則。 這類智慧偵測規則目前不支援電子郵件通知功能。

* 失敗異常規則。 此規則一旦從傳統警示功能移轉至整合警示平台，就會開始以新的預設角色為目標 (如需詳細資訊，請至[此處](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement)。)

## <a name="how-to-prepare-for-this-change"></a>如何針對這項變更做準備？

若要確保智慧偵測的電子郵件通知會傳送給相關的使用者，則必須將這些使用者指派給訂用帳戶的「[監視讀者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader)」或「[監視參與者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor)」角色。

若要透過 Azure 入口網站指派使用者為監視讀者或監視參與者，請按照[新增角色指派](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment)文章所述的步驟進行。 請務必選取監視讀者或監視參與者做為使用者指派的角色。

> [!NOTE]
> 若使用規則設定中的 [新增電子郵件收件者]選項來設定智慧偵測通知的特定收件者，則不會受到這項變更影響。 這些收件者會繼續接收到電子郵件通知。

如果您對這項變更有任何問題或疑慮，請盡速[與我們連絡](mailto:smart-alert-feedback@microsoft.com)。

## <a name="next-steps"></a>後續步驟

深入了解智慧偵測：

- [失敗的異常](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [記憶體流失](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [效能異常](../../azure-monitor/app/proactive-performance-diagnostics.md)