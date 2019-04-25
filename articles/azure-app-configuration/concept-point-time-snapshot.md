---
title: Azure 應用程式設定時間點快照集 | Microsoft Docs
description: 概略說明時間點快照集在 Azure 應用程式設定中的運作方式
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.service: azure-app-configuration
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 83770e8c5f415670855b5cf2502d02c4d6919440
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2019
ms.locfileid: "59998068"
---
# <a name="point-in-time-snapshot"></a>時間點快照集

Azure 應用程式設定會記錄新的索引鍵值組建立和後續修改的精確時間。 這些記錄會形成完整的金鑰-值變更時間軸。 應用程式設定存放區可以重建任何索引鍵值的過往記錄，並重新執行其在任何給定時刻的過往值，直到當下的時間。 使用這項功能，可讓您進行「時間回溯」，並擷取舊的索引鍵值。 例如，您可以取得昨天的最新部署之前的組態設定，以便復原先前的設定和復原應用程式。

## <a name="key-value-retrieval"></a>擷取金鑰-值

若要擷取過去的索引鍵值，請在 REST API 呼叫的 HTTP 標頭中，指定索引鍵值快照集的建立時間。 例如︰

        GET /kv HTTP/1.1
        Accept-Datetime: Sat, 1 Jan 2019 02:10:00 GMT

目前，應用程式設定會保留七天的變更記錄。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建立 ASP.NET Core Web 應用程式](./quickstart-aspnet-core-app.md)  
