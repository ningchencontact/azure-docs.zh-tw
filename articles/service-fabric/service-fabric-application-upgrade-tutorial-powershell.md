---
title: 使用 PowerShell 進行 Service Fabric 應用程式升級 | Microsoft Docs
description: 本文會逐步解說使用 PowerShell 來部署 Service Fabric 應用程式、變更程式碼及執行升級的體驗。
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: 9bc75748-96b0-49ca-8d8a-41fe08398f25
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: c8f56897380bc3108cb979d9d15e7dbd0a329064
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60614382"
---
# <a name="service-fabric-application-upgrade-using-powershell"></a>使用 PowerShell 進行 Service Fabric 應用程式升級
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Visual Studio](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

最常使用和建議的升級方法是監視輪流升級。  Azure Service Fabric 會根據健康狀態原則集，監視正在升級之應用程式的健康狀態。 升级某个更新域 (UD) 之后，Service Fabric 将评估应用程序运行状况，根据运行状况策略继续升级下一个更新域，或者使升级失败。

可使用托管或本机 API、PowerShell、Azure CLI、Java 或 REST 执行受监视的应用程序升级。 有关使用 Visual Studio 执行升级的说明，请参阅[使用 Visual Studio 升级应用程序](service-fabric-application-upgrade-tutorial.md)。

使用 Service Fabric 监视的滚动升级，应用程序管理员可以配置 Service Fabric 用于确定应用程序运行状况是否正常的运行状况评估策略。 此外，系統管理員也可設定當健康狀態評估失敗時採取的動作 (例如，進行自動回復)。本部分演练使用 PowerShell 对其中一个 SDK 示例进行受监视的升级。 

## <a name="step-1-build-and-deploy-the-visual-objects-sample"></a>步骤 1：建置和部署視覺物件範例
单击右键应用程序项目 **VisualObjectsApplication**，并选择“**发布**”命令生成并发布应用程序。  如需詳細資訊，請參閱 [Service Fabric 應用程式升級教學課程](service-fabric-application-upgrade-tutorial.md)。  或者，您可以使用 PowerShell 來部署您的應用程式。

> [!NOTE]
> 在 PowerShell 中使用任何 Service Fabric 命令之前，您必須先使用 `Connect-ServiceFabricCluster` Cmdlet 連接到叢集。 同样，假设已在本地计算机上设置了群集。 請參閱 [設定 Service Fabric 開發環境](service-fabric-get-started.md)上的文章
> 
> 

在 Visual Studio 中构建项目后，可以使用 PowerShell 命令 [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) 将应用程序包复制到 ImageStore。 如果要在本地验证应用包，请使用 [Test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage) cmdlet。 下一个步骤是使用 [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype) cmdlet 将应用程序注册到 Service Fabric 运行时。 接下来的一步骤是使用 [New-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) cmdlet 启动应用程序实例。  这三个步骤类似于使用 Visual Studio 中的“**部署**”菜单项。  完成预配后，应从映像存储区中清除复制的应用程序包以减少占用的资源。  如果不再需要應用程式類型，應該基於相同原因將其取消註冊。 有关详细信息，请按照[使用 PowerShell 部署和删除应用程序](service-fabric-application-upgrade-tutorial-powershell.md)。

现在可以使用 [Service Fabric Explorer 查看群集和应用程序](service-fabric-visualizing-your-cluster.md)。 应用程序具有一个 Web 服务，可通过在 Internet Explorer 地址栏中键入 [http://localhost:8081/visualobjects](http://localhost:8081/visualobjects) 导航到该 Web 服务。  应在屏幕上看到一些四处移动的浮动视觉对象。  此外，可使用 [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps) 检查应用程序状态。

## <a name="step-2-update-the-visual-objects-sample"></a>步骤 2：更新視覺物件範例
你可能会注意到，使用步骤 1 中部署的版本，视觉对象不会旋转。 让我们将此应用程序升级到视觉对象也会旋转的版本。

選取 VisualObjects 解決方案內的 VisualObjects.ActorService 專案，然後開啟 StatefulVisualObjectActor.cs 檔案。 在該檔案內，瀏覽至 `MoveObject` 方法，然後將 `this.State.Move()` 標記為註解，然後將 `this.State.Move(true)` 取消註解。 此项更改可在升级服务后旋转对象。

我們也需要更新 *VisualObjects.ActorService* 專案的 **ServiceManifest.xml**檔案 (在 [PackageRoot] 底下)。 請將 *ServiceManifest.xml* 檔案中的 *CodePackage* 和服務版本及對應的行更新成 2.0。
您可以在對解決方案按一下滑鼠右鍵之後，使用 Visual Studio [編輯資訊清單檔案]  選項來進行資訊清單檔案變更。

完成更改后，清单应该如下所示（突出显示的部分即为所做的更改）：

```xml
<ServiceManifestName="VisualObjects.ActorService" Version="2.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">

<CodePackageName="Code" Version="2.0">
```

現在，*ApplicationManifest.xml* 檔案 (可以在 **VisualObjects** 方案下的 **VisualObjects** 專案下找到)，會更新為 2.0 版的 **VisualObjects.ActorService** 專案。 此外，應用程式版本也會從 1.0.0.0 更新為 2.0.0.0。 *ApplicationManifest.xml* 看起來應該類似下列程式碼片段︰

```xml
<ApplicationManifestxmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VisualObjects" ApplicationTypeVersion="2.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

 <ServiceManifestRefServiceManifestName="VisualObjects.ActorService" ServiceManifestVersion="2.0" />
```

現在只選取 [ActorService] 專案，然後以滑鼠右鍵按一下並選取 Visual Studio 中的 [組建] 選項建置專案。 如果您選取 [全部重建]，因為程式碼已變更，所以您要更新所有專案的版本。 接下來，在 [VisualObjectsApplication] 上按一下滑鼠右鍵，選取 [Service Fabric] 功能表，然後選擇 [封裝]，來封裝已更新的應用程式。 這個動作會建立可部署的應用程式封裝。  更新的应用程序已准备就绪，可供部署。

## <a name="step-3--decide-on-health-policies-and-upgrade-parameters"></a>步骤 3：決定健康情況原則並升級參數
请熟悉[应用程序升级参数](service-fabric-application-upgrade-parameters.md)和[升级过程](service-fabric-application-upgrade.md)，充分了解所应用的各种升级参数、超时和运行状况条件。 对于本演练，服务运行状况评估条件设置为默认值（即推荐值），这意味着在升级后所有服务和实例均应为*运行状况正常*。  

但是，让我们将 *HealthCheckStableDuration* 增加到 180 秒（这样该服务在进行下一个更新域的升级之前将至少保持 120 秒的运行状况正常）。  我们还将 *UpgradeDomainTimeout* 设置为 1200 秒，将 *UpgradeTimeout* 设置为 3000 秒。

最後，我們也將 *UpgradeFailureAction* 設定為回復。 此選項要求 Service Fabric 在升級期間如果遇到任何問題時要將應用程式回復為舊版。 因此在啟動升級 (在步驟 4) 時，會指定下列參數︰

FailureAction = Rollback

HealthCheckStableDurationSec = 180

UpgradeDomainTimeoutSec = 1200

UpgradeTimeout = 3000

## <a name="step-4-prepare-application-for-upgrade"></a>步骤 4：準備應用程式以進行升級
现在，应用程序已生成并准备好进行升级。 如果您以系統管理員身分開啟 PowerShell 視窗並且輸入 [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps)，它應該會讓您知道它是已部署之 **VisualObjects** 的應用程式類型 1.0.0.0。  

應用程式封裝儲存在以下的相對路徑，您在其中解壓縮 Service Fabric SDK - *Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug*。 您應該會在該目錄中找到 "Package" 資料夾，這是應用程式封裝儲存的位置。 檢查時間戳記以確保它是最新組建 (您也可能需要適當地修改路徑)。

現在讓我們將更新的應用程式封裝複製到 Service Fabric ImageStore (Service Fabric 在其中儲存應用程式封裝)。 參數 *ApplicationPackagePathInImageStore* 會通知 Service Fabric 可以在哪裡找到應用程式封裝。 我们可以使用以下命令将更新的应用程序放入“VisualObjects\_V2”（可能需要再次相应地修改路径）。

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug\Package -ApplicationPackagePathInImageStore "VisualObjects\_V2"
```

下一步是向 Service Fabric 註冊此應用程式，這可以使用 [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) 命令來執行：

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore "VisualObjects\_V2"
```

如果上述命令不成功，很可能是您需要重建所有服務。 如步骤 2 中所述，可能还需要更新 WebService 版本。

建议在成功注册应用程序后删除应用程序包。  從映像存放區刪除應用程式套件會釋放系統資源。  保留未使用的应用程序包会占用磁盘存储空间，导致应用程序出现性能问题。

```powershell
Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore "VisualObjects\_V2" -ImageStoreConnectionString fabric:ImageStore
```

## <a name="step-5-start-the-application-upgrade"></a>步骤 5：開始應用程式升級
现在可以使用 [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps) 命令开始升级应用程序：

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/VisualObjects -ApplicationTypeVersion 2.0.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000   -FailureAction Rollback -Monitored
```


应用程序名称与 *ApplicationManifest.xml* 文件中所述的相同。 Service Fabric 會使用這個名稱來識別要升級哪一個應用程式。 如果您設定的逾時太短，您可能會遇到失敗訊息，指出此問題。 请参阅故障排除部分，或增加超时值。

現在，當應用程式升級進行時，您可以使用 Service Fabric Explorer 或 [Get-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/get-servicefabricapplicationupgrade?view=azureservicefabricps) PowerShell 命令監視進度。 

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/VisualObjects
```

幾分鐘後，使用上述 PowerShell 命令所取得的狀態應該會顯示所有更新網域已升級 (完成)。 而且，您應該會在瀏覽器視窗中發現該視覺物件已經開始旋轉！

您可以嘗試從第 2 版升級到第 3 版，或練習從第 2 版升級為第 1 版。 從第 2 版移至第 1 版也視為一種升級。 練習逾時和健康狀態原則，讓自己更熟練。 當您部署至 Azure 叢集時，參數必須正確設定。 最好保守地設定逾時。

## <a name="next-steps"></a>后续步骤
[使用 Visual Studio 升級您的應用程式](service-fabric-application-upgrade-tutorial.md) 將引導您完成使用 Visual Studio 進行應用程式升級的步驟。

使用 [升級參數](service-fabric-application-upgrade-parameters.md)來控制您應用程式的升級方式。

了解如何使用 [資料序列化](service-fabric-application-upgrade-data-serialization.md)，以讓您的應用程式升級相容。

參考 [進階主題](service-fabric-application-upgrade-advanced.md)，以了解如何在升級您的應用程式時使用進階功能。

參考 [疑難排解應用程式升級](service-fabric-application-upgrade-troubleshooting.md)中的步驟，以修正應用程式升級中常見的問題。

