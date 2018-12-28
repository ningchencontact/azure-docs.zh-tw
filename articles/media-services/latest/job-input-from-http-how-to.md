---
title: 從 HTTPS URL 建立 Azure 媒體服務工作輸入 | Microsoft Docs
description: 本主題說明如何從 HTTP(s) URL 建立工作輸入。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: 2295df97dfe6792979738debcc56e3b18b271e69
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2018
ms.locfileid: "53412929"
---
# <a name="create-a-job-input-from-an-https-url"></a>從 HTTPS URL 建立工作輸入

在媒體服務 v3 中，當您提交工作來處理視訊時，必須告知媒體服務到何處尋找輸入視訊。 其中有一個選項是將 HTTP(s) URL 指定為工作輸入 (如本範例所示)。 請注意，目前 AMS v3 不支援透過 HTTPS URL 的區塊傳送編碼。 如需完整的範例，請參閱此 [GitHub 範例](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs)。

## <a name="net-sample"></a>.NET 範例

下列程式碼示範如何使用 HTTPS URL 輸入來建立工作。

[!code-csharp[Main](../../../media-services-v3-dotnet-quickstarts/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="next-steps"></a>後續步驟

[從本機檔案建立工作輸入](job-input-from-local-file-how-to.md).
