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
ms.openlocfilehash: 4f26f3a31dc6303e991c39fc7f85d9bf254d57bc
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2019
ms.locfileid: "56885133"
---
# <a name="point-in-time-snapshot"></a>時間點快照集

Azure 應用程式設定會記錄新的金鑰-值組建立和後續修改的精確時間。 這些記錄會形成完整的金鑰-值變更時間軸。 應用程式設定存放區可以重建任何金鑰-值的過往記錄，並重新執行其在任何給定時刻的過往值，直到當下的時間。 這項功能可讓您進行「時間回溯」，並擷取舊的金鑰-值。 例如，您可以取得昨天的最新部署之前的組態設定，以便在需要復原應用程式的情況下復原先前的設定。

## <a name="key-value-retrieval"></a>擷取金鑰-值

若要擷取過去的金鑰-值，您必須在 REST API 呼叫的 HTTP 標頭中，指定金鑰-值快照集的建立時間。 例如︰

        GET /revisions HTTP/1.1
        Accept-Datetime: Sat, 1 Jan 2019 02:10:00 GMT

目前，應用程式設定會保留 7 天的變更記錄。

## <a name="next-steps"></a>後續步驟

* [快速入門：建立 ASP.NET Web 應用程式](quickstart-aspnet-core-app.md)  
