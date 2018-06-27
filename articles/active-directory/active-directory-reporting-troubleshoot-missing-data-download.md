---
title: 疑難排解︰已下載的 Azure Active Directory 活動記錄中的遺漏資料 | Microsoft Docs
description: 提供您已下載的 Azure Active Directory 活動記錄中遺漏資料的解決方案。
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 01/15/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: d0638404ec6f5b6d13aa207ef54913c1bd3ecc1a
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2018
ms.locfileid: "36232045"
---
# <a name="i-cant-find-any-data-in-the-azure-active-directory-activity-logs-i-downloaded"></a>我在下載的 Azure Active Directory 活動記錄中找不到任何資料


## <a name="symptoms"></a>徵兆

我已下載活動記錄 (稽核或登入)，卻沒看到我所選擇時間的所有記錄。 原因為何？ 

 ![報告](./media/active-directory-reporting-troubleshoot-missing-data-download/01.png)
 

## <a name="cause"></a>原因

當您在 Azure 入口網站中下載活動記錄時，我們會將級別限制為 5000 筆記錄，依最新記錄先排序。 

## <a name="resolution"></a>解決方案

您可以利用 [Azure AD 報告 API](active-directory-reporting-api-getting-started.md) 在任何指定時間點擷取最多一萬筆記錄。 我們建議的方法是定期執行指令碼，呼叫報告 API 以增量方式來擷取一段時間的記錄 (例如，每天或每週)。

## <a name="next-steps"></a>後續步驟
請參閱 [Azure Active Directory 報告常見問題集](active-directory-reporting-faq.md)。

