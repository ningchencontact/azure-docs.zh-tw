---
title: Azure Resource Graph 概觀
description: 了解 Azure Resource Graph 服務如何能夠大規模進行複雜的資源查詢。
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/06/2019
ms.topic: overview
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: d78c640f4269c799d3d371e6dd9db477faf96694
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807436"
---
# <a name="overview-of-the-azure-resource-graph-service"></a>Azure Resource Graph 服務的概觀

Azure Resource Graph 是 Azure 中的一項服務，透過大規模查詢指定訂用帳戶群組的能力，提供兼具效率及效能的資源探索延伸 Azure 資源管理，讓您可以有效的治理環境。 這些查詢提供下列功能：

- 根據資源屬性，使用複雜篩選、群組和排序查詢資源的能力。
- 根據治理需求反覆探索資源的能力。
- 評估在廣範圍雲端環境中套用原則所帶來影響的能力。
- [詳細說明對資源屬性所做變更](./how-to/get-resource-changes.md)的能力 (預覽)。

在本文件中，您將詳盡地逐一了解每項功能。

> [!NOTE]
> Azure Resource Graph 負責提供 Azure 入口網站的搜尋列、「所有資源」的全新瀏覽體驗以及用於 Azure 原則的[變更記錄](../policy/how-to/determine-non-compliance.md#change-history-preview)
>   「視覺效果差異」。其目的是協助客戶管理大規模的環境。

[!INCLUDE [service-provider-management-toolkit](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="how-does-resource-graph-complement-azure-resource-manager"></a>Resource Graph 輔助 Resource Manager 的方式

Azure Resource Manager 目前支援查詢基本資源欄位 (尤其是資源名稱、識別碼、類型、資源群組、訂用帳戶及位置)。 Resource Manager 也提供呼叫個別資源提供者的功能，可一次取得一個資源的詳細屬性。

使用 Azure Resource Graph，您可以存取這些由資源提供者傳回的屬性，而無須對每個資源提供者進行個別呼叫。 如需支援的資源類型清單，請在[完整模式部署資源](../../azure-resource-manager/complete-mode-deletion.md)資料表中尋找 [是]  的部分。

透過 Azure Resource Graph，您可以：

- 存取由資源提供者傳回的屬性，而無須對每個資源提供者進行個別呼叫。
- 檢視過去 14 天中資源上的變更記錄，以查看變更的屬性和變更的時間。 (預覽)

## <a name="how-resource-graph-is-kept-current"></a>Resource Graph 保持最新狀態的方式

更新 Azure 資源時，Resource Manager 會向 Resource Graph 通知變更。
Resource Graph 接著會更新其資料庫。 Resource Graph 也會執行標準的「完整掃描」  。 此掃描可確保 Resource Graph 能在使用者錯過通知，或是有資源在 Resource Manager 範圍外被更新的情況下保持在最新狀態。

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

對 Resource Graph 的查詢是免費的服務，可進行節流控制，以便為所有客戶提供最佳的體驗和回應時間。 如果您的組織想要使用 Resource Graph API 來執行頻繁的大規模查詢，請從 [Resource Graph 入口網站頁面](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/ResourceGraph)使用 [意見反應] 入口網站。
請提供您的商務案例，並選取 [Microsoft 會就您提供的意見傳送電子郵件給您] 核取方塊，以便支援小組可以與您連絡。

Resource Graph 會在使用者層級進行查詢的節流處理。 服務回應會包含下列 HTTP 標頭：

- `x-ms-user-quota-remaining` (int)：使用者的剩餘資源配額。 此值會對應至查詢計數。
- `x-ms-user-quota-resets-after` (hh:mm:ss)：使用者配額耗用量重設之前的剩餘時間長度

如需詳細資訊，請參閱[節流要求指引](./concepts/guidance-for-throttled-requests.md)。

## <a name="running-your-first-query"></a>執行您的第一個查詢

Resource Graph 支援 Azure CLI、Azure PowerShell 和 Azure SDK for .NET。 這兩種語言的查詢結構相同。 了解如何在 [Azure CLI](first-query-azurecli.md#add-the-resource-graph-extension) 和 [Azure PowerShell](first-query-powershell.md#add-the-resource-graph-module) 中啟用 Resource Graph。

## <a name="next-steps"></a>後續步驟

- 使用 [Azure CLI](first-query-azurecli.md) 執行第一個查詢。
- 使用 [Azure PowerShell](first-query-powershell.md) 執行第一個查詢。
- 從[入門查詢](./samples/starter.md)開始。
- 使用[進階查詢](./samples/advanced.md)增強您的了解。