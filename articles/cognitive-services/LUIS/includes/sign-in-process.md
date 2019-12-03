---
title: 包含檔案
description: 包含檔案
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: 5e7feac35ddd607d36cb0ddd3bae10cf1b9bc7a7
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278585"
---
## <a name="sign-in-to-luis-portal"></a>登入 LUIS 入口網站

LUIS 的新使用者必須遵循此程序：

1. 登入 [LUIS 入口網站 (預覽)](https://preview.luis.ai)，選取您的國家/地區並同意使用規定。 如果您看到的是**我的應用程式**，表示已有 LUIS 資源存在，您應直接跳到建立應用程式的步驟。

1. 選取 [建立 Azure 資源]  ，然後選取 [建立要作為應用程式遷移目的地的撰寫資源]  。

    ![選擇 Language Understanding 撰寫資源的類型](../media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. 填入資源的詳細資料。

    ![建立撰寫資源](../media/migrate-authoring-key/choose-authoring-resource-form.png)

    在**建立新的撰寫資源**時，請提供下列資訊： 

    * **資源名稱** - 您選擇的自訂名稱，用來作為撰寫和預測端點查詢 URL 的一部分。
    * **租用戶** - Azure 訂用帳戶所關聯的租用戶。 
    * **訂用帳戶名稱** - 要作為資源收費對象的訂用帳戶。
    * **資源群組** - 您選擇或建立的自訂資源群組名稱。 資源群組可讓您將 Azure 資源分組以方便存取和管理。 
    * **位置** - 位置選擇是以**資源群組**選取項目作為依據。
    * **定價層** - 定價層會決定每秒和每月的交易上限。

1. 隨即會顯示所要建立資源的摘要。 選取 [下一步]  。

    ![建立撰寫資源](../media/sign-in/sign-in-confirm-key-selection.png)

1. 請選取 [繼續]  來表示確認。 

    ![建立撰寫資源](../media/sign-in/sign-in-confirm-continue.png)