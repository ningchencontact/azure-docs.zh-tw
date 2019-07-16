---
title: 包含檔案
description: 包含檔案
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 06/17/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 29ab9b3c33aae6005510c34b207c7f87714149e5
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2019
ms.locfileid: "67608104"
---
### <a name="preview"></a>如何參與公開預覽？

您要上線才能參與公開預覽。 請使用[本文](../articles/bastion/bastion-create-host-portal.md)中的步驟來建立新的 Azure Bastion 資源。 目前，當您存取和使用這項服務時，您必須使用 [Azure 入口網站 - 預覽](https://aka.ms/BastionHost)，而不是一般 Azure 入口網站。

### <a name="regions"></a>預覽期間有哪些區域可以使用此服務？

您可以透過 [Azure 入口網站 - 預覽連結](https://aka.ms/BastionHost)，在下列任何預覽區域中部署和使用 Bastion 資源。

[!INCLUDE [region](bastion-regions-include.md)]

### <a name="portal"></a>我在 Azure 入口網站中找不到 Bastion 資源。 我該怎麼辦？

請務必使用 [Azure 入口網站 - 預覽連結](https://aka.ms/BastionHost)，而不是一般的 Azure 入口網站。

### <a name="publicip"></a>我的虛擬機器需要使用公用 IP 嗎？

您不需要在與 Azure Bastion 服務連線的 Azure 虛擬機器上使用 公用 IP。 Bastion 服務將會透過虛擬網路中的虛擬機器私人 IP，開啟 RDP/SSH 工作階段/連線至您的虛擬機器。

### <a name="rdpssh"></a>我需要 RDP 或 SSH 用戶端嗎？

您不需要 RDP 或 SSH 用戶端，即可在 Azure 入口網站中對您的 Azure 虛擬機器進行 RDP/SSH 存取。 請使用 [Azure 入口網站 - 預覽連結](https://aka.ms/BastionHost)來存取入口網站的預覽正式發行前小眾測試版。 這可讓您直接在瀏覽器中對虛擬機器進行 RDP/SSH 存取。

### <a name="agent"></a>我需要在 Azure 虛擬機器中執行代理程式嗎？

您不需要在瀏覽器或 Azure 虛擬機器上安裝代理程式或任何軟體。 Bastion 服務沒有代理程式，不需要任何其他 RDP/SSH 軟體。

### <a name="browsers"></a>支援哪些瀏覽器？

在公開預覽期間，若使用 Windows，請使用 Microsoft Edge 瀏覽器或 Google Chrome。 若為 Apple Mac，請使用 Google Chrome 瀏覽器。 Windows 和 Mac 也支援 Microsoft Edge Chromium。

### <a name="roles"></a>需要哪些角色權限才能存取虛擬機器？

若要建立連線，必須具備下列角色：

* 虛擬機器上的讀取者角色
* 虛擬機器的私人 IP 位址與 NIC 上的讀取者角色
* Azure Bastion 資源上的讀取者角色

### <a name="previewbill"></a>定價 - 加入此預覽版本需要付費嗎？

在公開預覽期間您只需支付部分費用。 不過，您的部署不會連結任何 SLA。 如需詳細資訊，請參閱[價格頁面](https://aka.ms/BastionHostPricing)。

### <a name="previewbill"></a>為何在 Bastion 工作階段開始之前，收到「您的工作階段已過期」錯誤訊息？

您應該只能從 Azure 入口網站起始工作階段。 登入 Azure 入口網站並再次開始您的工作階段。 如果您直接從另一個瀏覽器工作階段或索引標籤移至 URL，則預計會發生此錯誤。 這有助於確保您的工作階段更加安全，而且只能透過 Azure 入口網站存取工作階段。
