---
title: 匯入或匯出資料與 Azure 應用程式設定 |Microsoft Docs
description: 了解如何匯入或匯出資料，或從 Azure 應用程式組態
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 846472e00bc048de906ee8e14f6de38e366f3571
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58225224"
---
# <a name="import-or-export-configuration-data"></a>匯入或匯出設定資料

Azure 應用程式組態支援的資料匯入和匯出作業。 使用這些作業可使用您的應用程式組態存放區和程式碼之間的大量和交換資料中的組態資料的專案。 例如，您可以設定一個應用程式進行測試的組態存放區，另一個用於生產環境。 您接著可以複製它們透過檔案之間的應用程式設定，讓您不需要輸入兩次的資料。

這篇文章提供匯入和匯出資料與應用程式設定的指南。

## <a name="import-data"></a>匯入資料

匯入會將為應用程式組態的資料存放區從現有的來源，而不是手動輸入它的組態。 使用匯入函式將資料移轉至的應用程式組態存放區或多個來源的彙總資料。 應用程式設定支援從 JSON、 YAML 或內容檔案匯入。

使用匯入資料[Azure 入口網站](https://aka.ms/azconfig/portal)或[Azure CLI](./scripts/cli-import.md)。 從 Azure 入口網站中，請遵循下列步驟：

1. 瀏覽至您的應用程式的組態存放區，然後選取**匯入/匯出**。

2. 在 **匯入**索引標籤上，選取**來源服務** > **組態檔**。

3. 選取 **語言** > **檔案類型**。

4. 選取 **資料夾**圖示，並瀏覽至要匯入檔案。

    ![匯入檔案](./media/import-file.png)

5. 選取 **分隔符號**，並視需要輸入**前置詞**来用於匯入的索引鍵名稱。

6. （選擇性） 選取**標籤**。

7. 選取 **套用**完成匯入。

    ![完成匯入檔案](./media/import-file-complete.png)

## <a name="export-data"></a>匯出資料

匯出會寫入到另一個目的地應用程式組態中儲存組態資料。 比方說，使用匯出函式中，將資料儲存至檔案與應用程式程式碼內嵌在部署期間的應用程式組態存放區中。

使用其中一種匯出資料[Azure 入口網站](https://aka.ms/azconfig/portal)或[Azure CLI](./scripts/cli-export.md)。 從 Azure 入口網站中，請遵循下列步驟：

1. 瀏覽至您的應用程式的組態存放區，然後選取**匯入/匯出**。

2. 在 **匯出**索引標籤上，選取**目標服務** > **組態檔**。

3. （選擇性） 輸入**前置詞**，然後選取**標籤**和在時間點要匯出的金鑰。

4. 選取 **檔案類型** > **分隔符號**。

5. 選取 **套用**完成匯出。

    ![已完成的匯出檔案](./media/export-file-complete.png)

## <a name="next-steps"></a>後續步驟

* [快速入門：建立 ASP.NET Web 應用程式](./quickstart-aspnet-core-app.md)  
