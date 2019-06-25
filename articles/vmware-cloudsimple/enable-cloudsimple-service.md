---
title: CloudSimple 服務來啟用 Azure VMware 的解決方案
description: 描述如何在 Azure 訂用帳戶上啟用 CloudSimple，然後再登錄 CLoudSimple 資源提供者
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2553aa95d5028c510b4e1a1b7f51a9f410bcea51
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/17/2019
ms.locfileid: "67154867"
---
# <a name="register-the-microsoftvmwarecloudsimple-resource-provider-on-your-azure-subscription"></a>註冊 Microsoft.VMwareCloudSimple 資源提供者，您的 Azure 訂用帳戶

CloudSimple 服務可讓您取用 Azure CloudSimple VMware 方案。 您可以註冊 Microsoft.VMwareCloudSimple 服務作為資源提供者。

## <a name="register-the-resource-provider"></a>註冊資源提供者

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 選取 [所有服務]  。

3. 搜尋並選取**訂用帳戶**。

    ![選取訂用帳戶](media/cloudsimple-service-select-subscriptions.png)

4. 選取您想要啟用 CloudSimple 服務所在的訂用帳戶。

5. 按一下 **資源提供者**訂用帳戶。

6. 使用**Microsoft.VMwareCloudSimple**篩選資源提供者。

7. 選取資源提供者，然後按一下**註冊**。

    ![註冊資源提供者](media/cloudsimple-service-enable-resource-provider.png)

## <a name="next-steps"></a>後續步驟

* 了解如何[建立 CloudSimple 服務](create-cloudsimple-service.md)
* 了解如何[設定私用雲端環境](quickstart-create-private-cloud.md)
