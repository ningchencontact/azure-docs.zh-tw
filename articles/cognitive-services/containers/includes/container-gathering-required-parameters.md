---
title: 正在搜集必要的參數
services: cognitive-services
author: IEvangelist
manager: nitinme
description: 所有認知服務容器的參數
ms.service: cognitive-services
ms.topic: include
ms.date: 7/24/2019
ms.author: dapine
ms.openlocfilehash: 636a41fde345a08db1549e53626522962f9cf74f
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/25/2019
ms.locfileid: "68488787"
---
## <a name="gathering-required-parameters"></a>正在搜集必要的參數

所有必要的認知服務容器都有三個主要參數。 使用者授權合約 (**EULA**) 必須存在, 且值為`accept`。 此外, 也需要端點 URL 和 API 金鑰。

### <a name="endpoint-uri-endpointuri"></a>端點 URI`{Endpoint_URI}`

[**端點**URI] 值可在對應認知服務資源的 [Azure 入口網站*總覽*] 頁面上取得。 流覽至 [*總覽*] 頁面, 將滑鼠停留在端點上`Copy to clipboard` , <span class="docon docon-edit-copy x-hidden-focus"></span>圖示就會出現。 視需要複製並使用。

![收集端點 uri 以供稍後使用](../media/overview-endpoint-uri.png)

### <a name="keys-apikey"></a>快速鍵`{API_Key}`

此金鑰用來啟動容器, 並可在對應認知服務資源的 Azure 入口網站的 [金鑰] 頁面上取得。 流覽至 [*金鑰*] 頁面, 然後按一下`Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span>圖示。

![取得兩個金鑰的其中一個供稍後使用](../media/keys-copy-api-key.png)

> [!IMPORTANT]
> 這些訂用帳戶金鑰可用來存取您的認知服務 API。 請勿共用您的金鑰。 以安全的方式儲存它們, 例如使用 Azure Key Vault。 我們也建議您定期重新產生這些金鑰。 進行 API 呼叫時, 只需要一個金鑰。 重新產生第一個金鑰時, 您可以使用第二個金鑰繼續存取服務。