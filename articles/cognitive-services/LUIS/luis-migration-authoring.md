---
title: 遷移至 Azure 資源以供撰寫
titleSuffix: Azure Cognitive Services
description: 遷移至 Azure 撰寫資源金鑰。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: diberry
ms.openlocfilehash: 19852fe3a6925ada3dea141a1472683ee264f6d5
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973303"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>遷移至 Azure 資源撰寫金鑰

Language Understanding （LUIS）編寫驗證從電子郵件帳戶變更為 Azure 資源。 

## <a name="why-migrate"></a>為何要移轉？

使用 Azure 資源進行撰寫，可讓您（身為資源的擁有者）控制撰寫的存取權。 您可以建立及命名撰寫資源，以管理不同的作者群組。 

例如，如果您有兩種類型的 LUIS 應用程式正在撰寫中，但有不同的成員，您可以建立兩個不同的撰寫資源，並指派參與者。 Azure 撰寫資源會控制授權。 

> [!Note]
> 在遷移之前，共同_作者稱為「_ 共同作業者」。 遷移之後，_參與者_的 Azure 角色會用於相同的功能。

## <a name="what-is-migrating"></a>什麼是遷移？

遷移包括：

* LUIS、擁有者和參與者的所有使用者。
* **所有**應用程式。
* **單向**遷移。

擁有者無法選擇要遷移的應用程式子集，而且進程無法復原。 

遷移不是： 

* 收集共同作業者並自動移動或新增至 Azure 撰寫資源的程式。 身為應用程式擁有者，您必須完成此步驟。 此步驟需要適當資源的許可權。
* 建立和指派預測執行時間資源的進程。 如果您需要預測執行時間資源，這是[個別的進程](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal)，而且不會變更。 

## <a name="how-are-the-apps-migrating"></a>應用程式的遷移方式為何？

[LUIS 入口網站](https://www.luis.ai)提供了遷移程式。 

在下列情況中，系統會要求您進行遷移：

* 您在電子郵件驗證系統上有用於撰寫的應用程式。
* 而且您是應用程式擁有者。 

您可以取消視窗，以延遲遷移程式。 系統會定期要求您進行遷移，直到您遷移或通過遷移期限為止。 您可以從上方導覽列的鎖定圖示開始進行遷移程式。

## <a name="migration-for-the-app-owner"></a>應用程式擁有者的遷移

### <a name="before-you-migrate"></a>在您遷移之前

* **（選擇性**）藉由匯出每個應用程式或使用匯出[API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)，從 LUIS 入口網站的應用程式清單備份應用程式。
* **（選擇性**）儲存每個應用程式的 collaborator's 清單。 此電子郵件清單是在遷移過程中提供。
* **必要**，您必須擁有[Azure 訂](https://azure.microsoft.com/free/)用帳戶。 訂用帳戶程式的一部分需要帳單資訊。 不過，當您使用 LUIS 時，可以使用免費（`F0`）定價層。 

**撰寫 LUIS 應用程式是免費**的，由 @no__t 1 層表示。 深入瞭解[定價層](luis-boundaries.md#key-limits)。

如果您沒有 Azure 訂用帳戶，請[註冊](https://azure.microsoft.com/free/)。 

### <a name="migration-steps"></a>移轉步驟

請遵循[這些遷移步驟](luis-migration-authoring-steps.md)。

### <a name="after-you-migrate"></a>移轉後 

在遷移程式之後，您的所有 LUIS apps 現在都會指派給單一 LUIS 撰寫資源。

您可以從_LUIS 入口網站_中的 [**管理-> Azure 資源**] 頁面，建立更多撰寫資源並加以指派。 

您可以在該資源的 [**存取控制（IAM）** ] 頁面上，從_Azure 入口網站_將參與者新增至撰寫資源。 如需詳細資訊，請參閱[新增參與者存取](luis-migration-authoring-steps.md#after-the-migration-process-add-contributors-to-your-authoring-resource)。

|入口網站|用途|
|--|--|
|[Azure](https://azure.microsoft.com/free/)|* 建立預測和撰寫資源。<br>* 指派參與者。|
|[LUIS](https://www.luis.ai)|* 遷移至新的撰寫資源。<br>* 從 [**管理-> Azure 資源**] 頁面，指派或取消指派預測和撰寫資源給應用程式。| 

## <a name="migration-for-the-app-contributor"></a>應用程式參與者的遷移

LUIS 的每個使用者都需要遷移，包括共同作業者/參與者。 

### <a name="before-the-app-is-migrated"></a>遷移應用程式之前

您可以選擇匯出您是共同作業者的應用程式，然後將應用程式匯入回 LUIS。 匯入程式會使用您為擁有者的新應用程式識別碼來建立新的應用程式。

### <a name="after-the-app-is-migrated"></a>在應用程式遷移之後

應用程式擁有者必須[將您的電子郵件新增至 Azure 撰寫資源，做為](luis-how-to-collaborate.md#add-contributor-to-azure-authoring-resource)共同作業者。 

在遷移程式之後，您擁有的任何應用程式都可以在 LUIS 入口網站的 [**我的應用程式**] 頁面上取得。  

## <a name="troubleshooting"></a>疑難排解

LUIS 撰寫金鑰只有在完成遷移程式之後，才會顯示在 LUIS 入口網站中。 如果您建立撰寫金鑰（例如使用 LUIS CLI），使用者仍然需要完成遷移程式。 

## <a name="next-steps"></a>後續步驟

* [如何將您的應用程式遷移至撰寫資源](luis-migration-authoring-steps.md)