---
title: 管理 Azure 媒體服務 v2 帳戶 |Microsoft Docs
description: 若要在 Azure 中開始管理、加密、編碼、分析和串流處理媒體內容，您需要建立媒體服務帳戶。 這篇文章說明如何管理 Azure 媒體服務 v2 帳戶。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: b4c19b1f502d079d7dfcc1edef4674d21f78ac3a
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2019
ms.locfileid: "67622039"
---
# <a name="manage-azure-media-services-v2-accounts"></a>管理 Azure 媒體服務 v2 帳戶

若要在 Azure 中開始管理、加密、編碼、分析和串流處理媒體內容，您需要建立媒體服務帳戶。 建立媒體服務帳戶時，您需要提供 Azure 儲存體帳戶資源的名稱。 指定的儲存體帳戶會附加到您的媒體服務帳戶。 媒體服務帳戶和所有相關聯的儲存體帳戶必須位於相同的 Azure 訂用帳戶中。  

## <a name="moving-a-media-services-account-between-subscriptions"></a>訂用帳戶之間移動媒體服務帳戶 

如果您要移動到新的訂用帳戶的媒體服務帳戶，您需要先移動整個資源群組，其中包含新的訂用帳戶的媒體服務帳戶。 您必須移動連結的所有資源：Azure 儲存體帳戶、 Azure CDN 設定檔等。如需詳細資訊，請參閱 [將資源移動到新的資源群組或訂用帳戶](../../azure-resource-manager/resource-group-move-resources.md)。 如同 Azure 中的任何資源，資源群組移動可能需要一些時間才能完成。

媒體服務 v2 不支援多租用戶模型。 如果您需要將媒體服務帳戶移至新的租用戶中訂用帳戶，請在新的租用戶中建立新的 Azure Active Directory (Azure AD) 應用程式。 然後將您的帳戶移至新的租用戶中的訂用帳戶中。 租用戶移動完成之後，您可以開始使用新的租用戶 Azure AD 應用程式存取使用 v2 Api 的媒體服務帳戶。 

> [!IMPORTANT]
> 您需要重設[Azure AD 驗證](media-services-portal-get-started-with-aad.md)存取媒體服務 v2 API 的資訊。  
### <a name="considerations"></a>考量

* 移轉至不同的訂用帳戶之前，您帳戶中建立備份的所有資料。
* 您要停止所有串流的端點和即時資料流的資源。 您的使用者將無法在資源群組移動期間存取您的內容。 

> [!IMPORTANT]
> 移動作業成功完成之前，請不要啟動串流端點。

### <a name="troubleshoot"></a>疑難排解 

如果媒體服務帳戶或相關聯的 Azure 儲存體帳戶會變成 「 中斷連線 」 下列資源群組移動，請嘗試輪替儲存體帳戶金鑰。 如果輪替儲存體帳戶金鑰並未解決媒體服務帳戶的 「 中斷連接 」 狀態，提出新的支援要求，從 [支援 + 疑難排解] 功能表中的媒體服務帳戶。  
 
## <a name="next-steps"></a>後續步驟

[建立帳戶](media-services-portal-create-account.md)
