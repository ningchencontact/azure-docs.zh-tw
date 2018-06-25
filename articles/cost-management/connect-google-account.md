---
title: 將 Google Cloud Platform 帳戶連線到 Azure 成本管理 | Microsoft Docs
description: 連線 Google Cloud Platform 帳戶以在「成本管理」報表中檢視成本和使用方式資料。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 06/07/2018
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: d4b906bd966da66659d23b935f7dbbd44b33899a
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/11/2018
ms.locfileid: "35296436"
---
# <a name="connect-a-google-cloud-platform-account"></a>連線 Google Cloud Platform 帳戶

您可以將現有的 Google Cloud Platform 帳戶連線到 Azure 成本管理。 將您的帳戶連線到成本管理之後，成本和使用方式資料可以在「成本管理」報表中取得。 本文可協助您設定及連接 Google 帳戶與成本管理。

## <a name="collect-project-information"></a>收集專案資訊

首先請收集有關您專案的資訊。

1. 登入 Google Cloud Platform 主控台：[https://console.cloud.google.com](https://console.cloud.google.com)。
2. 檢閱要上架到成本管理的專案資訊，注意「專案名稱」和「專案識別碼」。 請就近保存此資訊以使用於後續步驟。  
    ![Google Cloud Platform 主控台](./media/connect-google-account/gcp-console01.png)
3. 如果未啟用計費並連結至您的專案，請建立計費帳戶。 如需詳細資訊，請參閱[建立新的計費帳戶](https://cloud.google.com/billing/docs/how-to/manage-billing-account#create\_a\_new\_billing\_account) \(英文\)。

## <a name="enable-storage-bucket-billing-export"></a>啟用儲存體貯體計費匯出

成本管理會從儲存體貯體擷取您的 Google 計費資料。 請就近保存「貯體名稱」和「報告前置詞」資訊，以便後續使用於成本管理註冊。

使用 Google Cloud Storage 儲存使用量報告需支付最低費用。 如需詳細資訊，請參閱[雲端儲存體定價](https://cloud.google.com/storage/pricing)。

1. 如果您未啟用計費匯出至檔案，請遵循[如何啟用計費匯出至檔案](https://cloud.google.com/billing/docs/how-to/export-data-file#how_to_enable_billing_export_to_a_file)的指示。 您可以使用 JSON 或 CSV 計費匯出格式。
2. 否則，在 Google Cloud Platform 主控台中，巡覽至 [計費] > [計費匯出]。 注意您的計費「貯體名稱」和「報告前置詞」。  
    ![計費匯出](./media/connect-google-account/billing-export.png)

## <a name="enable-google-cloud-platform-apis"></a>啟用 Google Cloud Platform API

若要收集使用方式和資產資訊，成本管理需要啟用下列 Google Cloud Platform API：

- BigQuery API
- Google Cloud SQL
- Google Cloud Datastore API
- Google Cloud Storage
- Google Cloud Storage JSON API
- Google Compute Engine API

### <a name="enable-or-verify-apis"></a>啟用或確認 API

1. 在 Google Cloud Platform 主控台中，選取您想要向成本管理註冊的專案。
2. 巡覽至 [API和服務] > [程式庫]。
3. 使用搜尋來尋找每個先前列出的 API。
4. 針對每個 API，確認已顯示 [已啟用 API]。 否則，請按一下 [啟用]。

## <a name="add-a-google-cloud-account-to-cost-management"></a>將 Google Cloud 帳戶新增到成本管理

1. 從 Azure 入口網站中開啟 Cloudyn 入口網站，或瀏覽至 [https://azure.cloudyn.com](https://azure.cloudyn.com/) 並登入。
2. 按一下 [設定] (齒輪符號)，然後選取 [雲端帳戶]。
3. 在 [帳戶管理] 中，選取 [Google 帳戶] 索引標籤，然後按一下 [新增 +]。
4. 在 [Google 帳戶名稱] 中輸入計費帳戶的電子郵件地址，然後按一下 [下一步]。
5. 在 Google 驗證對話方塊中，選取或輸入 Google 帳戶，然後「允許」cloudyn.com 存取您的帳戶。
6. 加入您先前記下的要求專案資訊。 它們包含「專案識別碼」「專案名稱」、「計費」貯體名稱和「計費檔案」報告前置詞，然後按一下 [儲存]。  
    ![加入 Google 專案](./media/connect-google-account/add-project.png)

您的 Google 帳戶會出現在帳戶清單，而且應該是「已驗證」。 您的 Google 專案名稱和識別碼應該會顯示在其下方，並有一個綠色核取記號。 帳戶狀態應該是 [已完成]。

幾個小時內，成本管理報告會顯示 Google 成本和使用方式資訊。

## <a name="next-steps"></a>後續步驟

- 若要深入了解 Azure 成本管理，請繼續進行「成本管理」的[檢閱使用量和成本](./tutorial-review-usage.md)教學課程。
