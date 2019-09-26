---
title: 支援的資料來源 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker 會自動從半結構化的內容 (例如，儲存為網頁、PDF 檔案或 MS Word DOC 檔的常見問題集、產品手冊、指導方針、支援文件和原則) 擷取問答組合。 內容也可以從結構化 QnA 內容檔案新增至知識庫。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: diberry
ms.openlocfilehash: 4e24246ec4ed30ec93bf8e113d659bc5e3600913
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/19/2019
ms.locfileid: "71130126"
---
# <a name="data-sources-for-qna-maker-content"></a>QnA Maker 內容的資料來源

QnA Maker 會自動從半結構化的內容 (例如，儲存為網頁、PDF 檔案或 MS Word DOC 檔的常見問題集、產品手冊、指導方針、支援文件和原則) 擷取問答組合。 內容也可以從結構化 QnA 內容檔案新增至知識庫。 

## <a name="data-types"></a>資料類型

下表摘要說明 QnA Maker 所支援的內容類型和檔案格式。

|來源類型|內容類型| 範例|
|--|--|--|
|URL|常見問題集<br> (一般，含有區段或含有主題首頁)<br>支援頁面 <br> (單頁操作說明文章、疑難排解文章等等。)|[一般的常見問題集](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs)、 <br>[具有連結的常見問題集](https://www.microsoft.com/software-download/faq)、<br> [具有主題首頁的常見問題集](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[支援文件](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|PDF/DOC|常見問題集、<br> 產品手冊、<br> 小手冊、<br> 文件、<br> 傳單原則、<br> 支援指南、<br> 結構化 QnA<br> 等|[Structured QnA.doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx)、<br> [Sample Product Manual.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf)、<br> [Sample semi-structured.doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx)、<br> [Sample white paper.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf)|
|Excel|結構化 QnA 檔案<br> (包括 RTF、HTML 支援)|[Sample QnA FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)|
|TXT/TSV|結構化 QnA 檔案|[Sample chit-chat.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

## <a name="data-source-locations"></a>資料來源位置

資料來源位置是**公用 url 或**檔案，不需要驗證。 

如果您需要驗證資料來源，請考慮下列方法，將該資料匯入 QnA Maker：

* [手動下載檔案](#download-file-from-authenticated-data-source-location)並匯入 QnA Maker
* 已驗證[Sharepoint 位置](#import-file-from-authenticated-sharepoint)的匯入檔案 

### <a name="download-file-from-authenticated-data-source-location"></a>從已驗證的資料來源位置下載檔案

如果您有已驗證的檔案（而不是在已驗證的 Sharepoint 位置）或 URL，另一個選項是將檔案從已驗證的網站下載到本機電腦，然後從本機電腦將檔案新增至知識庫。

### <a name="import-file-from-authenticated-sharepoint"></a>從已驗證的 Sharepoint 匯入檔案 

[Sharepoint 資料來源位置](../How-to/add-sharepoint-datasources.md)允許提供**已驗證的**檔案。 Sharepoint 資源必須是檔案，而不是網頁。 如果 URL 的結尾是 web 延伸模組，例如 **。ASPX**，它不會從 Sharepoint 匯入 QnA Maker。


## <a name="faq-urls"></a>常見問題集 URL

QnA Maker 可支援 3 種不同形式的常見問題集網頁：一般常見問題集網頁、具有連結的常見問題集網頁、具有主題首頁的常見問題集網頁。

### <a name="plain-faq-pages"></a>一般的常見問題集頁面

這是最常用的常見問題集頁面類型，在此處，解答會在相同的頁面中緊接在問題後面。 

以下是一般常見問題集網頁的範例：

![知識庫的一般常見問題集頁面範例](../media/qnamaker-concepts-datasources/plain-faq.png) 

 
### <a name="faq-pages-with-links"></a>具有連結的常見問題集網頁 

在這種常見問題集網頁中，問題會彙總在一起並連結至解答 (位於相同網頁的不同區段，或位於不同網頁)。

以下是連結位於相同網頁區段的常見問題集網頁範例：

 ![知識庫的區段連結常見問題集頁面範例](../media/qnamaker-concepts-datasources/sectionlink-faq.png) 


### <a name="faq-pages-with-a-topics-homepage"></a>具有主題首頁的常見問題集網頁

這種常見問題集會有首頁，首頁會有主題，且每個主題都是其相關 QnA 在不同網頁的連結。 在此，QnA Maker 會搜耙所有連結頁面，以擷取對應的問題和解答。

以下是主題首頁上有不同網頁常見問題集區段連結的常見問題集網頁範例。 

 ![知識庫的深層連結常見問題集頁面範例](../media/qnamaker-concepts-datasources/topics-faq.png) 


### <a name="support-urls"></a>支援 URL

QnA Maker 可處理半結構化的支援網頁，例如 Web 文件，其中會說明如何執行指定工作、如何診斷及解決特定問題，以及指定程序的最佳做法是什麼。 內容若具有包含階層式標題的明確結構，擷取將會有最佳效果。

> [!NOTE]
> 擷取支援文章是一項新功能，並且處於早期階段。 其適合用於結構完整且不包含複雜標頭/頁尾的簡單頁面。

![QnA Maker 支援從半結構化的網頁進行擷取，其中會以階層式標題顯示清楚的結構](../media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)


## <a name="pdf-doc-files"></a>PDF/DOC 檔

QnA Maker 可以處理 PDF 或 DOC 檔中的半結構化內容，並將它轉換成 QnA。 可以完好擷取的良好檔案，其內容會組織成某種結構化形式，並會以妥善定義的區段來表示。 區段可以再分成子區段或子主題。 文件若具有包含階層式標題的明確結構，擷取將會有最佳效果。

QnA Maker 可根據視覺線索 (例如字型大小、字型樣式、編號、色彩等等) 來識別檔案中的區段和子區段以及關聯性。半結構化的 PDF 或 DOC 檔可以是手冊、常見問題集、指導方針、原則、摺頁冊、傳單和其他諸多類型的檔案。 以下是這些檔案類型的一些範例。

### <a name="product-manuals"></a>產品手冊

手冊通常式隨附於產品的指引內容。 它可協助使用者設定、使用、維護產品，並對其問題進行疑難排解。 當 QnA Maker 處理手冊時，它會將標題和子標題擷取為問題，並將後續的內容擷取為解答。 請參閱[這裡](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf)的範例。

以下是具有索引頁面與階層式內容的手冊範例

 ![知識庫的產品手冊範例](../media/qnamaker-concepts-datasources/product-manual.png) 

> [!NOTE]
> 手冊若具有目錄及/或索引頁面，和包含階層式標題的明確結構，擷取將會有最佳效果。

### <a name="brochures-guidelines-papers-and-other-files"></a>摺頁冊、指導方針、白皮書及其他檔案

許多其他類型的文件經過處理後也能產生問答組合，但前提是這些文件要有明確的結構和版面配置。 它們包括：摺頁冊、指導方針、報告、白皮書、科學論文、原則、書籍等。請參閱[這裡](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx)的範例。

以下是沒有索引的半結構化文件範例：

 ![Azure Blob 儲存體的半結構化文件](../media/qnamaker-concepts-datasources/semi-structured-doc.png) 

### <a name="structured-qna-document"></a>結構化 QnA 文件

DOC 檔中結構化問答的格式，會採用逐行交替列出問題和解答的形式，每行一個問題，接著的下一行則是其解答，如下所示： 

```text
Question1

Answer1

Question2

Answer2
```

以下是結構化 QnA Word 文件的範例：

 ![知識庫的結構化 QnA 文件範例](../media/qnamaker-concepts-datasources/structured-qna-doc.png) 

## <a name="structured-txt-tsv-and-xls-files"></a>結構化的 TXT、TSV 和 XLS 檔案

採用結構化 .txt、.tsv 或 .xls 檔案形式的 QnA 也可上傳至 QnA Maker，以便建立或擴大知識庫。  這些檔案可以是純文字，也可以具有 RTF 或 HTML 的內容。 

| 問題  | 回答  | 中繼資料（1個索引鍵：1值） |
|-----------|---------|-------------------------|
| 問題 1 | 解答 1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| 問題 2 | 解答 2 |      `Key:Value`           |

來源檔案中的任何其他資料行都會被忽略。

### <a name="example-of-structured-excel-file"></a>結構化 Excel 檔案的範例

以下是具有 HTML 內容的結構化 QnA .xls 檔案範例：

 ![知識庫的結構化 QnA Excel 範例](../media/qnamaker-concepts-datasources/structured-qna-xls.png)

### <a name="example-of-alternate-questions-for-single-answer-in-excel-file"></a>Excel 檔案中單一答案的替代問題範例

以下是結構化 QnA *.xls*檔案的範例，其中單一答案有數個替代問題：

 ![Excel 檔案中單一答案的替代問題範例](../media/qnamaker-concepts-datasources/xls-alternate-question-example.png)

匯入檔案之後，問與答配對會在知識庫中，如下所示：

 ![匯入知識庫之單一回應的替代問題螢幕擷取畫面](../media/qnamaker-concepts-datasources/xls-alternate-question-example-after-import.png)

## <a name="structured-data-format-through-import"></a>透過匯入的結構化資料格式

匯入知識庫後，將會取代現有知識庫的內容。 匯入時必須要有包含資料來源資訊的結構化 .tsv 檔案。 這項資訊可協助 QnA Maker 將問答組分組，並將其歸於特定資料來源。

| 問題  | 回答  | Source| 中繼資料（1個索引鍵：1值） |          
|-----------|---------|----|---------------------|
| 問題 1 | 解答 1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| 問題 2 | 解答 2 | 編輯|    `Key:Value`       |

## <a name="editorially-add-to-knowledge-base"></a>透過編輯來新增至知識庫

如果您沒有既有的內容可填入知識庫，您也可以透過編輯將 QnA 新增至 QnA Maker 知識庫中。 在[這裡](../How-To/edit-knowledge-base.md)了解如何更新知識庫。

<a href="#formatting-considerations"></a>

## <a name="formatting-considerations"></a>格式化考量

匯入檔案或 URL 之後，QnA Maker 會以[markdown 格式](https://en.wikipedia.org/wiki/Markdown)轉換並儲存您的內容。 轉換程式會在文字中加入新行，例如`\n\n`。 Markdown 格式的知識可協助您瞭解已轉換的內容，並管理您的知識庫內容。 

如果您直接在知識庫中新增或編輯內容，請使用**markdown 格式**來建立 rtf 文字內容，或變更已在解答中的 markdown 格式內容。 QnA Maker 支援許多 markdown 格式，可將豐富的文字功能帶入您的內容。 不過，用戶端應用程式（例如聊天機器人）可能不支援相同的一組 markdown 格式。 測試用戶端應用程式的答案顯示是很重要的。 

以下是您可以在 QnA Maker 中使用的 markdown 格式清單： 

|用途|格式|範例 markdown|轉譯<br>如聊天機器人中所顯示|
|--|--|--|--|
2個句子之間的新行。|`\n\n`|`How can I create a bot with \n\n QnA Maker?`|![將兩個句子之間的新行格式化](../media/qnamaker-concepts-datasources/format-newline.png)|
|從 h1 到 h6 的標頭，的`#`數位代表哪一個標頭。 1 `#`是 h1。|`\n# text \n## text \n### text \n####text \n#####text` |`## Creating a bot \n ...text.... \n### Important news\n ...text... \n### Related Information\n ....text...`<br><br>`\n# my h1 \n## my h2\n### my h3 \n#### my h4 \n##### my h5`|![具有 markdown 標頭的格式](../media/qnamaker-concepts-datasources/format-headers.png)<br>![格式為 markdown 標頭 H1 至 H5](../media/qnamaker-concepts-datasources/format-h1-h5.png)|
|斜體 |`*text*`|`How do I create a bot with *QnA Maker*?`|![具有斜體的格式](../media/qnamaker-concepts-datasources/format-italics.png)|
|字串（粗體）|`**text**`|`How do I create a bot with **QnA Maker**?`|![具有強式標記的格式（粗體）](../media/qnamaker-concepts-datasources/format-strong.png)|
|連結的 URL|`[text](https://www.my.com)`|`How do I create a bot with [QnA Maker](https://www.qnamaker.ai)?`|![URL 的格式（超連結）](../media/qnamaker-concepts-datasources/format-url.png)|
|\* 公用影像的 URL|`![text](https://www.my.com/image.png)`|`How can I create a bot with ![QnAMaker](https://review.docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/media/qnamaker-how-to-key-management/qnamaker-resource-list.png)`|![公用影像 URL 的格式 ](../media/qnamaker-concepts-datasources/format-image-url.png)|
|刪除線|`~~text~~`|`some ~~questoins~~ questions need to be asked`|![刪除線的格式](../media/qnamaker-concepts-datasources/format-strikethrough.png)|
|粗體和斜體|`***text***`|`How can I create a ***QnA Maker*** bot?`|![粗體和斜體的格式](../media/qnamaker-concepts-datasources/format-bold-italics.png)|
|連結的粗體 URL|`[**text**](https://www.my.com)`|`How do I create a bot with [**QnA Maker**](https://www.qnamaker.ai)?`|![粗體 URL 的格式](../media/qnamaker-concepts-datasources/format-bold-url.png)|
|連結的斜體 URL|`[*text*](https://www.my.com)`|`How do I create a bot with [*QnA Maker*](https://www.qnamaker.ai)?`|![斜體 URL 的格式](../media/qnamaker-concepts-datasources/format-url-italics.png)|
|Escape markdown 符號|`\*text\*`|`How do I create a bot with \*QnA Maker\*?`|![斜體 URL 的格式](../media/qnamaker-concepts-datasources/format-escape-markdown-symbols.png)|
|已排序清單|`\n 1. item1 \n 1. item2`|`This is an ordered list: \n 1. List item 1 \n 1. List item 2`<br>上述範例使用內建于 markdown 的自動編號。<br>`This is an ordered list: \n 1. List item 1 \n 2. List item 2`<br>上述範例使用明確編號。|![已排序清單的格式](../media/qnamaker-concepts-datasources/format-ordered-list.png)|
|未排序清單|`\n * item1 \n * item2`<br>或<br>`\n - item1 \n - item2`|`This is an ordered list: \n * List item 1 \n * List item 2`|![未排序清單的格式](../media/qnamaker-concepts-datasources/format-unordered-list.png)|
|嵌套清單|`\n * Parent1 \n\t * Child1 \n\t * Child2 \n * Parent2`<br><br>`\n * Parent1 \n\t 1. Child1 \n\t * Child2 \n 1. Parent2`<br><br>您可以將已排序和未排序的清單一併嵌套在一起。 `\t`索引標籤表示子項目的縮排層級。|`This is an unordered list: \n * List item 1 \n\t * Child1 \n\t * Child2 \n * List item 2`<br><br>`This is an ordered nested list: \n 1. Parent1 \n\t 1. Child1 \n\t 1. Child2 \n 1. Parent2`|![嵌套未排序清單的格式](../media/qnamaker-concepts-datasources/format-nested-unordered-list.png)<br>![嵌套排序清單的格式](../media/qnamaker-concepts-datasources/format-nested-ordered-list.png)|

\* QnA Maker 不會以任何方式處理影像。 這是用戶端應用程式用來呈現影像的角色。 

如果您想要使用更新/取代知識庫 Api 來新增內容，且內容/檔案包含 html 標籤，您可以藉由確保標記的開頭和結尾轉換成編碼格式，來保留檔案中的 HTML。

| 保留 HTML  | API 要求中的標記法  | 標記法（KB） |
|-----------|---------|-------------------------|
| 是 | \&lt; br\&gt; | &lt;b&gt; |
| 是 | \&lt; h3\&gt; 標\&頭 lt;\&/h3 gt; | &lt;h3&gt;標&lt;頭/h3&gt; |

此外，在 KB 中，CR LF （\r\n）會轉換為 \n。 LF （\n）會保持不被保留。 如果您想要將任何 escape 序列（例如 \t 或 \n）轉義，您可以使用反斜線，\\例如\\： '\\r\\n\\' 和 '\\t '

## <a name="editing-your-knowledge-base-locally"></a>在本機編輯您的知識庫

在建立知識庫之後，建議您在 [QnA Maker 入口網站](https://qnamaker.ai)中對知識庫文字進行編輯，而不要透過本機檔案匯出和重新匯入。 不過，有時您可能需要在本機編輯知識庫。 

請從 [設定] 頁面匯出知識庫，然後使用 Microsoft Excel 來編輯知識庫。 如果您選擇使用另一個應用程式來編輯已匯出的 TSV 檔案，應用程式可能會因為它不完全符合 TSV 規範而造成語法錯誤。 Microsoft Excel 的 TSV 檔案通常不會造成任何格式設定錯誤。 

完成編輯之後，請從 [設定] 檔案重新匯入 TSV 檔案。 這會以匯入的知識庫完全取代目前的知識庫。 

## <a name="testing-your-markdown"></a>測試您的 Markdown

請使用 **[CommonMark](https://commonmark.org/help/tutorial/index.html)** \(英文\) 教學課程來驗證您的 Markdown。 此教學課程具有 [Try it] \(試用\) 功能，可快速進行複製/貼上驗證。 

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [設定 QnA Maker 服務](../How-To/set-up-qnamaker-service-azure.md)

## <a name="see-also"></a>另請參閱 

[QnA Maker 概觀](../Overview/overview.md)
