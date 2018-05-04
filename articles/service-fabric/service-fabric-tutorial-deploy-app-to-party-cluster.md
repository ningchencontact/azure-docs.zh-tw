---
title: 從 Visual Studio 將 Azure Service Fabric 應用程式部署到叢集 | Microsoft Docs
description: 了解如何從 Visual Studio 將應用程式部署到叢集
services: service-fabric
documentationcenter: .net
-author: mikkelhegn
-manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/23/2018
ms.author: mikhegn
ms.custom: mvc
ms.openlocfilehash: 4f0d41dbc2438217cb4f382da7c44833379b9637
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2018
---
# <a name="tutorial-deploy-an-application-to-a-service-fabric-cluster-in-azure"></a>教學課程：將應用程式部署到 Azure 中的 Service Fabric 叢集
本教學課程是系列中的第二部分，示範如何直接從 Visual Studio 將 Azure Service Fabric 應用程式部署到 Azure 中的新叢集。

在本教學課程中，您將了解如何：
> [!div class="checklist"]
> * 從 Visual Studio 建立叢集
> * 使用 Visual Studio 將應用程式部署至遠端叢集


在本教學課程系列中，您將了解如何：
> [!div class="checklist"]
> * [建置 .NET Service Fabric 應用程式](service-fabric-tutorial-create-dotnet-app.md)
> * 將應用程式部署到遠端叢集
> * [將 HTTPS 端點新增至 ASP.NET Core 前端服務](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
> * [使用 Visual Studio Team Services 設定 CI/CD](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [設定應用程式的監視和診斷](service-fabric-tutorial-monitoring-aspnet.md)


## <a name="prerequisites"></a>先決條件
開始進行本教學課程之前：
- 如果您沒有 Azure 訂用帳戶，請建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [安裝 Visual Studio 2017](https://www.visualstudio.com/) 並安裝 **Azure 開發**以及 **ASP.NET 和 Web 開發**工作負載。
- [安裝 Service Fabric SDK](service-fabric-get-started.md)

## <a name="download-the-voting-sample-application"></a>下載投票應用程式範例
如果您未在[本教學課程系列的第一部分](service-fabric-tutorial-create-dotnet-app.md)中建置投票應用程式範例，可以在此下載。 在命令視窗中執行下列命令，將範例應用程式存放庫複製到本機電腦。

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="deploy-the-sample-application"></a>部署範例應用程式

### <a name="select-a-service-fabric-cluster-to-which-to-publish"></a>選取要發佈至的 Service Fabric 叢集。
應用程式備妥後，即可直接從 Visual Studio 將其部署到叢集。

您有兩個部署選項：
- 從 Visual Studio 建立叢集。 此選項可讓您使用慣用的組態，直接從 Visual Studio 建立安全的叢集。 這種叢集類型很適合用於測試案例，您可以在其中建立叢集，然後在 Visual Studio 內直接發佈至該叢集。
- 發佈至您訂用帳戶中的現有叢集。

本教學課程會依照步驟從 Visual Studio 建立叢集。 您也可以複製和貼上您的連線端點，或從您的訂用帳戶中選擇。
> [!NOTE]
> 許多服務會使用反向 Proxy 彼此通訊。 從 Visual Studio 和合作對象叢集建立的叢集預設都已啟用反向 Proxy。  如果使用現有叢集，您必須[在叢集中啟用反向 Proxy](service-fabric-reverseproxy.md#setup-and-configuration)。

### <a name="deploy-the-app-to-the-service-fabric-cluster"></a>將應用程式部署到 Service Fabric 叢集
1. 以滑鼠右鍵按一下 [方案總管] 中的應用程式專案，然後選擇 [發佈]。

2. 使用您的 Azure 帳戶登入，以便存取您的訂用帳戶。 如果您使用合作對象叢集，可以選擇是否進行此步驟。

3. 選取 [連線端點] 下拉式清單，然後選取 <Create New Cluster...> 選項。
    
    ![[發佈] 對話方塊](./media/service-fabric-tutorial-deploy-app-to-party-cluster/publish-app.png)
    
4. 在 [建立叢集] 對話方塊中，修改下列設定：

    1. 在 [叢集名稱] 欄位中指定叢集名稱，以及訂用帳戶和您想要使用的位置。
    2. 您可以選擇是否修改節點的數目。 根據預設，您有三個節點，這是測試 Service Fabric 案例所需的最少節點數。
    3. 選取 [憑證] 索引標籤。在此索引標籤中，輸入要用來保護叢集憑證的密碼。 此憑證可協助保護您的叢集。 您也可以修改您要儲存憑證的路徑。 Visual Studio 也可以為您匯入憑證，因為這是要將應用程式發佈至叢集所需的項目。
    4. 選取 [VM 詳細資料] 索引標籤。指定您想用於組成叢集之虛擬機器 (VM) 的密碼。 使用者名稱和密碼可用來從遠端連線到 VM。 您也必須選取 VM 機器大小，並可視需要變更 VM 映像。
    5. 選擇性：在 [進階] 索引標籤上，您可以修改要在負載平衡器上開啟的連接埠清單 (這些連接埠將與叢集一起建立)。 您也可以新增現有的 Application Insights 金鑰，此金鑰會用於路由傳送應用程式記錄檔。
    6. 當您完成設定修改時，請選取 [建立] 按鈕。 建立作業需要幾分鐘才能完成；輸出視窗會指出叢集何時建立完成。
    
    ![建立叢集對話方塊](./media/service-fabric-tutorial-deploy-app-to-party-cluster/create-cluster.png)

4. 您要使用的叢集準備就緒後，以滑鼠右鍵按一下應用程式專案，然後選擇 [發佈]。

    發佈完成後，您應該能夠透過瀏覽器將要求傳送到應用程式。

5. 開啟您偏好的瀏覽器，並輸入叢集位址 (不含連接埠資訊的連線端點，例如，win1kw5649s.westus.cloudapp.azure.com)。

    您現在應該會看到與在本機執行應用程式相同的結果。

    ![叢集的 API 回應](./media/service-fabric-tutorial-deploy-app-to-party-cluster/response-from-cluster.png)

## <a name="next-steps"></a>後續步驟
在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 從 Visual Studio 建立叢集
> * 使用 Visual Studio 將應用程式部署至遠端叢集

前進到下一個教學課程：
> [!div class="nextstepaction"]
> [啟用 HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
