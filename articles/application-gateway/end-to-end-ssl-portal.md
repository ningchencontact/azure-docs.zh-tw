---
title: 快速入門-使用 Azure 應用程式閘道-Azure 入口網站設定端對端 SSL 加密 |Microsoft Docs
description: 了解如何使用 Azure 入口網站建立 Azure 應用程式閘道端對端 SSL 加密。
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 3/19/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: e47a3e1231701f3339057e25ee4388aff0c9fbd7
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58227560"
---
# <a name="configure-end-to-end-ssl-by-using-application-gateway-with-the-portal"></a>藉由使用入口網站應用程式閘道設定端對端 SSL

這篇文章會示範如何使用 Azure 入口網站中，若要使用的應用程式閘道設定端對端 SSL 加密 v1 SKU。  

> [!NOTE]
> 應用程式閘道 v2 SKU 需要啟用端對端設定的受信任的根憑證。 入口網站加入信任的根憑證的支援尚未提供。 因此，如果 V2 SKU 看到[設定使用 PowerShell 的端對端 SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="before-you-begin"></a>開始之前

若要對應用程式閘道設定端對端 SSL，則需要閘道憑證和後端伺服器憑證。 閘道憑證用來根據 SSL 通訊協定規格衍生對稱金鑰。 對稱金鑰則用來加密和解密流量傳送至閘道中。 若要加密端對端 SSL，後端必須已加入到應用程式閘道的允許清單。 若要這樣做，請上傳至應用程式閘道的後端伺服器，也就是驗證憑證的公開憑證。 新增憑證可確保應用程式閘道只會與已知的後端執行個體通訊。 這會進而保護端對端通訊。

若要進一步了解，請參閱[SSL 終止和端對端 SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview)。

## <a name="create-a-new-application-gateway-with-end-to-end-ssl"></a>使用端對端 SSL 建立新的應用程式閘道

若要建立新的應用程式閘道端對端 SSL 加密，您必須先啟用 SSL 終止時建立新的應用程式閘道。 這可讓用戶端和應用程式閘道之間的通訊的 SSL 加密。 然後，您必須以 HTTP 設定中的後端伺服器的允許清單憑證來為應用程式閘道與後端伺服器，因此完成端對端 SSL 加密之間的通訊啟用 SSL 加密。

### <a name="enable-ssl-termination-while-creating-a-new-application-gateway"></a>啟用 SSL 終止時建立新的應用程式閘道

請參閱此文章以了解如何[啟用時建立新的應用程式閘道的 SSL 終止](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)。

### <a name="whitelist-certificates-for-backend-servers"></a>後端伺服器的允許清單憑證

1. 選取 [所有資源]，然後選取 [myAppGateway]。

2. 選取  **http-settings**從左側功能表中。 Azure 會自動建立預設 HTTP 設定中， **appGatewayBackendHttpSettings**，當您建立應用程式閘道。 

3. 選取  **appGatewayBackendHttpSettings**。

4. 底下**通訊協定**，選取**HTTPS**。 窗格**後端驗證憑證**會出現。 

5. 底下**後端驗證憑證**，選擇**新建**。

6. 輸入適當**名稱**。

7. 使用憑證**上傳 CER 憑證** 方塊中。![addcert](./media/end-to-end-ssl-portal/addcert.png)

   > [!NOTE]
   > 在此步驟中所提供的憑證，應該是在後端上出現的 .pfx 憑證本身的公開金鑰。 將安裝在後端伺服器上的憑證 (非根憑證) 以「宣告、證據和推論」(CER) 格式匯出，並將它用於此步驟。 此步驟會將後端加入到應用程式閘道的允許清單。

8. 選取 [ **儲存**]。

## <a name="enable-end-to-end-ssl-for-existing-application-gateway"></a>啟用現有的應用程式閘道的端對端 SSL

若要將現有的應用程式閘道設定端對端 SSL 加密，您必須在接聽程式的第一個啟用 SSL 終止。 這可讓用戶端和應用程式閘道之間的通訊的 SSL 加密。 然後，您必須以 HTTP 設定中的後端伺服器的允許清單憑證來為應用程式閘道與後端伺服器，因此完成端對端 SSL 加密之間的通訊啟用 SSL 加密。

您必須使用 HTTPS 通訊協定與憑證使用接聽程式，啟用 SSL 終止。 您無法變更現有接聽程式通訊協定。 因此，您可以選擇使用現有的接聽程式使用 HTTPS 通訊協定和憑證，或建立新的接聽程式。 如果您選擇先前的功能，您可以忽略如下所述的步驟**現有的應用程式閘道中的 啟用 SSL 終止**並直接移至**後端伺服器的允許清單憑證**一節。 如果您選擇後者，請執行下列步驟。 

### <a name="enable-ssl-termination-in-existing-application-gateway"></a>啟用現有的應用程式閘道中的 SSL 終止

1. 選取 [所有資源]，然後選取 [myAppGateway]。

2. 選取 **接聽程式**從左側功能表中。

3. 選擇**基本**並**多站台**接聽程式，根據您的需求。

4. 底下**通訊協定**，選取**HTTPS**。 窗格**憑證**會出現。

5. 上傳您想要使用用戶端和應用程式閘道之間的 SSL 終止的 PFX 憑證。

   > [!NOTE]
   > 基於測試目的，您可以使用自我簽署的憑證。 您不應該用於生產工作負載使用自我簽署的憑證。 了解如何[建立自我簽署的憑證](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal#create-a-self-signed-certificate)。

6. 新增其他必要的設定，如**接聽程式**根據您的需求。

7. 選取 [確定] 以儲存。

### <a name="whitelist-certificates-for-backend-servers"></a>後端伺服器的允許清單憑證

1. 選取 [所有資源]，然後選取 [myAppGateway]。

2. 選取  **http-settings**從左側功能表中。 您可以其中一個允許清單中的憑證的現有後端 HTTP 設定，或建立新的 HTTP 設定。 在以下步驟中，我們將會預設 HTTP 設定中，憑證允許清單**appGatewayBackendHttpSettings**。

3. 選取  **appGatewayBackendHttpSettings**。

4. 底下**通訊協定**，選取**HTTPS**。 窗格**後端驗證憑證**會出現。 

5. 底下**後端驗證憑證**，選擇**新建**。

6. 輸入適當**名稱**。

7. 使用憑證**上傳 CER 憑證** 方塊中。![addcert](./media/end-to-end-ssl-portal/addcert.png)

   > [!NOTE]
   > 在此步驟中所提供的憑證，應該是在後端上出現的 .pfx 憑證本身的公開金鑰。 將安裝在後端伺服器上的憑證 (非根憑證) 以「宣告、證據和推論」(CER) 格式匯出，並將它用於此步驟。 此步驟會將後端加入到應用程式閘道的允許清單。

8. 選取 [ **儲存**]。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用 Azure CLI 以應用程式閘道管理網路流量](./tutorial-manage-web-traffic-cli.md)
