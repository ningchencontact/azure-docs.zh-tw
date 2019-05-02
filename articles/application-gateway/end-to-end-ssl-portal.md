---
title: 快速入门 - 使用 Azure 应用程序网关配置端到端 SSL 加密 - Azure 门户 | Microsoft Docs
description: 了解如何使用 Azure 门户创建启用端到端 SSL 加密的 Azure 应用程序网关。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/30/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: bd165f81b45e3ae0c121fb8876ed88e68d493195
ms.sourcegitcommit: ed66a704d8e2990df8aa160921b9b69d65c1d887
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2019
ms.locfileid: "64946788"
---
# <a name="configure-end-to-end-ssl-by-using-application-gateway-with-the-portal"></a>在门户中使用应用程序网关配置端到端 SSL

本文介绍如何在 Azure 门户中使用应用程序网关 v1 SKU 配置端到端 SSL 加密。  

> [!NOTE]
> 應用程式閘道 v2 SKU 需要啟用端對端設定的受信任的根憑證。 入口網站加入信任的根憑證的支援尚未提供。 因此，如果 v2 SKU 會看到[設定使用 PowerShell 的端對端 SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="before-you-begin"></a>開始之前

若要對應用程式閘道設定端對端 SSL，則需要閘道憑證和後端伺服器憑證。 閘道憑證用來根據 SSL 通訊協定規格衍生對稱金鑰。 然后，可以使用该对称密钥来加密和解密发送到网关的流量。 若要加密端對端 SSL，後端必須已加入到應用程式閘道的允許清單。 为此，请将后端服务器的公共证书（也称为身份验证证书）上传到应用程序网关。 新增憑證可確保應用程式閘道只會與已知的後端執行個體通訊。 這會進而保護端對端通訊。

有关详细信息，请参阅 [SSL 终止和端到端 SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview)。

## <a name="create-a-new-application-gateway-with-end-to-end-ssl"></a>创建启用端到端 SSL 的新应用程序网关

若要建立新的應用程式閘道端對端 SSL 加密，您必須先啟用 SSL 終止時建立新的應用程式閘道。 这会针对客户端与应用程序网关之间的通信启用 SSL 加密。 然後，您要以白名單後端 HTTP 設定中的伺服器憑證啟用 SSL 加密的應用程式閘道與後端伺服器之間，完成端對端 SSL 加密通訊。

### <a name="enable-ssl-termination-while-creating-a-new-application-gateway"></a>创建新应用程序网关时启用 SSL 终止

请参阅有关如何[在创建新应用程序网关时启用 SSL 终止](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)的文章。

### <a name="whitelist-certificates-for-backend-servers"></a>将后端服务器的证书加入白名单

1. 選取 [所有資源]，然後選取 [myAppGateway]。

2. 在左侧菜单中选择“HTTP 设置”。 当你创建应用程序网关时，Azure 自动创建了默认 HTTP 设置 **appGatewayBackendHttpSettings**。 

3. 选择“appGatewayBackendHttpSettings”。

4. 在“协议”下，选择“HTTPS”。 随后会显示“后端身份验证证书”窗格。 

5. 在“后端身份验证证书”下，选择“新建”。

6. 输入适当的**名称**。

7. 使用“上传 CER 证书”框上传证书。![addcert](./media/end-to-end-ssl-portal/addcert.png)

   > [!NOTE]
   > 在此步驟中所提供的憑證，應該是在後端上出現的 .pfx 憑證本身的公開金鑰。 將安裝在後端伺服器上的憑證 (非根憑證) 以「宣告、證據和推論」(CER) 格式匯出，並將它用於此步驟。 此步驟會將後端加入到應用程式閘道的允許清單。

8. 選取 [ **儲存**]。

## <a name="enable-end-to-end-ssl-for-existing-application-gateway"></a>为现有的应用程序网关启用端到端 SSL

若要將現有的應用程式閘道設定端對端 SSL 加密，您將需要接聽程式中的第一個啟用 SSL 終止。 这会针对客户端与应用程序网关之间的通信启用 SSL 加密。 然後，您要以白名單後端 HTTP 設定中的伺服器憑證啟用 SSL 加密的應用程式閘道與後端伺服器之間，完成端對端 SSL 加密通訊。

您必須使用 HTTPS 通訊協定與憑證使用接聽程式，啟用 SSL 終止。 您無法變更現有接聽程式通訊協定。 因此，您可以選擇使用現有的接聽程式使用 HTTPS 通訊協定和憑證，或建立新的接聽程式。 如果选择前一种做法，则可以忽略下面所述的“在现有应用程序网关中启用 SSL 终止”步骤，并直接转到“将后端服务器的证书加入白名单”部分。 如果您選擇後者，請使用下列步驟。

### <a name="enable-ssl-termination-in-existing-application-gateway"></a>在现有应用程序网关中启用 SSL 终止

1. 選取 [所有資源]，然後選取 [myAppGateway]。

2. 在左侧菜单中选择“侦听器”。

3. 根据要求选择“基本”侦听器或“多站点”侦听器。

4. 在“协议”下，选择“HTTPS”。 随后会显示“证书”窗格。

5. 上传 PFX 证书用于在客户端与应用程序网关之间实现 SSL 终止。

   > [!NOTE]
   > 对于测试，可以使用自签名的证书。 不应将自签名证书用于生产工作负荷。 了解如何[创建自签名证书](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal#create-a-self-signed-certificate)。

6. 根据要求添加“侦听器”的其他所需设置。

7. 選取 [確定] 以儲存。

### <a name="whitelist-certificates-for-backend-servers"></a>将后端服务器的证书加入白名单

1. 選取 [所有資源]，然後選取 [myAppGateway]。

2. 在左侧菜单中选择“HTTP 设置”。 可将现有后端 HTTP 设置中的证书加入白名单，或创建新的 HTTP 设置。 以下步骤将默认 HTTP 设置 **appGatewayBackendHttpSettings** 的证书加入白名单。

3. 选择“appGatewayBackendHttpSettings”。

4. 在“协议”下，选择“HTTPS”。 随后会显示“后端身份验证证书”窗格。 

5. 在“后端身份验证证书”下，选择“新建”。

6. 输入适当的**名称**。

7. 使用“上传 CER 证书”框上传证书。![addcert](./media/end-to-end-ssl-portal/addcert.png)

   > [!NOTE]
   > 在此步驟中所提供的憑證，應該是在後端上出現的 .pfx 憑證本身的公開金鑰。 將安裝在後端伺服器上的憑證 (非根憑證) 以「宣告、證據和推論」(CER) 格式匯出，並將它用於此步驟。 此步驟會將後端加入到應用程式閘道的允許清單。

8. 選取 [ **儲存**]。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用 Azure CLI 以應用程式閘道管理網路流量](./tutorial-manage-web-traffic-cli.md)
