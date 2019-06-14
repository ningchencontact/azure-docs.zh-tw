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
ms.openlocfilehash: 50d71a3967e61e5d531f4bfeae3582b56230a0d2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66117281"
---
## <a name="terminology"></a>術語

Azure 中的 Marketplace 映像具有下列屬性：

* **發行者**：建立映像的組織。 範例：Canonical、MicrosoftWindowsServer
* **供應項目**：相關映像群組 (由發行者所建立) 的名稱。 範例：UbuntuServer、 WindowsServer
* **SKU**：供應項目執行個體，例如發佈的主要版本。 範例：18.04-LTS、2019-Datacenter
* **版本**：映像 SKU 的版本號碼。 

當您以程式設計方式部署 VM 時，若要識別 Marketplace 映像，請以參數方式個別提供這些值。 有些工具會接受映像 *URN*，URN 會結合這些值並以冒號 (:) 字元分隔：*Publisher*:*Offer*:*Sku*:*Version*。 在 URN 中，您可以使用 "latest" 來取代版本號碼，這會選取最新的映像版本。 

如果映像發行者提供額外的授權和購買條款，則您必須接受這些條款並啟用以程式設計方式部署。 當您以程式設計方式部署 VM 時，也必須提供「購買方案」  參數。 請參閱[利用 Marketplace 條款部署映像](#deploy-an-image-with-marketplace-terms)。
