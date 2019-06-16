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
ms.openlocfilehash: cacd5510147ce997efec922f4b4656956a098d88
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66676934"
---
# <a name="register-the-microsoftvmwarecloudsimple-resource-provider-on-your-azure-subscription"></a>註冊 Microsoft.VMwareCloudSimple 資源提供者，您的 Azure 訂用帳戶

CloudSimple 服務可讓您取用 Azure CloudSimple VMware 方案。 若要使用 CloudSimple 服務，它必須先啟用您的 Azure 訂用帳戶。 然後，您就可以註冊 Microsoft.VMwareCloudSimple 服務作為資源提供者。

## <a name="enable-the-cloudsimple-service"></a>啟用 CloudSimple 服務

啟用 CloudSimple 服務，您的 Azure 訂用帳戶，請開啟支援要求[Microsoft 支援服務](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。 當您提交要求，請選取下列選項。

* 問題類型：**技術**
* 訂用帳戶：**您的訂用帳戶識別碼**
* 服務類型：**VMware CloudSimple 方案**
* 問題類型：**專用的節點配額**
* 問題的子類型：**增加配額的專用節點**
* 主旨：**啟用 CloudSimple 服務**

您也可以連絡 Microsoft 客戶代表在[ azurevmwaresales@microsoft.com ](mailto:azurevmwaresales@microsoft.com)。 提供您電子郵件中的 Azure 訂用帳戶識別碼。  

CloudSimple 服務啟用訂用帳戶之後，您可以啟用訂用帳戶的資源提供者。

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