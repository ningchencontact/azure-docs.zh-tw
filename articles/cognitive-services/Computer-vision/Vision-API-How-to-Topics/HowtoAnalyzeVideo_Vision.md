---
title: 使用電腦視覺 API 進行即時影片分析 | Microsoft Docs
description: 了解如何使用認知服務中的電腦視覺 API，針對從即時影片資料流中擷取的畫面，執行接近即時的分析。
services: cognitive-services
author: KellyDF
manager: corncar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 01/20/2017
ms.author: kefre
ms.openlocfilehash: d75b1a887e5e4557d5464d8062e1bde628e7adab
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2018
ms.locfileid: "35368558"
---
# <a name="how-to-analyze-videos-in-real-time"></a>如何即時分析影片
本指南將示範如何在從即時視訊資料流擷取的畫面上，執行近乎即時的分析。 這類系統中的基本元件如下：
- 從影片來源取得畫面
- 選取要分析的畫面
- 將這些畫面提交給 API
- 取用 API 呼叫所傳回的每一筆分析結果

這些範例是用 C# 撰寫的，而且程式碼可於下列 GitHub 位置取得：[https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) \(英文\)。

## <a name="the-approach"></a>方法
在視訊資料流上執行近乎即時分析時，有多種方式可以解決所遇到的問題。 我們會先舉出其中的三種方法，並從複雜度較低的方法開始。

### <a name="a-simple-approach"></a>簡易方法
針對接近即時的分析系統，最簡單的設計將會是無限迴圈，其中我們會在每個反覆項目中抓取一個畫面、分析它，然後取用其結果：
```CSharp
while (true)
{
    Frame f = GrabFrame();
    if (ShouldAnalyze(f))
    {
        AnalysisResult r = await Analyze(f);
        ConsumeResult(r);
    }
}
```
如果我們的分析包括輕量型的用戶端演算法，這個方法就很合適。 不過，當我們的分析是在雲端中進行，其涉及的延遲將會使 API 呼叫需要花費幾秒鐘的時間；在這段時間內，系統並不會擷取影像，因此我們的執行緒基本上不會執行任何操作。 因此，我們的畫面播放速率上限將會受限於 API 呼叫的延遲。

### <a name="parallelizing-api-calls"></a>平行處理 API 呼叫
雖然簡易的單一執行緒迴圈很適合用於輕量型的用戶端演算法，它並不適用於雲端 API 呼叫所涉及的延遲情況。 解決這個問題的方式，就是讓長時間執行的 API 呼叫與畫面抓取平行執行。 在 C# 中，我們可以使用以工作為基礎的平行處理機制來達到這個目的，例如：
```CSharp
while (true)
{
    Frame f = GrabFrame();
    if (ShouldAnalyze(f))
    {
        var t = Task.Run(async () => 
        {
            AnalysisResult r = await Analyze(f);
            ConsumeResult(r);
        }
    }
}
```
這會在獨立的工作中啟動每一項分析，並在我們繼續抓取新畫面時於背景中執行。 這能在等候 API 呼叫傳回時避免封鎖主執行緒；不過，此方法將無法提供一些和簡易版本相同的保證，也就是多個 API 呼叫可能會以平行方式進行，並導致以錯誤的順序傳回結果。 這也可能會造成多個執行緒同時進入 ConsumeResult() 函式，這在函式沒有具備安全執行緒的情況下，是一件很危險的事。 最後，由於這個簡易的程式碼並不會追蹤所建立的工作，因此例外狀況會以無訊息方式消失。 因此，我們要新增的最後一個元素是「取用者」執行緒，它會追蹤分析工作、引發例外狀況、終止長時間執行的工作，以及確保系統會以正確的順序一次取用一個結果。

### <a name="a-producer-consumer-design"></a>產生者-取用者設計
在我們的最後一個「產生者-取用者」系統中，會有一個和前面的無限迴圈非常相似的產生者執行緒。 不過，和先前會立即取用分析結果不同，產生者只會將工作置於佇列中以進行追蹤。
```CSharp
// Queue that will contain the API call tasks. 
var taskQueue = new BlockingCollection<Task<ResultWrapper>>();
     
// Producer thread. 
while (true)
{
    // Grab a frame. 
    Frame f = GrabFrame();
 
    // Decide whether to analyze the frame. 
    if (ShouldAnalyze(f))
    {
        // Start a task that will run in parallel with this thread. 
        var analysisTask = Task.Run(async () => 
        {
            // Put the frame, and the result/exception into a wrapper object.
            var output = new ResultWrapper(f);
            try
            {
                output.Analysis = await Analyze(f);
            }
            catch (Exception e)
            {
                output.Exception = e;
            }
            return output;
        }
        
        // Push the task onto the queue. 
        taskQueue.Add(analysisTask);
    }
}
```
此方法也包含一個取用者執行緒，它會從佇列取出工作並等候它們完成，並於之後顯示結果或引發所擲回的例外狀況。 藉由使用佇列，我們將能保證系統會按照正確的順序一次取用一個結果，而不會限制系統的畫面播放速率上限。
```CSharp
// Consumer thread. 
while (true)
{
    // Get the oldest task. 
    Task<ResultWrapper> analysisTask = taskQueue.Take();
 
    // Await until the task is completed. 
    var output = await analysisTask;
     
    // Consume the exception or result. 
    if (output.Exception != null)
    {
        throw output.Exception;
    }
    else
    {
        ConsumeResult(output.Analysis);
    }
}
```

## <a name="implementing-the-solution"></a>實作解決方案
### <a name="getting-started"></a>開始使用
為了協助您儘快使應用程式開始運作並正常執行，我們已實作上述的系統，其已設計為能夠提供足夠的彈性以實作各種案例，同時非常容易上手。 若要存取其程式碼，請移至 [ https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis ](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis) \(英文\)。

