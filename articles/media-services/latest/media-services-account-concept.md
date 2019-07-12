---
title: 管理 Azure 媒體服務 v3 帳戶 |Microsoft Docs
description: 若要在 Azure 中開始管理、加密、編碼、分析和串流處理媒體內容，您需要建立媒體服務帳戶。 這篇文章說明如何管理 Azure 媒體服務 v3 帳戶。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/08/2019
ms.author: juliako
ms.openlocfilehash: fa9720c2c29af184016d2903e60520e701b4cf79
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67670676"
---
# <a name="manage-azure-media-services-v3-accounts"></a>管理 Azure 媒體服務 v3 帳戶

若要在 Azure 中開始管理、加密、編碼、分析和串流處理媒體內容，您需要建立媒體服務帳戶。 建立媒體服務帳戶時，您需要提供 Azure 儲存體帳戶資源的名稱。 指定的儲存體帳戶會附加到您的媒體服務帳戶。 媒體服務帳戶和所有相關聯的儲存體帳戶必須位於相同的 Azure 訂用帳戶中。 如需詳細資訊，請參閱 <<c0> [ 儲存體帳戶](storage-account-concept.md)。

## <a name="moving-a-media-services-account-between-subscriptions"></a>訂用帳戶之間移動媒體服務帳戶 

如果您要移動到新的訂用帳戶的媒體服務帳戶，您需要先移動整個資源群組，其中包含新的訂用帳戶的媒體服務帳戶。 您必須移動連結的所有資源：Azure 儲存體帳戶、 Azure CDN 設定檔等。如需詳細資訊，請參閱 [將資源移動到新的資源群組或訂用帳戶](../../azure-resource-manager/resource-group-move-resources.md)。 如同 Azure 中的任何資源，資源群組移動可能需要一些時間才能完成。

> [!NOTE]
> 媒體服務 v3 支援多租用戶模型。

### <a name="considerations"></a>考量

* 移轉至不同的訂用帳戶之前，您帳戶中建立備份的所有資料。
* 您要停止所有串流的端點和即時資料流的資源。 您的使用者將無法在資源群組移動期間存取您的內容。 

> [!IMPORTANT]
> 移動作業成功完成之前，請不要啟動串流端點。

### <a name="troubleshoot"></a>疑難排解 

如果媒體服務帳戶或相關聯的 Azure 儲存體帳戶會變成 「 中斷連線 」 下列資源群組移動，請嘗試輪替儲存體帳戶金鑰。 如果輪替儲存體帳戶金鑰並未解決媒體服務帳戶的 「 中斷連接 」 狀態，提出新的支援要求，從 [支援 + 疑難排解] 功能表中的媒體服務帳戶。  

## <a name="next-steps"></a>後續步驟

[建立帳戶](create-account-cli-quickstart.md)
