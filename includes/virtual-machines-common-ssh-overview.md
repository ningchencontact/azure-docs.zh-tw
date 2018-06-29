---
title: 包含檔案
description: 包含檔案
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 04/16/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 5e199771c39ba2fbdeabbd04ed4081a9cd3ea117
ms.sourcegitcommit: 0408c7d1b6dd7ffd376a2241936167cc95cfe10f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/26/2018
ms.locfileid: "36964513"
---
## <a name="overview-of-ssh-and-keys"></a>SSH 和金鑰的概觀

SSH 是允許透過不安全連線進行安全登入的已加密連線通訊協定。 它是 Azure 中裝載之 Linux VM 的預設連線通訊協定。 雖然 SSH 本身提供加密的連線，但使用密碼搭配 SSH 連線仍會讓 VM 容易遭受暴力密碼破解攻擊或密碼猜測。 使用公開/私密金鑰組 (也就是 SSH 金鑰) 連線至 VM 是更安全且慣用的方法。 

* *公開金鑰*會置於您的 Linux VM 或任何您要搭配公開金鑰加密使用的其他服務上。

* 當您建立 SSH 連線時，要對 Linux VM 呈現「私密金鑰」以驗證您的身分識別。 保護此私密金鑰。 不要共用它。

根據組織的安全性原則，您可以重複使用單一公用/私密金鑰組來存取多個 Azure VM 和服務。 您想要存取的每個 VM 或服務都不需要各有一對金鑰。 

公開金鑰可以與任何人共用；但只有您 (或您的本機安全性基礎結構) 會擁有私密金鑰。