該程式庫包含 FrameGrabber 類別，它會實作上述的產生者-取用者系統，以處理來自網路攝影機的視訊畫面。 使用者可以指定 API 呼叫的確切形式，且該類別會使用事件來通知呼叫程式碼，以讓它能夠得知已取得新畫面或有新的分析結果可用的時機。

為了說明一些可能性，有兩個範例應用程式會使用該程式庫。 第一個範例是簡易的主控台應用程式，其簡化版本已重現於下方。 它會抓取預設網路攝影機的畫面，然後提交給臉部 API 以進行臉部偵測。
```CSharp
using System;
using VideoFrameAnalyzer;
using Microsoft.ProjectOxford.Face;
using Microsoft.ProjectOxford.Face.Contract;
     
namespace VideoFrameConsoleApplication
{
    class Program
    {
        static void Main(string[] args)
        {
            // Create grabber, with analysis type Face[]. 
            FrameGrabber<Face[]> grabber = new FrameGrabber<Face[]>();
            
            // Create Face API Client. Insert your Face API key here.
            FaceServiceClient faceClient = new FaceServiceClient("<subscription key>");

            // Set up our Face API call.
            grabber.AnalysisFunction = async frame => return await faceClient.DetectAsync(frame.Image.ToMemoryStream(".jpg"));

            // Set up a listener for when we receive a new result from an API call. 
            grabber.NewResultAvailable += (s, e) =>
            {
                if (e.Analysis != null)
                    Console.WriteLine("New result received for frame acquired at {0}. {1} faces detected", e.Frame.Metadata.Timestamp, e.Analysis.Length);
            };
            
            // Tell grabber to call the Face API every 3 seconds.
            grabber.TriggerAnalysisOnInterval(TimeSpan.FromMilliseconds(3000));

            // Start running.
            grabber.StartProcessingCameraAsync().Wait();

            // Wait for keypress to stop
            Console.WriteLine("Press any key to stop...");
            Console.ReadKey();
            
            // Stop, blocking until done.
            grabber.StopProcessingAsync().Wait();
        }
    }
}
```
第二個範例應用程式比較有趣一點，它可讓您選擇要針對視訊畫面呼叫的 API。 應用程式的左側會顯示即時視訊的預覽，在右側則會顯示疊加在對應畫面上的 API 最新結果。

在大部分模式中，在左側的即時視訊和右側的視覺化分析之間，會存在明顯的延遲。 這個延遲是做出 API 呼叫所需的時間。 唯一的例外是 "EmotionsWithClientFaceDetect" 模式，此模式並不會出現延遲，因為它會在用戶端電腦上使用 OpenCV 執行本機臉部偵測，然後再將影像提交認知服務。 藉由這麼做，我們可以立即將偵測到的臉部視覺化，然後在 API 呼叫於稍後傳回時再更新表情。 這示範出「混合」方法的可能性，其中部分較簡易的處理可在用戶端上執行，並在必要時使用認知服務 API 以更高階的分析方式做出進一步調整。

![HowToAnalyzeVideo](../../Video/Images/FramebyFrame.jpg)

### <a name="integrating-into-your-codebase"></a>整合至您的程式碼基底
若要開始進行此範例，請遵循下列步驟：

1. 從[訂用帳戶](https://azure.microsoft.com/try/cognitive-services/)取得視覺 API 的 API 金鑰。 如需進行視訊畫面分析，適用的 API 為：
    - [電腦視覺 API](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
    - [表情 API](https://docs.microsoft.com/azure/cognitive-services/emotion/home) \(英文\)
    - [臉部 API](https://docs.microsoft.com/azure/cognitive-services/face/overview)
2. 複製 [Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) \(英文\) GitHub 存放庫

3. 在 Visual Studio 2015 中開啟該範例，然後建置並執行範例應用程式：
    - 針對 BasicConsoleSample，臉部 API 金鑰已直接硬式編碼於 [BasicConsoleSample/Program.cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs) \(英文\) 中。
    - 針對 LiveCameraSample，金鑰應該要輸入至應用程式的 [設定] 窗格中。 它們會以使用者資料的形式跨工作階段保存。
        

當您準備好進行整合的時候，您只需**參考自己專案中的 VideoFrameAnalyzer 程式庫**即可。 



## <a name="developer-code-of-conduct"></a>開發人員行為準則
和所有認知服務一樣，使用我們的 API 和範例進行開發的開發人員都應當遵守[適用於 Microsoft 認知服務的開發人員管理辦法](https://azure.microsoft.com/support/legal/developer-code-of-conduct/) \(英文\)。 


了解 VideoFrameAnalyzer 功能的影像、語音、視訊或文字都會使用 Microsoft 認知服務。 Microsoft 將會收到您 (透過此應用程式) 上傳的影像、音訊、視訊和其他資料，並可能會將它們用於改善服務的目的之上。 我們要求您協助保護由您的應用程式傳送至 Microsoft 認知服務之資料的所有人。 


## <a name="summary"></a>總結
在本指南中，您已學會如何使用臉部、電腦視覺和表情 API 在即時視訊資料流上執行接近即時的分析，以及如何使用我們的範例程式碼來開始進行。 您可以從 [Microsoft 認知服務註冊頁面](https://azure.microsoft.com/try/cognitive-services/)取得免費的 API 金鑰，來開始建置應用程式。 

歡迎您在 [GitHub 存放庫](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) \(英文\) 中提供意見反應和建議。針對其他更廣泛的 API 意見反應，請移至我們的 [UserVoice 網站](https://cognitive.uservoice.com/) \(英文\)。

