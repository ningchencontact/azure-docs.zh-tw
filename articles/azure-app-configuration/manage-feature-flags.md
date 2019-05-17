---
title: 使用 Azure 應用程式組態來管理功能旗標的教學課程 | Microsoft Docs
description: 在本教學課程中，您將了解如何使用 Azure 應用程式組態來分開管理功能旗標與應用程式
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/19/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: d995a2e9f0d05dc3b0853036e8fb3c04ccdfab96
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2019
ms.locfileid: "65412287"
---
# <a name="tutorial-manage-feature-flags-in-azure-app-configuration"></a>教學課程：在 Azure 應用程式組態中管理功能旗標

您可以將所有功能旗標儲存在 Azure 應用程式組態中，並從單一位置進行管理。 其擁有稱為**功能管理員**的入口網站 UI，專供功能旗標使用。 此外，應用程式組態還原生支援 .NET Core 功能旗標的資料結構描述。

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 在應用程式組態中定義和管理功能旗標。
> * 從應用程式存取功能旗標。

## <a name="create-feature-flags"></a>建立功能旗標

應用程式組態在 Azure 入口網站中的**功能管理員**會提供 UI 來建立和管理功能旗標，以供您在應用程式中使用。 請遵循下列步驟來新增功能旗標。

1. 選取 [功能管理員] > [+ 建立] 來新增功能旗標。

    ![功能旗標清單](./media/azure-app-configuration-feature-flags.png)

2. 輸入功能旗標的唯一索引鍵名稱。 您需要此名稱才能在程式碼中參考旗標。

3. 選擇是否要為功能旗標提供更容易理解的描述。

4. 設定功能旗標的初始狀態。 其通常只會是「開啟」或「關閉」。

    ![功能旗標建立](./media/azure-app-configuration-feature-flag-create.png)

5. 當狀態為「開啟」時，請選擇是否要指定任何其他條件，以使用 [新增篩選條件] 來加以限定。 輸入內建或自訂的篩選鍵，並關聯參數。 內建篩選條件包括：

    | Key | JSON 參數 |
    |---|---|
    | Microsoft.Percentage | {"Value":0-100 percent} |
    | Microsoft.TimeWindow | {"Start":UTC time, "End":UTC time} |

    ![功能旗標篩選條件](./media/azure-app-configuration-feature-flag-filter.png)

## <a name="update-feature-flag-states"></a>更新功能旗標狀態

請遵循下列步驟，以變更功能旗標的狀態值。

1. 選取 [功能管理員]。

2. 在所要修改的功能旗標右邊，按一下 [...] > [編輯]。

3. 為功能旗標設定新狀態。

## <a name="access-feature-flags"></a>存取功能旗標

**功能管理員**所建立的功能旗標會以一般的索引鍵值形式來儲存與擷取。 其會保存在特殊的命名空間前置詞 .appconfig.featureflag 底下。 您可以使用**組態總管**來檢視基礎的索引鍵值。 應用程式可以使用應用程式組態的組態提供者、SDK、命令列延伸模組和 REST API 來擷取這些索引鍵值。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何使用應用程式組態來管理功能旗標和其狀態。 如需應用程式組態和 ASP.NET Core 中的功能管理支援詳細資訊，請參閱下列資源。

* [在 ASP.NET Core 應用程式中使用功能旗標](./use-feature-flags-dotnet-core.md)
