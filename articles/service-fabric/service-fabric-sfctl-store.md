---
title: Azure Service Fabric CLI- sfctl store | Microsoft Docs
description: 描述 Service Fabric CLI sfctl store 命令。
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 05/23/2018
ms.author: bikang
ms.openlocfilehash: 39ecf568c5c41c0007b358670af755be1dd5d99e
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763233"
---
# <a name="sfctl-store"></a>sfctl store
在叢集映像存放區上執行基本檔案層級作業。

## <a name="commands"></a>命令

|命令|說明|
| --- | --- |
| delete | 刪除現有映像存放區內容。 |
| root-info | 取得映像存放區根的內容資訊。 |
| stat | 取得映像存放區內容資訊。 |

## <a name="sfctl-store-delete"></a>sfctl store delete
刪除現有映像存放區內容。

刪除在指定映像存放區相對路徑內找到的現有映像存放區內容。 此命令可以用來刪除已上傳且佈建的應用程式套件。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --content-path [必要] | 映像存放區中檔案或資料夾與其根的相對路徑。 |
| --timeout -t | 伺服器逾時 (秒)。  預設值：60。 |

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug | 增加記錄詳細程度以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值：json、jsonc、table、tsv。  預設值：json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細程度。 使用 --debug 取得完整偵錯記錄。 |

## <a name="sfctl-store-root-info"></a>sfctl store root-info
取得映像存放區根的內容資訊。

傳回位於映像存放區根目錄中映像存放區內容的相關資訊。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --timeout -t | 伺服器逾時 (秒)。  預設值：60。 |

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug | 增加記錄詳細程度以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值：json、jsonc、table、tsv。  預設值：json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細程度。 使用 --debug 取得完整偵錯記錄。 |

## <a name="sfctl-store-stat"></a>sfctl store stat
取得映像存放區內容資訊。

傳回位於所指定 contentPath 映像存放區內容的相關資訊。 contentPath 是相對於映像存放區根目錄的路徑。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --content-path [必要] | 映像存放區中檔案或資料夾與其根的相對路徑。 |
| --timeout -t | 伺服器逾時 (秒)。  預設值：60。 |

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug | 增加記錄詳細程度以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值：json、jsonc、table、tsv。  預設值：json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細程度。 使用 --debug 取得完整偵錯記錄。 |

## <a name="next-steps"></a>後續步驟
- [設定](service-fabric-cli.md) Service Fabric CLI。
- 了解如何使用[範例指令碼](/azure/service-fabric/scripts/sfctl-upgrade-application)來使用 Service Fabric CLI。