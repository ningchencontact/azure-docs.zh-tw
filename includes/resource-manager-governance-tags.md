---
title: 包含檔案
description: 包含檔案
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 07/11/2019
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: a9a837d8a486e10ea2a62be69b889e657b1dbc05
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2019
ms.locfileid: "72600875"
---
您可將標記套用至提供中繼資料的 Azure 資源，以邏輯方式依分類組織這些資源。 每個標記都是由一個名稱和一個值配對組成。 例如，您可以將「環境」名稱和「生產」值套用至生產環境中的所有資源。

套用標記之後，您可以擷取訂用帳戶中具有該標記名稱和值的所有資源。 標記可讓您擷取不同資源群組中的相關資源。 當您需要組織資源以進行計費或管理時，此方法很有用。

除了自動標記策略以外，您的分類法也應考量自助式中繼資料標記策略，以減少使用者的負擔並提高精確度。

標籤具有下列限制：

* 並非所有資源類型都支援標記。 若要判斷您是否可以將標記套用至資源類型，請參閱 [Azure 資源的標記支援](../articles/azure-resource-manager/tag-support.md)。
* 每個資源或資源群組最多可以有50個標記名稱/值配對。 目前，儲存體帳戶僅支援15個標記，但在未來的版本中，該限制將會提高至50。 如果您需要套用的標記超過允許的最大數目，請使用標記值的 JSON 字串。 JSON 字串可以包含許多套用至單一標記名稱的值。 資源群組可以包含多個具有50標記名稱/值組的資源。
* 標記名稱上限為 512 個字元，且標記值上限為 256 字元。 儲存體帳戶的標記名稱上限為 128 個字元，且標記值上限為 256 個字元。
* 一般化 Vm 不支援標記。
* 資源群組中的資源不會繼承套用至該資源群組的標籤。
* 標記無法套用到類似雲服務的傳統資源。
* 標記名稱不得包含這些字元：`<`、`>`、`%`、`&`、`\`、`?`、`/`

   > [!NOTE]
   > 目前 Azure DNS 區域和流量管理員服務也不允許在標記中使用空格。 
