---
title: 教學課程 - 建立 Azure Red Hat OpenShift 叢集 | Microsoft Docs
description: 了解如何使用 Azure CLI 建立 Microsoft Azure Red Hat OpenShift 叢集
services: container-service
author: TylerMSFT
ms.author: twhitney
manager: jeconnoc
ms.topic: tutorial
ms.service: openshift
ms.date: 05/06/2019
ms.openlocfilehash: 5bc71a2d0f29fed163fb5c93ebd27df7f66a1325
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65079473"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-cluster"></a>教學課程：建立 Azure Red Hat OpenShift 叢集

本教學課程是一個系列的第一部分。 您將了解如何使用 Azure CLI 建立 Microsoft Azure Red Hat OpenShift 叢集、進行調整，然後予以刪除來清除資源。

在系列的第一部分中，您將了解如何：

> [!div class="checklist"]
> * 建立 Azure Red Hat OpenShift 叢集

在本教學課程系列中，您將了解如何：
> [!div class="checklist"]
> * 建立 Azure Red Hat OpenShift 叢集
> * [調整 Azure Red Hat OpenShift 叢集](tutorial-scale-cluster.md)
> * [刪除 Azure Red Hat OpenShift 叢集](tutorial-delete-cluster.md)

## <a name="prerequisites"></a>必要條件

開始進行本教學課程之前：

請確定您已[設定開發環境](howto-setup-environment.md)，其中包括：
- 安裝最新的 CLI
- 建立租用戶
- 建立 Azure 應用程式物件
- 建立 Active Directory 使用者，用來登入在叢集上執行的應用程式。

## <a name="step-1-sign-in-to-azure"></a>步驟 1：登入 Azure

如果您在本機執行 Azure CLI，請開啟 Bash 命令殼層並執行 `az login` 來登入 Azure。

```bash
az login
```

 如果您可存取多個訂用帳戶，請執行 `az account set -s {subscription ID}` 並以您想使用的訂用帳戶取代 `{subscription ID}`。

## <a name="step-2-create-an-azure-red-hat-openshift-cluster"></a>步驟 2：建立 Azure Red Hat OpenShift 叢集

在 Bash 命令視窗中，設定下列變數：

> [!IMPORTANT]
> 您的叢集名稱必須全部小寫，否則叢集建立會失敗。

