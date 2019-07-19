---
title: 保留折扣如何套用至 Azure App Service 隔離戳記
description: 瞭解保留折扣如何套用至 Azure App Service 隔離戳記。
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: banders
ms.openlocfilehash: cb4b371deadb1ae9e6ae048c3157809aff857c9d
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/17/2019
ms.locfileid: "68298252"
---
# <a name="how-reservation-discounts-apply-to-azure-app-service-isolated-stamps"></a>保留折扣如何套用至 Azure App Service 隔離戳記

購買 App Service 隔離式方案戳記費用保留容量之後, 保留折扣會自動套用至區域中的戳記費用。 保留折扣適用于獨立戳記費用計量表所發出的使用量。 背景工作、額外的前端, 以及與此戳記相關聯的任何其他資源, 會繼續以一般費率計費。

## <a name="reservation-discount-application"></a>保留折扣應用程式

App Service 隔離式方案戳記費用折扣會套用至以小時為單位執行隔離的戳記。 如果您沒有部署一個小時的戳記, 則保留容量會浪費一小時。 它不會繼續執行。

購買之後, 您購買的保留會與在特定區域中執行的隔離戳記進行比對。 如果您關閉該戳記, 則保留折扣會自動套用至在該區域中執行的任何其他戳記。 當沒有任何戳記存在時, 保留會套用至在該區域中建立的下一個戳記。

當戳記未執行一小時, 保留期間會自動套用至相同區域中的其他相符戳記。

## <a name="choose-a-stamp-type---windows-or-linux"></a>選擇戳記類型-Windows 或 Linux

空的隔離戳記預設會發出 Windows 戳記計量。 例如, 未部署任何背景工作角色時。 當部署 Windows worker 時, 它會繼續發出計量。 如果您部署 Linux 背景工作角色, 計量會變更為 Linux 戳記計量。 當 Linux 和 Windows 背景工作角色都已部署時, 戳記會發出 Windows 計量。

因此, 戳記計量可能會在戳記的生命週期內, 于 Windows 和 Linux 之間變更。 同時, 保留是作業系統特定的。 您必須購買一個保留, 以支援您打算部署至戳記的背景工作角色。 僅限 windows 的戳記和混合戳記會使用 Windows 保留區。 只有 Linux 背景工作的戳記會使用 Linux 保留區。

只有當您打算在戳記中_只有_linux 背景工作角色時, 才應該購買 linux 保留專案。

## <a name="discount-examples"></a>折扣範例

下列範例會根據部署, 顯示如何套用「隔離戳記費用」保留實例折扣。

- **範例 1**:您在沒有 App Service 隔離式方案戳記的區域中購買一個隔離的保留戳記容量實例。 您會在區域中部署新的戳記, 並支付該戳記的保留費率。
- **範例 2：** 您在已部署 App Service 隔離式方案戳記的區域中購買一個隔離的保留戳記容量實例。 您開始收到已部署戳記的保留費率。
- **範例 3**:您在已部署 App Service 隔離式方案戳記的區域中購買一個隔離的保留戳記容量實例。 您開始收到已部署戳記的保留速率。 稍後, 您會刪除戳記並部署新的戳記。 您會收到新戳記的保留費率。 折扣不會在沒有部署戳記的情況下繼續執行。
- **範例 4**:您會在區域中購買一個隔離的 Linux 保留戳記容量實例, 然後將新的戳記部署到該區域。 一開始部署戳記而沒有背景工作時, 它會發出 Windows 戳記計量。 未收到折扣。 當第一個 Linux 背景工作角色部署戳記時, 它會發出 Linux 戳記計量, 並套用保留折扣。 如果稍後將 windows worker 部署到戳記, 戳記計量會還原為 Windows。 您不會再收到隔離的 Linux 保留戳記保留的折扣。

## <a name="next-steps"></a>後續步驟

- 若要了解如何管理保留項目，請參閱[管理 Azure 保留項目](billing-manage-reserved-vm-instance.md)。
- 若要深入瞭解預先購買 App Service 隔離式方案戳記保留容量以節省成本, 請參閱[預付 Azure App Service 使用保留容量的隔離戳記費用](billing-prepay-app-service-isolated-stamp.md)。
- 若要深入了解 Azure 保留項目，請參閱下列文章：
  - [什麼是 Azure 保留項目？](billing-save-compute-costs-reservations.md)
  - [管理 Azure 中的保留](billing-manage-reserved-vm-instance.md)
  - [瞭解使用隨用隨付費率之訂用帳戶的保留使用量](billing-understand-reserved-instance-usage.md)
  - [了解 Enterprise 註冊的保留項目使用量](billing-understand-reserved-instance-usage-ea.md)
