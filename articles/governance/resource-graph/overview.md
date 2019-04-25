---
title: Azure Resource Graph 概觀
description: 了解 Azure Resource Graph 服務如何能夠大規模進行複雜的資源查詢。
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/30/2019
ms.topic: overview
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: d76a5b32403bd14f18181580f891925130808922
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2019
ms.locfileid: "60002879"
---
# <a name="overview-of-the-azure-resource-graph-service"></a>Azure Resource Graph 服務的概觀

Azure Resource Graph 是 Azure 中的一項服務，透過大規模查詢所有訂用帳戶及管理群組的能力，提供兼具效率及效能的資源探索延伸 Azure 資源管理，讓您可以有效的治理環境。 這些查詢提供下列功能：

- 根據資源屬性，使用複雜篩選、群組和排序查詢資源的能力。
- 以治理需求為基礎反覆探索資源，以及將結果運算式轉換成原則定義的能力。
- 評估在廣範圍雲端環境中套用原則所帶來影響的能力。
- [詳細說明對資源屬性所做變更](./how-to/get-resource-changes.md)的能力 (預覽)。

在本文件中，您將詳盡地逐一了解每項功能。

> [!NOTE]
> Azure Resource Graph 已用於 Azure 入口網站中瀏覽「所有資源」的新體驗，以及用於 Azure 原則的[變更記錄](../policy/how-to/determine-non-compliance.md#change-history-preview)。
> 視覺化差異。其目的是協助客戶管理大規模的環境。

## <a name="how-does-resource-graph-complement-azure-resource-manager"></a>Resource Graph 輔助 Resource Manager 的方式

Azure Resource Manager 目前會將資料傳送到啟用數個資源欄位 (即資源名稱、識別碼、類型、資源群組、訂用帳戶及位置) 的有限資源快取。 在過去，若要使用多種不同的資源屬性，您必須呼叫每個個別資源提供者，並要求每個資源的屬性詳細資料。

使用 Azure Resource Graph，您可以存取這些由資源提供者傳回的屬性，而無須對每個資源提供者進行個別呼叫。 如需支援的資源類型清單，請在[完整模式部署資源](../../azure-resource-manager/complete-mode-deletion.md)資料表中尋找 [是] 的部分。

透過 Azure Resource Graph，您可以：

- 存取由資源提供者傳回的屬性，而無須對每個資源提供者進行個別呼叫。
- 檢視過去 14 天中資源上的變更記錄，以查看變更的屬性和變更的時間。 (預覽)

## <a name="the-query-language"></a>查詢語言

現在您已更加了解 Azure Resource Graph，讓我們繼續深入了解如何建構查詢。

請務必了解，Azure Resource Graph 的查詢語言是以 Azure 資料總管所使用的 [Kusto 查詢語言](../../data-explorer/data-explorer-overview.md)為基礎的。

首先，如需可搭配 Azure Resource Graph 使用的作業及函式詳細資料，請參閱 [Resource Graph 查詢語言](./concepts/query-language.md)。
若要瀏覽資源，請參閱[探索資源](./concepts/explore-resources.md)。

## <a name="permissions-in-azure-resource-graph"></a>Azure Resource Graph 中的權限

若要使用 Resource Graph，您必須先透過[角色型存取控制](../../role-based-access-control/overview.md) (RBAC) 獲得適當授權，至少取得您欲查詢資源的讀取權限。 如果連 Azure 物件或物件群組的 `read` 權限都不具備，則不會傳回結果。

> [!NOTE]
> Resource Graph 會使用主體在登入期間可使用的訂用帳戶。 若要查看作用中工作階段期間加入的新訂用帳戶資源，主體必須重新整理內容。 若登出後再登入，則此動作會自動執行。

## <a name="throttling"></a>節流

對 Resource Graph 的查詢會進行節流控制，以便為所有客戶提供最佳的體驗和回應時間。 如果您的組織想要使用 Resource Graph API 來執行頻繁的大規模查詢，請從 Resource Graph 頁面使用 [意見反應] 入口網站。 請務必提供您的商務案例，並選取 [Microsoft 可以就您提供的意見傳送電子郵件給您] 核取方塊，以便支援小組可以與您連絡。

## <a name="running-your-first-query"></a>執行您的第一個查詢

Resource Graph 支援 Azure CLI、Azure PowerShell 和 Azure SDK for .NET。 這兩種語言的查詢結構相同。 了解如何在 [Azure CLI](first-query-azurecli.md#add-the-resource-graph-extension) 和 [Azure PowerShell](first-query-powershell.md#add-the-resource-graph-module) 中啟用 Resource Graph。

## <a name="next-steps"></a>後續步驟

- 使用 [Azure CLI](first-query-azurecli.md) 執行第一個查詢。
- 使用 [Azure PowerShell](first-query-powershell.md) 執行第一個查詢。
- 從[入門查詢](./samples/starter.md)開始。
- 使用[進階查詢](./samples/advanced.md)增強您的了解。