```bash
CLUSTER_NAME=<cluster name in lowercase>
```

 對您在[建立新的應用程式註冊](howto-aad-app-configuration.md#create-a-new-app-registration)步驟 6 中選擇的叢集，使用相同的名稱。

```bash
LOCATION=<location>
```

選擇要建立叢集的位置。 如需在 Azure 上支援 OpenShift 的 Azure 區域清單，請參閱[支援的區域](supported-resources.md#azure-regions)。 例如： `LOCATION=eastus` 。

將 `FQDN` 設定為叢集的完整名稱。 此名稱是由叢集名稱、位置和結尾附加的 `.cloudapp.azure.com` 所組成。 這與您在[建立新的應用程式註冊](howto-aad-app-configuration.md#create-a-new-app-registration)步驟 6 中建立的登入 URL 相同。 例如︰  

```bash
FQDN=$CLUSTER_NAME.$LOCATION.cloudapp.azure.com
```

將 `APPID` 設定為您在[建立新的應用程式註冊](howto-aad-app-configuration.md#create-a-new-app-registration)步驟 9 中儲存的值。  

```bash
APPID=<app ID value>
```

將 `SECRET` 設定為您在[建立用戶端密碼](howto-aad-app-configuration.md#create-a-client-secret)步驟 6 中儲存的值。  

```bash
SECRET=<secret value>
```

將 `TENANT` 設定為您在[建立新的租用戶](howto-create-tenant.md#create-a-new-azure-ad-tenant)步驟 7 中儲存的租用戶識別碼值。  

```bash
TENANT=<tenant ID>
```

建立叢集的資源群組。 從您用來定義上述變數的 Bash 殼層執行下列命令：

```bash
az group create --name $CLUSTER_NAME --location $LOCATION
```

### <a name="optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network"></a>選用：將叢集的虛擬網路連線到現有的虛擬網路

如果您不需將所建立叢集的虛擬網路 (VNET) 連線到現有的 VNET，請略過此步驟。

首先，取得現有 VNET 的識別碼。 識別碼的格式會是：`/subscriptions/{subscription id}/resourceGroups/{resource group of VNET}/providers/Microsoft.Network/virtualNetworks/{VNET name}`。

如果您不知道網路名稱或現有 VNET 所屬的資源群組，請移至[虛擬網路刀鋒視窗](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Network%2FvirtualNetworks)，然後按一下您的虛擬網路。 [虛擬網路] 頁面隨即出現，其中會列出網路名稱及其所屬的資源群組。

在 BASH 殼層中使用下列 CLI 命令定義 VNET_ID 變數：

```bash
VNET_ID=$(az network vnet show -n {VNET name} -g {VNET resource group} --query id -o tsv)
```

例如：`VNET_ID=$(az network vnet show -n MyVirtualNetwork -g MyResourceGroup --query id -o tsv`

### <a name="create-the-cluster"></a>建立叢集

您現在可以開始建立叢集。

 如果您不會將叢集的虛擬網路連線到現有的虛擬網路，請省略下列範例中的尾端 `--vnet-peer-id $VNET_ID` 參數。

```bash
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --fqdn $FQDN --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --vnet-peer-id $VNET_ID
```

在幾分鐘後，`az openshift create`會 順利完成並傳回 JSON 回應，其中包含您的叢集詳細資料。

> [!NOTE]
> 如果您收到的錯誤表示主機名稱不可用，則可能是因為您的叢集名稱並非唯一。 嘗試刪除原始應用程式註冊，並使用不同的叢集名稱重新執行 [建立新的應用程式註冊] (howto-aad-app-configuration.md#create-a-new-app-registration) 中的步驟 (省略建立新使用者的最後一個步驟，因為您已經建立了一個)。

## <a name="step-3-sign-in-to-the-openshift-console"></a>步驟 3：登入 OpenShift 主控台

您現在即可登入新叢集的 OpenShift 主控台。 [OpenShift Web 主控台](https://docs.openshift.com/dedicated/architecture/infrastructure_components/web_console.html)可讓您以視覺化方式呈現、瀏覽及管理 OpenShift 專案的內容。

我們將以您為了測試建立的[新 Azure AD 使用者](howto-aad-app-configuration.md#create-a-new-active-directory-user)登入。 若要這麼做，您需要全新瀏覽器執行個體，該執行個體尚未快取您平常用來登入 Azure 入口網站的身分識別。

1. 開啟 *incognito* 視窗 (Chrome) 或 *InPrivate* 視窗 (Microsoft Edge)。
2. 瀏覽至您在[建立新的應用程式註冊](howto-aad-app-configuration.md#create-a-new-app-registration)步驟 6 中建立的登入 URL。 例如， https://constoso.eastus.cloudapp.azure.com

> [!NOTE]
> OpenShift 主控台會建立一個自我簽署憑證。
> 當瀏覽器中出現系統提示時，請略過警告並接受「不受信任」的憑證。

使用您在[建立新的 Active Directory 使用者](howto-aad-app-configuration.md#create-a-new-active-directory-user)中建立的使用者和密碼登入。當 [要求的權限] 對話方塊出現時，請選取 [代表組織同意] ，然後選取 [接受]。

您現在已登入叢集主控台。

[OpenShift 叢集主控台的螢幕擷取畫面](./media/aro-console.png)

 您可以深入了解如何[使用 OpenShift 主控台](https://docs.openshift.com/dedicated/getting_started/developers_console.html)，以建立 [Red Hat OpenShift](https://docs.openshift.com/dedicated/welcome/index.html) 文件中的映像。

## <a name="step-4-install-the-openshift-cli"></a>步驟 4：安裝 OpenShift CLI

[OpenShift CLI](https://docs.openshift.com/dedicated/cli_reference/get_started_cli.html) (或 OC 工具) 會提供用於管理應用程式和較低層級公用程式的命令，以便與 OpenShift 叢集的各種元件互動。

在 OpenShift 主控台中，按一下右上角您登入名稱旁邊的問號，然後選取 [命令列工具]。  請遵循 [最新版本] 連結來下載和安裝 Linux、 MacOS 或 Windows 支援的 oc CLI。

> [!NOTE]
> 如果看不到右上角的問號圖示，請從左上角下拉式清單中選取 [服務目錄] 或 [應用程式主控台]。
>
> 或者，您可以直接[下載 oc CLI](https://www.okd.io/download.html)。

[命令列工具] 頁面提供 `oc login https://<your cluster name>.<azure region>.cloudapp.azure.com --token=<token value>` 形式的命令。  按一下 [複製到剪貼簿] 按鈕來複製此命令。  在終端機視窗中，[設定您的路徑](https://docs.okd.io/latest/cli_reference/get_started_cli.html#installing-the-cli)以包含您本機安裝的 oc 工具。 然後使用您複製的 oc CLI 命令登入叢集。

如果您無法使用上述步驟取得權杖值，請從 `https://<your cluster name>.<azure region>.cloudapp.azure.com/oauth/token/request` 取得權杖值。

## <a name="next-steps"></a>後續步驟

在教學課程的這個部分中，您已了解如何：

> [!div class="checklist"]
> * 建立 Azure Red Hat OpenShift 叢集

前進到下一個教學課程：
> [!div class="nextstepaction"]
> [調整 Azure Red Hat OpenShift 叢集](tutorial-scale-cluster.md)