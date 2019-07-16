---
title: 在 Azure 中建立自訂動作和資源
description: 本教學課程將介紹如何在 Azure Resource Manager 中建立自訂動作和資源，以及如何將其整合至 Azure Resource Manager 範本、Azure CLI、Azure 原則和活動記錄的自訂工作流程。
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 4bbfcf070611e3df5c0fe47070f2ab6961111e07
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67799187"
---
# <a name="create-custom-actions-and-resources-in-azure"></a>在 Azure 中建立自訂動作和資源

自訂提供者可讓您在 Azure 上自訂工作流程。 自訂提供者是 Azure 和 `endpoint` 之間的合約。 其可將新的自訂 API 新增至 Azure Resource Manager，來啟用新的部署和管理功能。 本教學課程會詳細解說一個簡單範例，讓您了解如何將新的動作和資源新增至 Azure，以及如何進行整合。

本教學課程分成下列步驟：

- 設定 Azure 自訂提供者的 Azure Functions
- 撰寫自訂提供者的 RESTful 端點
- 建立和使用自訂提供者

## <a name="setup-azure-functions-for-azure-custom-providers"></a>設定 Azure 自訂提供者的 Azure Functions

本教學課程的這個部分會詳細說明如何設定 Azure 函式來使用自訂提供者。 自訂提供者可以使用任何公用 URL。

- [設定 Azure 自訂提供者的 Azure Functions](./tutorial-custom-providers-function-setup.md)

## <a name="authoring-a-restful-endpoint-for-custom-providers"></a>撰寫自訂提供者的 RESTful 端點

本教學課程的這個部分會詳細說明如何撰寫自訂提供者的 RESTful 端點。

- [撰寫自訂提供者的 RESTful 端點](./tutorial-custom-providers-function-authoring.md)

## <a name="creating-and-utilizing-the-custom-provider"></a>建立和使用自訂提供者

本教學課程的這個部分會詳細說明如何建立自訂提供者，並使用其自訂動作和資源。

- [建立和使用 Azure 自訂提供者](./tutorial-custom-providers-create.md)

## <a name="next-steps"></a>後續步驟

在本文中，我們已了解自訂提供者及其建置方式。 若要繼續進行教學課程的下一個步驟：

- [教學課程：設定 Azure 自訂提供者的 Azure Functions](./tutorial-custom-providers-function-setup.md)

如果您要尋找參考或快速入門，以下有一些實用連結：

- [快速入門：建立 Azure 自訂資源提供者和部署自訂資源](./create-custom-provider.md)
- [操作說明：將自訂動作新增至 Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [操作說明：將自訂資源新增至 Azure REST API](./custom-providers-resources-endpoint-how-to.md)
