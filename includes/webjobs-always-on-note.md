---
title: 包含檔案
description: 包含檔案
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 02/19/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 14f2e663e3db81684a73c4ea093ed0403cbb09ed
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67174137"
---
> [!NOTE]
> Web 應用程式會在 20 分鐘沒有活動之後逾時。 只有對實際 Web 應用程式所提出的要求會重設計時器。 在 Azure 入口網站中檢視應用程式的組態，或要求對進階的工具網站 (`https://<app_name>.scm.azurewebsites.net`) 不會重設計數器。 如果您的應用程式執行連續或已排程的 WebJob，請啟用 [永遠開啟]  ，確保 Webjob 會可靠地執行。 這項功能僅適用於基本、標準和進階[定價層](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
