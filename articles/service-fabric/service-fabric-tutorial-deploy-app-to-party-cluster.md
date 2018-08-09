---
title: 將 Service Fabric 應用程式部署到 Azure 中的叢集 | Microsoft Docs
description: 了解如何從 Visual Studio 將應用程式部署到叢集。
services: service-fabric
documentationcenter: .net
author: rwike77
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/12/2018
ms.author: ryanwi,mikhegn
ms.custom: mvc
ms.openlocfilehash: 81cd4d247ba6153fd205ead36f29a52b420bb427
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2018
ms.locfileid: "39502823"
---
# <a name="tutorial-deploy-a-service-fabric-application-to-a-cluster-in-azure"></a>教學課程：將 Service Fabric 應用程式部署到 Azure 中的叢集

本教學課程是系列中的第二部分，示範如何將 Azure Service Fabric 應用程式部署到 Azure 中的新叢集。

在本教學課程中，您將了解如何：
> [!div class="checklist"]
> * 建立合作對象叢集。
> * 使用 Visual Studio 將應用程式部署至遠端叢集。

在本教學課程系列中，您將了解如何：
> [!div class="checklist"]
> * [建置 .NET Service Fabric 應用程式](service-fabric-tutorial-create-dotnet-app.md)
> * 將應用程式部署到遠端叢集
> * [將 HTTPS 端點新增至 ASP.NET Core 前端服務](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
> * [使用 Visual Studio Team Services 設定 CI/CD](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [設定應用程式的監視和診斷](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>必要條件

開始進行本教學課程之前：

* 如果您沒有 Azure 訂用帳戶，請建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [安裝 Visual Studio 2017](https://www.visualstudio.com/) 並安裝 **Azure 開發**以及 **ASP.NET 和 Web 開發**工作負載。
* [安裝 Service Fabric SDK](service-fabric-get-started.md)。

## <a name="download-the-voting-sample-application"></a>下載投票應用程式範例

如果您未在[本教學課程系列的第一部分](service-fabric-tutorial-create-dotnet-app.md)中建置投票應用程式範例，可以在此下載。 在命令視窗中執行下列命令，將範例應用程式存放庫複製到本機電腦。

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="publish-to-a-service-fabric-cluster"></a>發佈至 Service Fabric 叢集

應用程式備妥後，即可直接從 Visual Studio 將其部署到叢集。 [Service Fabric 叢集](https://docs.microsoft.com/en-gb/azure/service-fabric/service-fabric-deploy-anywhere)是一組由網路連接的虛擬或實體機器，可用來將您的微服務部署到其中並進行管理。

針對本教學課程，您有兩個選項可使用 Visual Studio 將「投票」應用程式部署到 Service Fabric 叢集：

* 發佈至試用 (合作對象) 叢集。
* 發佈至您訂用帳戶中的現有叢集。  您可以透過 [Azure 入口網站](https://portal.azure.com)，並使用 [PowerShel](./scripts/service-fabric-powershell-create-secure-cluster-cert.md) 或 [Azure CLI](./scripts/cli-create-cluster.md) 指令碼，或者從 [Azure Resource Manager 範本](service-fabric-tutorial-create-vnet-and-windows-cluster.md)建立 Service Fabric 叢集。

> [!NOTE]
> 許多服務會使用反向 Proxy 彼此通訊。 從 Visual Studio 和合作對象叢集建立的叢集預設都已啟用反向 Proxy。  如果使用現有叢集，您必須[在叢集中啟用反向 Proxy](service-fabric-reverseproxy-setup.md#)。


### <a name="find-the-votingweb-service-endpoint-for-your-azure-subscription"></a>尋找 Azure 訂用帳戶中的 VotingWeb 服務端點

如果您要將「投票」應用程式發佈至您自己的 Azure 訂用帳戶，請尋找前端 Web 服務的端點。 如果您使用合作對象叢集，「投票」範例所使用的連接埠 8080 會自動開啟，您不需要在合作對象叢集的負載平衡器中進行設定。

前端 Web 服務會接聽特定連接埠。  當應用程式部署至 Azure 中的叢集時，叢集和應用程式都會在 Azure 負載平衡器後方執行。  必須使用 Azure 負載平衡器中的規則來開啟應用程式連接埠，讓輸入流量可以通過以傳到 Web 服務。  您可以在 *VotingWeb/PackageRoot/ServiceManifest.xml* 檔案的 **Endpoint** 元素中找到連接埠 (例如 8080)：

```xml
<Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8080" />
```

針對您的 Azure 訂用帳戶，透過 [PowerShell 指令碼](./scripts/service-fabric-powershell-open-port-in-load-balancer.md)或透過 [Azure 入口網站](https://portal.azure.com)中此叢集的負載平衡器，使用 Azure 中的負載平衡器規則來開啟此連接埠。

### <a name="join-a-party-cluster"></a>加入合作對象叢集

> [!NOTE]
> 如果您要將應用程式發佈至您自己 Azure 訂用帳戶中的叢集，請移至下一節中的使用 Visual Studio 部署應用程式。

合作對象的叢集是免費的限時 Service Fabric 叢集，裝載於 Azure 上，並且由任何人都可以部署應用程式並了解平台的 Service Fabric 小組執行。 叢集會針對節點對節點和用戶端對節點安全性，使用單一的自我簽署憑證。

登入並[加入 Windows 叢集](http://aka.ms/tryservicefabric) \(英文\)。 藉由按一下 [PFX] 連結，將 PFX 憑證下載至您的電腦。 按一下 [如何連線至安全的合作對象叢集?] 連結，並複製憑證密碼。 後續步驟中會使用憑證、憑證密碼和 [連線端點] 值。

![PFX 和連線端點](./media/service-fabric-quickstart-dotnet/party-cluster-cert.png)

> [!Note]
> 每小時可用的合作對象叢集數目有限。 如果您在嘗試註冊合作對象叢集時收到錯誤訊息，您可以等候一段時間再重試，也可以遵循[部署 .NET 應用程式](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-app-to-party-cluster#deploy-the-sample-application)教學課程中的這些步驟，在您的 Azure 訂用帳戶中建立 Service Fabric 叢集並對其部署應用程式。 如果您還沒有 Azure 訂用帳戶，您可以建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
>

在 Windows 電腦上，將 PFX 安裝在 CurrentUser\My 憑證存放區。

```powershell
PS C:\mycertificates> Import-PfxCertificate -FilePath .\party-cluster-873689604-client-cert.pfx -CertStoreLocation Cert:\CurrentUser\My -Password (ConvertTo-SecureString 873689604 -AsPlainText -Force)


   PSParentPath: Microsoft.PowerShell.Security\Certificate::CurrentUser\My

Thumbprint                                Subject
----------                                -------
3B138D84C077C292579BA35E4410634E164075CD  CN=zwin7fh14scd.westus.cloudapp.azure.com
```

請記住指紋以在後續步驟中使用。

> [!Note]
> 根據預設，Web 前端服務設定為在連接埠 8080 上接聽傳入流量。 在合作對象叢集中，會開啟連接埠 8080。  如果您需要變更應用程式連接埠，請將它變更為合作對象叢集中開啟的連接埠之一。
>

### <a name="publish-the-application-using-visual-studio"></a>使用 Visual Studio 發佈應用程式

應用程式備妥後，即可直接從 Visual Studio 將其部署到叢集。

1. 以滑鼠右鍵按一下方案總管中的 [投票]，並選擇 [發行]。 [發行] 對話方塊隨即出現。

2. 將合作對象叢集頁面或 Azure 訂用帳戶上的 [連線端點] 複製到 [連線端點] 欄位。 例如： `zwin7fh14scd.westus.cloudapp.azure.com:19000`。 按一下 [進階連線參數]，並確定 FindValue 和 ServerCertThumbprint 值，符合先前步驟中針對合作對象叢集或憑證 (符合您的 Azure 訂用帳戶) 所安裝的憑證指紋。

    ![[發佈] 對話方塊](./media/service-fabric-quickstart-dotnet/publish-app.png)

    叢集中的每個應用程式都必須有一個唯一的名稱。  合作對象叢集是公用的共用環境，可能會與現有的應用程式發生衝突。  如果發生名稱衝突，請將 Visual Studio 專案重新命名並再次部署。

3. 按一下 [發佈] 。

4. 開啟瀏覽器並鍵入叢集位址，再加上 ': 8080' (或設定的其他連接埠) 以連線到叢集中的「投票」應用程式，例如 `http://zwin7fh14scd.westus.cloudapp.azure.com:8080`。 您現在應該會看到應用程式在 Azure 的叢集中執行。 在「投票」網頁上，嘗試新增和刪除投票選項，並投票表決一或多個選項。

    ![應用程式前端](./media/service-fabric-quickstart-dotnet/application-screenshot-new-azure.png)


## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 建立合作對象叢集。
> * 使用 Visual Studio 將應用程式部署至遠端叢集。

前進到下一個教學課程：
> [!div class="nextstepaction"]
> [啟用 HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
