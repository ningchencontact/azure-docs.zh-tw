---
title: 從 HTTP(s) URL 建立 Azure 媒體服務工作輸入 | Microsoft Docs
description: 本主題說明如何從 HTTP(s) URL 建立工作輸入。
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: a47dc3e4939dd27d7ac11be823a09b8a6cba1f60
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
---
# <a name="create-a-job-input-from-an-https-url"></a>從 HTTP(s) URL 建立工作輸入

在媒體服務 v3 中，當您提交工作來處理視訊時，必須告知媒體服務到何處尋找輸入視訊。 其中有一個選項是將 HTTP(s) URL 指定為工作輸入 (如本範例所示)。 如需完整的範例，請參閱此 [github 範例](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs)。

## <a name="net-sample"></a>.Net 範例

下列程式碼示範如何使用 HTTPS URL 輸入來建立工作。

```csharp
private static Job SubmitJob(IAzureMediaServicesClient client, string transformName, string jobName)
{
    Asset outputAsset = client.Assets.CreateOrUpdate(Guid.NewGuid().ToString() + "-output", new Asset());

    JobInputHttp jobInput = 
        new JobInputHttp(files: new[] { "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/Ignite-short.mp4" });

    JobOutput[] jobOutputs =
    {
        new JobOutputAsset(outputAsset.Name),
    };

    Job job = client.Jobs.CreateOrUpdate(
        transformName,
        jobName,
        new Job
        {
            Input = jobInput,
            Outputs = jobOutputs,
        });

    return job;
}
```

## <a name="next-steps"></a>後續步驟

[從本機檔案建立工作輸入](job-input-from-local-file-how-to.md).
