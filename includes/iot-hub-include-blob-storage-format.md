---
title: 包含檔案
description: 包含檔案
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/15/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: c779147e464a592d45da8a9a2d8e812320dc23e8
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67174602"
---
<!-- This is the note explaining about the avro and json formats when routing to blob storage. -->
> [!NOTE]
> 資料可採用 [Apache Avro](https://avro.apache.org/) 格式 (預設值) 或 JSON (預覽) 寫入至 Blob 儲存體中。 
>    
> 將 JSON 格式編碼的功能，在所有可使用 IoT 中樞的區域中均為預覽狀態，但美國東部、美國西部和歐洲西部除外。 您只能在設定 Blob 儲存體端點時設定編碼格式。 已完成設定的端點就無法變更此格式。 在使用 JSON 編碼時，您必須在訊息系統屬性中將 contentType 設定為 JSON，並將 contentEncoding 設定為 UTF-8。 
>
> 如需如何使用 Blob 儲存體端點的詳細資訊，請參閱[路由傳送至 Blob 儲存體的指引](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#azure-blob-storage)。
>