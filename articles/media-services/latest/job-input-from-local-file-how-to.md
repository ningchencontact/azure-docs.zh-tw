---
title: 從本機檔案建立 Azure 媒體服務工作輸入 | Microsoft Docs
description: 本主題說明如何從本機檔案建立工作輸入。
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
ms.openlocfilehash: 9b366cca37e562e229e7d139426fc0b24978e366
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2018
ms.locfileid: "53412958"
---
# <a name="create-a-job-input-from-a-local-file"></a>從本機檔案建立工作輸入

在媒體服務 v3 中，當您提交工作來處理視訊時，必須告知媒體服務到何處尋找輸入視訊。 輸入視訊可以儲存為媒體服務資產，在這種情況下，您會建立一個以檔案為基礎的輸入資產 (儲存在本機或 Azure Blob 儲存體)。 本主題說明如何從本機檔案建立工作輸入。 如需完整的範例，請參閱此 [GitHub 範例](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs)。

## <a name="net-sample"></a>.NET 範例

下列程式碼說明如何建立一個輸入資產，並將它當作工作的輸入。 CreateInputAsset 函式會執行下列動作：

* 建立資產
* 取得資產[儲存體容器](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet?tabs=windows#upload-blobs-to-the-container)的 [SAS URL](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)
* 使用 SAS URL，將檔案上傳至儲存體中的容器

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="next-steps"></a>後續步驟

[從 HTTPS URL 建立工作輸入](job-input-from-http-how-to.md)。
