---
title: Azure Key Vault 節流指南
description: Key Vault 節流措施會限制並行呼叫數目，以防止過度使用資源。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: ''
ms.service: key-vault
ms.topic: conceptual
ms.date: 05/10/2018
ms.author: mbaldwin
ms.openlocfilehash: f10f40551701cafd94692afc0916972b1fd73aff
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2019
ms.locfileid: "70883040"
---
# <a name="azure-key-vault-throttling-guidance"></a>Azure Key Vault 節流指南

節流是您起始來限制 Azure 服務並行呼叫數目，以避免過度使用資源的程序。 Azure Key Vault (AKV) 被設計來處理大量的要求。 如果產生大量的要求，針對用戶端的要求進行節流有助於維護 AKV 服務的最佳效能和可靠性。

節流限制視情節而異。 例如，如果您正在執行大量的寫入，則節流的可能性要高於僅執行讀取作業的可能性。

## <a name="how-does-key-vault-handle-its-limits"></a>Key Vault 如何處理其限制？

Key Vault 中的服務限制是為了防止濫用資源，並確保所有 Key Vault 用戶端的服務品質。 超過服務閾值時，Key Vault 可以限制一段時間內來自該用戶端的任何進一步要求。 當這種情況發生時，Key Vault 會傳回 HTTP 狀態碼 429 (太多要求)，且要求會失敗。 此外，傳回狀態碼 429 的失敗要求會計入 Key Vault 所追蹤的節流限制中。 

如果您有需要更高節流限制的有效商務案例，請與我們連絡。


## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>如何為您的應用程式進行節流處理，以回應服務限制

以下是您在服務節流時應執行的**最佳作法**：
- 減少每個要求的作業數目。
- 減少要求的頻率。
- 避免立即重試。 
    - 所有要求都會讓您的使用量限制累加。

當您實作應用程式的錯誤處理時，請使用 HTTP 錯誤碼 429 來偵測用戶端節流的需求。 如果要求再次失敗並傳回 HTTP 429 錯誤碼，您仍然遇到 Azure 服務限制。 繼續使用建議的用戶端節流方法，重試要求直到成功為止。

實作指數輪詢的程式碼如下所示。 
```
    public sealed class RetryWithExponentialBackoff
    {
        private readonly int maxRetries, delayMilliseconds, maxDelayMilliseconds;

        public RetryWithExponentialBackoff(int maxRetries = 50,
            int delayMilliseconds = 200,
            int maxDelayMilliseconds = 2000)
        {
            this.maxRetries = maxRetries;
            this.delayMilliseconds = delayMilliseconds;
            this.maxDelayMilliseconds = maxDelayMilliseconds;
        }

        public async Task RunAsync(Func<Task> func)
        {
            ExponentialBackoff backoff = new ExponentialBackoff(this.maxRetries,
                this.delayMilliseconds,
                this.maxDelayMilliseconds);
            retry:
            try
            {
                await func();
            }
            catch (Exception ex) when (ex is TimeoutException ||
                ex is System.Net.Http.HttpRequestException)
            {
                Debug.WriteLine("Exception raised is: " +
                    ex.GetType().ToString() +
                    " –Message: " + ex.Message +
                    " -- Inner Message: " +
                    ex.InnerException.Message);
                await backoff.Delay();
                goto retry;
            }
        }
    }

    public struct ExponentialBackoff
    {
        private readonly int m_maxRetries, m_delayMilliseconds, m_maxDelayMilliseconds;
        private int m_retries, m_pow;

        public ExponentialBackoff(int maxRetries, int delayMilliseconds,
            int maxDelayMilliseconds)
        {
            m_maxRetries = maxRetries;
            m_delayMilliseconds = delayMilliseconds;
            m_maxDelayMilliseconds = maxDelayMilliseconds;
            m_retries = 0;
            m_pow = 1;
        }

        public Task Delay()
        {
            if (m_retries == m_maxRetries)
            {
                throw new TimeoutException("Max retry attempts exceeded.");
            }
            ++m_retries;
            if (m_retries < 31)
            {
                m_pow = m_pow << 1; // m_pow = Pow(2, m_retries - 1)
            }
            int delay = Math.Min(m_delayMilliseconds * (m_pow - 1) / 2,
                m_maxDelayMilliseconds);
            return Task.Delay(delay);
        }
    }
```


在用戶端 C\#應用程式中使用此程式碼相當簡單。 以下範例會示範如何使用 HttpClient 類別。

```csharp
public async Task<Cart> GetCartItems(int page)
{
    _apiClient = new HttpClient();
    //
    // Using HttpClient with Retry and Exponential Backoff
    //
    var retry = new RetryWithExponentialBackoff();
    await retry.RunAsync(async () =>
    {
        // work with HttpClient call
        dataString = await _apiClient.GetStringAsync(catalogUrl);
    });
    return JsonConvert.DeserializeObject<Cart>(dataString);
}
```

請注意，此程式碼僅適用做為概念證明。 

### <a name="recommended-client-side-throttling-method"></a>建議使用的用戶端節流方法

針對 HTTP 錯誤碼 429，使用指數型輪詢方法開始為您的用戶端進行節流處理：

1. 等候 1 秒，重試要求
2. 如果等候 2 秒仍然進行節流處理，重試要求
3. 如果等候 4 秒仍然進行節流處理，重試要求
4. 如果等候 8 秒仍然進行節流處理，重試要求
5. 如果等候 16 秒仍然進行節流處理，重試要求

此時，您應該不會收到 HTTP 429 回應碼。

## <a name="see-also"></a>另請參閱

如需在 Microsoft Cloud 上進行節流處理的詳細資訊，請參閱[節流模式](https://docs.microsoft.com/azure/architecture/patterns/throttling) \(英文\)。

