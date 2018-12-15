---
title: 使用 Azure Application Insights 撰寫程式碼來追蹤要求 | Microsoft Docs
description: 使用 Application Insights 撰寫程式碼來追蹤要求，讓您可以取得要求的設定檔
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: 20f408d9dd32c3fd7a0e319e4051483e3aa54dd9
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2018
ms.locfileid: "52722117"
---
# <a name="write-code-to-track-requests-with-application-insights"></a>使用 Application Insights 撰寫程式碼來追蹤要求

若要在 [效能] 頁面上查看您應用程式的設定檔，Application Insights 必須追蹤您應用程式的要求。 Application Insights 可針對以經過檢測的架構 (例如 ASP.net 和 ASP.Net Core) 為基礎的應用程式，自動追蹤其要求。 但是對於其他應用程式，例如 Azure 雲端服務背景工作角色和 Service Fabric 無狀態 API，您必須撰寫程式碼來告訴 Application Insights 何處是您要求的開頭與結尾。 撰寫此程式碼後，要求遙測就會傳送至 Application Insights，您會在 [效能] 頁面上看到遙測資料，而系統將會針對這些要求收集設定檔。 

以下是手動追蹤要求所需採取的步驟：


  1. 在應用程式存留期早期新增下列程式碼：  

        ```csharp
        using Microsoft.ApplicationInsights.Extensibility;
        ...
        // Replace with your own Application Insights instrumentation key.
        TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
        ```
      如需這個全域檢測金鑰設定的詳細資訊，請參閱[搭配 Application Insights 使用 Service Fabric](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md)。  

  1. 針對您想要檢測的任何程式碼片段，在其周圍新增 `StartOperation<RequestTelemetry>` **USING** 陳述式，如下列範例所示：

        ```csharp
        using Microsoft.ApplicationInsights;
        using Microsoft.ApplicationInsights.DataContracts;
        ...
        var client = new TelemetryClient();
        ...
        using (var operation = client.StartOperation<RequestTelemetry>("Insert_Your_Custom_Event_Unique_Name"))
        {
          // ... Code I want to profile.
        }
        ```

        不支援在另一個 `StartOperation<RequestTelemetry>` 範圍內呼叫 `StartOperation<RequestTelemetry>`。 您可以改為在巢狀範圍中使用 `StartOperation<DependencyTelemetry>`。 例如︰  
        
        ```csharp
        using (var getDetailsOperation = client.StartOperation<RequestTelemetry>("GetProductDetails"))
        {
        try
        {
          ProductDetail details = new ProductDetail() { Id = productId };
          getDetailsOperation.Telemetry.Properties["ProductId"] = productId.ToString();
        
          // By using DependencyTelemetry, 'GetProductPrice' is correctly linked as part of the 'GetProductDetails' request.
          using (var getPriceOperation = client.StartOperation<DependencyTelemetry>("GetProductPrice"))
          {
              double price = await _priceDataBase.GetAsync(productId);
              if (IsTooCheap(price))
              {
                  throw new PriceTooLowException(productId);
              }
              details.Price = price;
          }
        
          // Similarly, note how 'GetProductReviews' doesn't establish another RequestTelemetry.
          using (var getReviewsOperation = client.StartOperation<DependencyTelemetry>("GetProductReviews"))
          {
              details.Reviews = await _reviewDataBase.GetAsync(productId);
          }
        
          getDetailsOperation.Telemetry.Success = true;
          return details;
        }
        catch(Exception ex)
        {
          getDetailsOperation.Telemetry.Success = false;
        
          // This exception gets linked to the 'GetProductDetails' request telemetry.
          client.TrackException(ex);
          throw;
        }
        }
        ```
