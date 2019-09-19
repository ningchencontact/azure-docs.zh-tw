---
title: 快速入門-使用 Azure 應用程式閘道設定端對端 SSL 加密-Azure 入口網站 |Microsoft Docs
description: 瞭解如何使用 Azure 入口網站來建立具有端對端 SSL 加密的 Azure 應用程式閘道。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/30/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: a37b313bd808ee0441d84ac92050b087eba7ac9d
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/18/2019
ms.locfileid: "71097216"
---
# <a name="configure-end-to-end-ssl-by-using-application-gateway-with-the-portal"></a>透過入口網站使用應用程式閘道設定端對端 SSL

本文說明如何使用 Azure 入口網站，以應用程式閘道 v1 SKU 設定端對端 SSL 加密。  

> [!NOTE]
> 應用程式閘道 v2 SKU 需要受信任的根憑證，才能啟用端對端設定。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="before-you-begin"></a>開始之前

若要使用應用程式閘道設定端對端 SSL，閘道需要憑證，而且後端伺服器需要憑證。 閘道憑證用來根據 SSL 通訊協定規格衍生對稱金鑰。 接著會使用對稱金鑰來加密和解密傳送至閘道的流量。 對於端對端 SSL 加密，應用程式閘道中必須允許正確的後端伺服器。 若要這麼做，請將後端伺服器的公開憑證（也稱為驗證憑證（v1）或受信任的根憑證（v2））上傳至應用程式閘道。 新增憑證可確保應用程式閘道只會與已知的後端實例進行通訊。 這會進而保護端對端通訊。

若要深入瞭解，請參閱[ssl 終止和端對端 ssl](https://docs.microsoft.com/azure/application-gateway/ssl-overview)。

## <a name="create-a-new-application-gateway-with-end-to-end-ssl"></a>建立具有端對端 SSL 的新應用程式閘道

若要建立具有端對端 SSL 加密的新應用程式閘道，您必須先啟用 SSL 終止，同時建立新的應用程式閘道。 這會針對用戶端和應用程式閘道之間的通訊啟用 SSL 加密。 接著，您必須在 HTTP 設定中將後端伺服器的憑證列入允許清單，以啟用應用程式閘道與後端伺服器之間通訊的 SSL 加密，完成端對端 SSL 加密。

### <a name="enable-ssl-termination-while-creating-a-new-application-gateway"></a>建立新的應用程式閘道時啟用 SSL 終止

請參閱這篇文章，以瞭解如何[在建立新的應用程式閘道時啟用 SSL 終止](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)。

### <a name="add-authenticationroot-certificate-of-back-end-servers"></a>新增後端伺服器的驗證/根憑證

1. 選取 [所有資源]，然後選取 [myAppGateway]。

2. 從左側功能表中選取 [ **HTTP 設定**]。 當您建立應用程式閘道時，Azure 會自動建立預設的 HTTP 設定**appGatewayBackendHttpSettings**。 

3. 選取 [ **appGatewayBackendHttpSettings**]。

4. 在 [**通訊協定**] 底下，選取 [ **HTTPS**]。 **後端驗證憑證或受信任的根憑證**的窗格隨即出現。 

5. 選擇 [**新建**]。

6. 輸入適當的**名稱**。

7. 使用 [**上傳 CER 憑證**] 方塊選取憑證檔案。

   針對 Standard 和 WAF （v1）應用程式閘道，您應該以 .cer 格式上傳後端伺服器憑證的公開金鑰。

   ![addcert](./media/end-to-end-ssl-portal/addcert.png)

   針對 Standard_v2 和 WAF_v2 應用程式閘道，您應該以 .cer 格式上傳後端伺服器憑證的**根憑證**。 如果後端憑證是由知名 CA 所發行，您可以勾選 [使用已知的 CA 憑證] 方塊，而不需要上傳憑證。

   ![addtrustedrootcert](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

   ![rootcert](./media/end-to-end-ssl-portal/trustedrootcert.png)

8. 選取 [儲存]。

## <a name="enable-end-to-end-ssl-for-existing-application-gateway"></a>針對現有的應用程式閘道啟用端對端 SSL

若要設定具有端對端 SSL 加密的現有應用程式閘道，您必須先在接聽程式中啟用 SSL 終止。 這會針對用戶端和應用程式閘道之間的通訊啟用 SSL 加密。 接著，您必須在 HTTP 設定中將後端伺服器的憑證列入允許清單，以啟用應用程式閘道與後端伺服器之間通訊的 SSL 加密，完成端對端 SSL 加密。

您必須使用具有 HTTPS 通訊協定和憑證的接聽程式來啟用 SSL 終止。 因此，您可以選擇使用現有的接聽程式搭配 HTTPS 通訊協定和憑證，或建立新的接聽程式。 如果您選擇前者，可以忽略下面所述的步驟，在**現有的應用程式閘道上啟用 SSL 終止**，並直接移至 [**為後端伺服器新增驗證/受信任的根憑證**] 區段。 如果您選擇後者，請使用下列步驟。

### <a name="enable-ssl-termination-in-existing-application-gateway"></a>在現有的應用程式閘道上啟用 SSL 終止

1. 選取 [所有資源]，然後選取 [myAppGateway]。

2. 從左側功能表**中選取 [** 接聽程式]。

3. 根據您的需求選擇 [**基本**] 和 [**多網站**] 接聽程式。

4. 在 [**通訊協定**] 底下，選取 [ **HTTPS**]。 **憑證**的窗格隨即出現。

5. 上傳您想要用於用戶端和應用程式閘道之間 SSL 終止的 PFX 憑證。

   > [!NOTE]
   > 基於測試目的，您可以使用自我簽署憑證。 但不建議用於生產工作負載，因為它們難以管理，而不是完全安全。 瞭解如何[建立自我簽署憑證](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal#create-a-self-signed-certificate)。

6. 依據您的**需求，新增接聽程式的其他**必要設定。

7. 選取 [確定] 以儲存。

### <a name="add-authenticationtrusted-root-certificates-of-back-end-servers"></a>新增後端伺服器的驗證/受信任的根憑證

1. 選取 [所有資源]，然後選取 [myAppGateway]。

2. 從左側功能表中選取 [ **HTTP 設定**]。 您可以在現有的後端 HTTP 設定中將憑證列入允許清單，或建立新的 HTTP 設定。 在下列步驟中，我們會將憑證的預設 HTTP 設定（ **appGatewayBackendHttpSettings**）列入允許清單。

3. 選取 [ **appGatewayBackendHttpSettings**]。

4. 在 [**通訊協定**] 底下，選取 [ **HTTPS**]。 **後端驗證憑證或受信任的根憑證**的窗格隨即出現。 

5. 選擇 [**新建**]。

6. 輸入適合的**名稱**。

7. 使用 [**上傳 CER 憑證**] 方塊選取憑證檔案。

   針對 Standard 和 WAF （v1）應用程式閘道，您應該以 .cer 格式上傳後端伺服器憑證的公開金鑰。

   ![addcert](./media/end-to-end-ssl-portal/addcert.png)

   針對 Standard_v2 和 WAF_v2 應用程式閘道，您應該以 .cer 格式上傳後端伺服器憑證的**根憑證**。 如果後端憑證是由知名 CA 所發行，您可以勾選 [使用已知的 CA 憑證] 方塊，而不需要上傳憑證。

   ![addtrustedrootcert](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

8. 選取 [儲存]。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用 Azure CLI 以應用程式閘道管理網路流量](./tutorial-manage-web-traffic-cli.md)
