---
title: 管理 Azure Logic Apps 中的整合服務環境
description: 檢查網路健康狀態, 並管理整合服務環境 (ISE) 中的邏輯應用程式、連線、自訂連接器和整合帳戶, 以進行 Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/26/2019
ms.openlocfilehash: 8f10e3d3fd7c67d1e803e8f85c9918c91bb81d59
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68517458"
---
# <a name="manage-your-integration-service-environment-ise-in-azure-logic-apps"></a>在 Azure Logic Apps 中管理您的整合服務環境 (ISE)

若要檢查您的[整合服務環境 (ise)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)的網路健全狀況, 並管理存在於 ISE 中的邏輯應用程式、連線、整合帳戶和自訂連接器, 請遵循本主題中的步驟。

## <a name="view-your-ise"></a>查看您的 ISE

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 在入口網站的搜尋方塊中, 輸入「整合服務環境」, 然後選取 [**整合服務環境**]。

   ![尋找整合服務環境](./media/ise-manage-integration-service-environment/find-integration-service-environment.png)

1. 從 [結果] 清單中, 選取您的整合服務環境。

   ![選取整合服務環境](./media/ise-manage-integration-service-environment/select-integration-service-environment.png)

1. 繼續閱讀下一節, 以尋找 ISE 中的邏輯應用程式、連線、連接器或整合帳戶。

<a name="check-network-health"></a>

## <a name="check-network-health"></a>檢查網路健全狀況

在您的 ISE 功能表的 [**設定**] 底下, 選取 [**網路健康**情況]。 此窗格會顯示子網的健全狀況狀態, 以及其他服務的輸出相依性。

![檢查網路健全狀況](./media/ise-manage-integration-service-environment/ise-check-network-health.png)

<a name="find-logic-apps"></a>

## <a name="manage-your-logic-apps"></a>管理您的邏輯應用程式

1. 在您的 ISE 功能表的 [**設定**] 底下, 選取 [**邏輯應用程式**]。

   ![尋找邏輯應用程式](./media/ise-manage-integration-service-environment/ise-find-logic-apps.png)

1. 若要在不再需要時從 ISE 移除邏輯應用程式, 請選取那些邏輯應用程式, 然後選取 [**刪除**]。

<a name="find-api-connections"></a>

## <a name="manage-api-connections"></a>管理 API 連線

1. 若要查看在 ISE 中執行的邏輯應用程式所建立的 API 連線, 請在您的 ISE 功能表的 [**設定**] 底下, 選取 [ **API**連線]。

   ![尋找 API 連線](./media/ise-manage-integration-service-environment/ise-find-api-connections.png)

1. 若要在不再需要您的 ISE 連接時將其移除, 請選取這些連線, 然後選取 [**刪除**]。

<a name="find-custom-connectors"></a>

## <a name="manage-custom-connectors"></a>管理自訂連接器

1. 若要查看在 ISE 中建立的自訂連接器, 請在 ISE 功能表的 [**設定**] 底下, 選取 [**自訂連接器**]。

   ![尋找自訂連接器](./media/ise-manage-integration-service-environment/ise-find-custom-connectors.png)

1. 若不再需要您的 ISE 中的自訂連接器, 請選取這些連接器, 然後選取 [**刪除**]。

<a name="find-integration-accounts"></a>

## <a name="manage-integration-accounts"></a>管理整合帳戶

1. 在您的 ISE 功能表的 [**設定**] 下, 選取 [**整合帳戶**]。

   ![尋找整合帳戶](./media/ise-manage-integration-service-environment/ise-find-integration-accounts.png)

1. 若要在不再需要的情況下從 ISE 中移除整合帳戶, 請選取那些整合帳戶, 然後選取 [**刪除**]。

## <a name="next-steps"></a>後續步驟

* 了解如何[從隔離式 Logic Apps 連線到 Azure 虛擬網路](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
