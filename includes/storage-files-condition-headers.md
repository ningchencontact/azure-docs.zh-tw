---
title: 包含檔案
description: 包含檔案
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/17/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0486b595bffd18b06d54e8377b24deab04e2aa93
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67174047"
---
## <a name="error-conditionheadersnotsupported-from-a-web-application-using-azure-files-from-browser"></a>使用 Azure 檔案從瀏覽器的 Web 應用程式的錯誤 ConditionHeadersNotSupported

當存取應用程式，可透過裝載 Azure 檔案服務中的內容使用條件式標頭，例如 web 瀏覽器，存取會失敗，顯示 ConditionHeadersNotSupported 時發生錯誤。

![ConditionHeaderNotSupported 錯誤](media/storage-files-condition-headers/conditionalerror.png)

### <a name="cause"></a>原因

尚不支援條件式標頭。 實作它們的應用程式需要每次存取檔案時，要求完整的檔案。

### <a name="workaround"></a>因應措施

上傳新的檔案時，快取控制屬性預設是 [無快取]。 若要強制應用程式要求檔案每次檔案的快取控制屬性必須從 [無快取] 更新為 「 無快取、 hodnotě no-store，必須 must-revalidate"。 這可以使用來達成[Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)。

![儲存體總管 內容的快取的修改](media/storage-files-condition-headers/storage-explorer-cache.png)