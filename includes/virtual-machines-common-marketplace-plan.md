---
title: 包含檔案
description: 包含檔案
services: virtual-machines-windows, virtual-machines-linux
author: rockboyfor
ms.service: multiple
ms.topic: include
origin.date: 10/09/2018
ms.date: 11/26/2018
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: 4d2235eaea457c89d01a632afa5dd5a862bec344
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60880536"
---
## <a name="deploy-an-image-with-marketplace-terms"></a>利用 Marketplace 條款部署映像

Azure Marketplace 中的某些 VM 映像有額外的授權和購買條款，您必須先接受這些條款，才能以程式設計方式部署這些映像。  

若要從這類映像部署 VM，您將必須接受映像的條款，並啟用以程式設計方式部署。 針對每一訂用帳戶，您將只需執行一次此動作。 之後，每次您從映像以程式設計方式部署 VM 時，也將必須指定「購買方案」參數。

下列各節將示範如何：

* 查明 Marketplace 映像是否有額外的授權條款 
* 以程式設計方式接受條款
* 當您以程式設計方式部署 VM 時，提供購買方案參數

<!-- Update_Description: update meta properties, wording update -->