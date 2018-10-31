---
title: 包含檔案
description: 包含檔案
services: virtual-machines-windows, virtual-machines-linux
author: dlepow
ms.service: multiple
ms.topic: include
ms.date: 10/09/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 6c7a9857f6d6d57dc9e314bcb1ef848a326b7ed2
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2018
ms.locfileid: "49437080"
---
## <a name="terminology"></a>術語

Azure 中的 Marketplace 映像具有下列屬性：

* **發行者**：建立映像的組織。 範例：Canonical、MicrosoftWindowsServer
* **供應項目**：發行者所建立之一組相關映像的名稱。 範例：Ubuntu Server、WindowsServer
* **SKU**：供應項目執行個體，例如主要發佈版本。 範例：16.04-LTS、2016-Datacenter
* **版本**：映像 SKU 的版本號碼。 

當您以程式設計方式部署 VM 時，若要識別 Marketplace 映像，請以參數方式個別提供這些值。 有些工具會接受映像 *URN*，URN 會結合這些值並以冒號 (:) 字元分隔：發行者:供應項目:SKU:版本。 在 URN 中，您可以使用 "latest" 來取代版本號碼，這會選取最新的映像版本。 

如果映像發行者提供額外的授權和購買條款，則您必須接受這些條款並啟用以程式設計方式部署。 當您以程式設計方式部署 VM 時，也必須提供「購買方案」參數。 請參閱[利用 Marketplace 條款部署映像](#deploy-an-image-with-marketplace-terms)。