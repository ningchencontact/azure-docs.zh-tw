---
title: 包含檔案
description: 包含檔案
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 050d3314345e64e3d69a07367a0e9acc318fa106
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2019
ms.locfileid: "66271527"
---
| Resource | 預設限制 | 上限 |
| --- | --- | --- |
| 自動調整設定 |訂用帳戶每區域每 100 個。 | 預設值相同。 |
| 計量警示 （傳統） |100 個作用中警示規則每訂用帳戶。 | 請連絡支援人員。 |
| 度量警示 |1000 使用中的警示規則每個訂用帳戶 （在公用雲端中） 和每個訂用帳戶中 Azure China 和 Azure Government 的 100 個作用中警示規則。 | 請連絡支援人員。 |
| 活動記錄警示 | 100 個作用中警示規則每訂用帳戶。 | 預設值相同。 |
| 記錄警示 | 512 | 請連絡支援人員。 |
| 動作群組 |2,000 動作群組，每個訂用帳戶。 | 請連絡支援人員。 |

**動作群組特定的限制**

| Resource | 預設限制 | 上限 |
| --- | --- | --- |
| Azure 應用程式推送 | 每個動作群組的 10 個 azure 應用程式動作。 | 請連絡支援人員。 |
| 電子郵件 | 在 動作群組的電子郵件動作 1,000。 另請參閱[速率限制資訊](../articles/azure-monitor/platform/alerts-rate-limiting.md)。 | 請連絡支援人員。 |
| ITSM | 在 動作群組中的 10 個 ITSM 動作。 | 請連絡支援人員。 | 
| 邏輯應用程式 | 在 動作群組中的 10 個邏輯應用程式動作。 | 請連絡支援人員。 |
| Runbook | 在 動作群組中的 10 個 runbook 動作。 | 請連絡支援人員。 |
| sms | 在 動作群組中的 10 個簡訊動作。 另請參閱[速率限制資訊](../articles/azure-monitor/platform/alerts-rate-limiting.md)。 | 請連絡支援人員。 |
| 語音 | 在 動作群組中的 10 個語音動作。 另請參閱[速率限制資訊](../articles/azure-monitor/platform/alerts-rate-limiting.md)。 | 請連絡支援人員。 |
| Webhook | 在 動作群組中的 10 個 webhook 動作。  Webhook 呼叫的數目上限是 1500 每分鐘每個訂用帳戶。 其他限制位於[動作特有的資訊](../articles/azure-monitor/platform/action-groups.md#action-specific-information)。  | 請連絡支援人員。 |
