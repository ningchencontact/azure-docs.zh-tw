---
title: 要移轉 Microsoft Translator Hub 工作區和專案嗎？ - 自訂翻譯工具
titleSuffix: Azure Cognitive Services
description: 將您的中樞工作區和專案移轉至自訂翻譯工具。
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: article
ms.openlocfilehash: 611cd0878e88d2e1c0a988f73b57e391c5a8551d
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/19/2018
ms.locfileid: "51975902"
---
# <a name="migrate-hub-workspace-and-projects-to-custom-translator"></a>將中樞工作區和專案移轉至自訂翻譯工具

您可以將 [Microsoft Translator Hub](https://hub.microsofttranslator.com/) 工作區和專案移轉至自訂翻譯工具。 移轉作業始於中樞。


在過程中會移轉下列項目：

1.  專案定義。

2.  定型定義將用來在自訂翻譯工具上建立新的模型定義。

3.  用於定型的平行和單一語言檔案，將會移轉為自訂翻譯工具中的新文件。

4.  自動產生的系統測試和調整資料將會匯出，並建立為自訂翻譯工具中的新文件。

對於所有已部署的定型，自訂翻譯工具將會直接定型模型，而不需要任何成本。 您可以選擇手動加以部署。

>[!Note]
>若要成功定型，自訂翻譯工具至少需要 10,000 個擷取的句子。 如果擷取的句數少於[建議的最小值](sentence-alignment.md#suggested-minimum-number-of-extracted-and-aligned-sentences)，自訂翻譯工具就無法執行定型。

任何成功但尚未部署的定型，都將移轉成為自訂翻譯工具中的草稿。

## <a name="find-custom-translator-workspace-id"></a>尋找自訂翻譯工具工作區識別碼

若要移轉 [Microsoft Translator Hub](https://hub.microsofttranslator.com/) 工作區，您必須要有自訂翻譯工具中的目的地工作區識別碼。 自訂翻譯工具中的目的地工作區，是您所有的中樞工作區和專案所應移轉到的位置。

您可以在自訂翻譯工具的 [設定] 頁面上找到目的地工作區識別碼： 

1.  移至自訂翻譯工具入口網站中的 [設定] 頁面。

2.  您可以在 [基本資訊] 區段中找到工作區識別碼。

    ![如何尋找目的地工作區識別碼](media/how-to/how-to-find-destination-ws-id.png)

3. 妥善保存您的目的地工作區識別碼，以便在移轉程序期間參考。

## <a name="migrate-workspace"></a>移轉工作區

當您將整個中樞工作區移轉至自訂翻譯工具時，您的專案、文件和定型集都會移轉至自訂翻譯工具。 在移轉之前，您必須選擇您只要移轉已部署的定型，還是要移轉所有成功的定型。

若要移轉工作區：

1.  登入 Microsoft Translator Hub。

2.  移至 [設定] 頁面。

3.  在 [設定] 頁面上，按一下 [將工作區資料移轉至自訂翻譯工具]。

    ![如何從中樞移轉](media/how-to/how-to-migrate-workspace-from-hub.png)

4.  在下一個頁面上，選取下列兩個選項之一：

    a.  僅限已部署的定型：若選取此選項，將只會移轉您已部署的系統和相關文件。

    b.  所有成功的定型：若選取此選項，將會移轉您所有成功的定型和相關文件。

    c.  輸入您在自訂翻譯工具中的目的地工作區識別碼。

    ![如何從中樞移轉](media/how-to/how-to-migrate-from-hub-screen.png)

5.  按一下 [提交要求]。

## <a name="migrate-project"></a>移轉專案

如果您想要選擇性地移轉專案，可以使用 Microsoft Translator Hub 所提供的功能。

若要移轉專案：

1.  登入 Microsoft Translator Hub。

2.  移至 [專案] 頁面。

3.  按一下適當專案的 [移轉] 連結。

    ![如何從中樞移轉](media/how-to/how-to-migrate-from-hub.png)

4.  在下一個頁面上，選取下列兩個選項之一：

    a.  僅限已部署的定型：若選取此選項，將只會移轉您已部署的系統和相關文件。 

    b.  所有成功的定型：若選取此選項，將會移轉您所有成功的定型和相關文件。

    c.  輸入您在自訂翻譯工具中的目的地工作區識別碼。

    ![如何從中樞移轉](media/how-to/how-to-migrate-from-hub-screen.png)

5.  按一下 [提交要求]。

## <a name="migration-history"></a>移轉歷程記錄

如果您要求從中樞進行工作區/專案移轉，您將可在自訂翻譯工具的 [設定] 頁面中找到移轉歷程記錄。 

若要檢視移轉歷程記錄，請遵循下列步驟：

1.  移至自訂翻譯工具入口網站中的 [設定] 頁面。

2.  在 [設定] 頁面的 [移轉歷程記錄] 區段中，按一下 [移轉歷程記錄]。

    ![移轉歷程記錄](media/how-to/how-to-migration-history.png)

[移轉歷程記錄] 頁面會針對您所要求的各個移轉顯示下列摘要資訊。

1.  移轉者：提交此移轉要求的使用者名稱及其電子郵件

2.  移轉日期：移轉的日期和時間戳記

3.  專案：要求移轉的專案數目與成功移轉的專案數目。

4.  定型：要求移轉的定型數目與成功移轉的定型數目。

5.  文件：要求移轉的文件數目與成功移轉的文件數目。

    ![移轉歷程記錄詳細資料](media/how-to/how-to-migration-history-details.png)

如果您希望專案、定型和文件有更詳細的相關移轉報告，您可以選擇將詳細資料匯出為 CSV。

>[!Note]
>目前僅支援移轉至有 NMT 語言存在的語言組。 請查看目前[支援的語言 NMT](https://www.microsoft.com/translator/business/languages/) 清單。 對於沒有 NMT 語言存在的語言組，資料將會從中樞移至自訂翻譯工具，但無法對這些語言組進行定型。

## <a name="custom-translator-versus-hub"></a>自訂翻譯工具與中樞

下表比較 Microsoft Translator Hub 與自訂翻譯工具之間的功能。

|   | 中樞 | 自訂翻譯工具 |
|:-----|:----:|:----:|
|自訂功能狀態   | 正式運作  | 預覽 |
| 文字 API 版本  | V2    | V3  |
| SMT 自訂 | 是   | 否 |
| NMT 自訂 | 否    | 是 |
| 新的統一語音服務自訂 | 否    | 是 |
| 不追蹤 | 是 | 是 |

## <a name="next-steps"></a>後續步驟

- [將模型定型](how-to-train-model.md)。
- 透過 [Microsoft Translator Text API V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl) 開始使用您已部署的自訂翻譯模型。
