---
title: 疑難排解：已下載的 Azure Active Directory 活動記錄中遺漏資料 | Microsoft Docs
description: 提供您已下載的 Azure Active Directory 活動記錄中遺漏資料的解決方案。
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: f120c1b86efe94f4ff6316e6116b9049582b07e9
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2019
ms.locfileid: "68987992"
---
# <a name="i-cant-find-all-the-data-in-the-azure-active-directory-activity-logs-i-downloaded"></a>我在下載的 Azure Active Directory 活動記錄中無法找到所有資料

## <a name="symptoms"></a>徵兆

我已下載活動記錄 (稽核或登入)，卻沒看到我所選擇時間的所有記錄。 為何? 

 ![報表](./media/troubleshoot-missing-data-download/01.png)
 
## <a name="cause"></a>原因

當您下載 Azure 入口網站中的活動記錄時, 我們會將縮放比例限制為250000筆記錄 (依最新的第一個排序)。 

## <a name="resolution"></a>解決方法

您可以利用 [Azure AD 報告 API](concept-reporting-api.md) 在任何指定時間點擷取最多一萬筆記錄。

## <a name="next-steps"></a>後續步驟

* [Azure Active Directory 報告常見問題集](reports-faq.md)

