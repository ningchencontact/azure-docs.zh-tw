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
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/24/2019
ms.locfileid: "56751087"
---
> [!NOTE]
> Web 應用程式會在 20 分鐘沒有活動之後逾時。 只有對實際 Web 應用程式所提出的要求會重設計時器。 在 Azure 门户中查看应用的配置或向高级工具站点 (`https://<app_name>.scm.azurewebsites.net`) 发出请求不会重置计时器。 如果您的應用程式執行連續或已排程的 WebJob，請啟用 [永遠開啟]，確保 Webjob 會可靠地執行。 這項功能僅適用於基本、標準和進階[定價層](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
