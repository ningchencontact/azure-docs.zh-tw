---
title: 教學課程：設定包含 SSL 終止的應用程式閘道 - Azure 入口網站
description: 在此教學課程中，您將了解如何使用 Azure 入口網站設定應用程式閘道，並新增 SSL 終止的憑證。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 4/17/2019
ms.author: victorh
ms.openlocfilehash: f3ba3eb12dc85a72c4e49c374e62209b83400d33
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "66134438"
---
# <a name="tutorial-configure-an-application-gateway-with-ssl-termination-using-the-azure-portal"></a>教學課程：使用 Azure 入口網站設定包含 SSL 終止的應用程式閘道

您可以使用 Azure 入口網站來設定[應用程式閘道](overview.md)，當中包含使用虛擬機器作為後端伺服器的 SSL 終止憑證。

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 建立自我簽署憑證
> * 建立包含憑證的應用程式閘道
> * 建立用來作為後端伺服器的虛擬機器
> * 測試應用程式閘道

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>登入 Azure

在 [https://portal.azure.com](https://portal.azure.com) 登入 Azure 入口網站

## <a name="create-a-self-signed-certificate"></a>建立自我簽署憑證

在此節中，您將使用 [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) 來建立自我簽署的憑證。 當您為應用程式閘道建立接聽程式時，須將憑證上傳至 Azure 入口網站。

在您的本機電腦上，以系統管理員身分開啟 Windows PowerShell 視窗。 執行下列命令來建立憑證：

```powershell
New-SelfSignedCertificate \
  -certstorelocation cert:\localmachine\my \
  -dnsname www.contoso.com
```

您應該會看到類似這個回應的內容：

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com

Use [Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate) with the Thumbprint that was returned to export a pfx file from the certificate:
```

```powershell
$pwd = ConvertTo-SecureString -String "Azure123456!" -Force -AsPlainText
Export-PfxCertificate \
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 \
  -FilePath c:\appgwcert.pfx \
  -Password $pwd
```

## <a name="create-an-application-gateway"></a>建立應用程式閘道

需要虛擬網路，才能在您所建立的資源之間進行通訊。 這個範例中會建立兩個子網路：一個用於應用程式閘道，另一個用於後端伺服器。 您建立應用程式閘道時，可以同時建立虛擬網路。

1. 選取 Azure 入口網站左上角的 [新增]。
2. 在 [精選] 清單中選取 [網路]，然後選取 [應用程式閘道]。
3. 輸入 myAppGateway 作為應用程式閘道的名稱，輸入 myResourceGroupAG 作為新的資源群組。
4. 接受其他設定的預設值，然後選取 [確定]。
5. 選取 [選擇虛擬網路]，接著選取 [新建]，然後針對虛擬網路輸入這些值：

   - myVNet - 作為虛擬網路的名稱。
   - 10.0.0.0/16 - 作為虛擬網路位址空間。
   - myAGSubnet - 作為子網路名稱。
   - 10.0.0.0/24 - 作為子網路位址空間。

     ![建立虛擬網路](./media/create-ssl-portal/application-gateway-vnet.png)

6. 選取 [確定] 以建立虛擬網路和子網路。
7. 依序選取 [選擇公用 IP 位址]、[新建]，然後輸入公用 IP 位址的名稱。 在此範例中，公用 IP 位址名為 myAGPublicIPAddress。 接受其他設定的預設值，然後選取 [確定]。
8. 選取接聽程式通訊協定的 [HTTPS]，並確定連接埠定義為 **443**。
9. 選取資料夾圖示，並瀏覽至您先前建立的 appgwcert.pfx 憑證，以將其上傳。
10. 輸入 mycert1 作為憑證的名稱，並輸入 Azure123456! 作為密碼，然後選取 [確定]。

    ![建立新的應用程式閘道](./media/create-ssl-portal/application-gateway-create.png)

11. 檢閱 [摘要] 頁面上的設定，然後選取 [確定] 以建立網路資源和應用程式閘道。 建立應用程式閘道可能需要幾分鐘的時間，請等候部署成功完成後，再繼續進行至下一節。

### <a name="add-a-subnet"></a>新增子網路

1. 選取左側功能表中的 [所有資源]，然後從 [資源] 清單中選取 [myVNet]。
2. 選取 [子網路]，然後選取 [子網路]。

    ![建立子網路](./media/create-ssl-portal/application-gateway-subnet.png)

3. 輸入 myBackendSubnet 作為子網路的名稱，然後選取 [確定]。

## <a name="create-backend-servers"></a>建立後端伺服器

在此範例中，您要建立兩個虛擬機器，作為應用程式閘道的後端伺服器。 您也可以在虛擬機器上安裝 IIS，以確認成功建立應用程式閘道。

### <a name="create-a-virtual-machine"></a>建立虛擬機器

1. 選取 [ **新增**]。
2. 選取 [計算]，然後選取 [精選] 清單中的 [Windows Server 2016 Datacenter]。
3. 針對虛擬機器，請輸入這些值：

    - myVM - 作為虛擬機器的名稱。
    - azureuser - 作為系統管理員使用者名稱。
    - *Azure123456!* 作為密碼。
    - 選取 [使用現有的]，然後選取 [myResourceGroupAG]。

4. 選取 [確定] 。
5. 選取 [DS1_V2] 作為虛擬機器的大小，然後選取 [選取]。
6. 確定您已選取 [myVNet] 作為虛擬網路，而且子網路是 [myBackendSubnet]。 
7. 選取 [停用] 來停用開機診斷。
8. 選取 [確定]，檢閱 [摘要] 頁面上的設定，然後選取 [建立]。

### <a name="install-iis"></a>安裝 IIS

1. 開啟互動式殼層，並確定它是設定為 **PowerShell**。

    ![安裝自訂延伸模組](./media/create-ssl-portal/application-gateway-extension.png)

2. 執行下列命令以在虛擬機器上安裝 IIS： 

    ```azurepowershell-interactive
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -ExtensionName IIS `
      -VMName myVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
      -Location EastUS
    ```

3. 建立第二個虛擬機器，並使用您剛完成的步驟來安裝 IIS。 輸入 myVM2 作為其名稱，及作為 Set-AzVMExtension 中的 VMName。

### <a name="add-backend-servers"></a>新增後端伺服器

1. 選取 [所有資源]，然後選取 [myAppGateway]。
1. 選取 [後端集區]。 已自動建立具有應用程式閘道的預設集區。 選取 **appGatewayBackendPool**。
1. 選取 [新增目標]，將您所建立的每個虛擬機器新增至後端集區。

    ![新增後端伺服器](./media/create-ssl-portal/application-gateway-backend.png)

1. 選取 [ **儲存**]。

## <a name="test-the-application-gateway"></a>測試應用程式閘道

1. 選取 [所有資源]，然後選取 [myAGPublicIPAddress]。

    ![記錄應用程式閘道公用 IP 位址](./media/create-ssl-portal/application-gateway-ag-address.png)

2. 將公用 IP 位址複製並貼到您瀏覽器的網址列。 若要在使用自我簽署憑證時接受安全性警告，請依序選取 [詳細資料] 與 [繼續瀏覽網頁]：

    ![安全警告](./media/create-ssl-portal/application-gateway-secure.png)

    接著會顯示受保護的 IIS 網站，如下列範例所示：

    ![在應用程式閘道中測試基底 URL](./media/create-ssl-portal/application-gateway-iistest.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [深入了解 Azure 應用程式閘道的用途](application-gateway-introduction.md)
