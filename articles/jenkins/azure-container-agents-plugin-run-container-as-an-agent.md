---
title: "建置專案，以在 Azure 中使用 Jenkins 和 Azure 容器執行個體"
description: "了解如何使用 Jenkins 的 Azure 容器代理程式 」 外掛程式來建立 Azure 容器執行個體與 Azure 中的專案"
services: multiple
documentationcenter: 
author: tomarcher
manager: rloutlaw
editor: 
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 12/11/2017
ms.author: tarcher
ms.custom: jenkins
ms.openlocfilehash: 269e936cb79ba4138285f5dbd326413d70d5924d
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/13/2017
---
# <a name="build-a-project-in-azure-using-jenkins-and-azure-container-instances"></a>建置專案，以在 Azure 中使用 Jenkins 和 Azure 容器執行個體

Azure 容器執行個體可讓您輕鬆地啟動且正在執行不需要重新佈建虛擬機器，或採用較高層級的服務。 Azure 容器執行個體提供每秒計費根據您需要; 的容量建立暫時性的工作負載，例如執行建置的理想選擇。

您會了解如何：
> [!div class="checklist"]
> * 安裝及設定 Azure 上的 Jenkins 伺服器
> * 安裝和設定 Azure 容器代理程式外掛程式 Jenkins
> * 使用 Azure 容器執行個體來建置[Spring PetClinic 範例應用程式](https://github.com/spring-projects/spring-petclinic)

## <a name="prerequisites"></a>必要條件

- **Azure 訂用帳戶**-若要深入了解 Azure 購買選項，請參閱[如何購買 Azure](https://azure.microsoft.com/pricing/purchase-options/)或[免費試用一個月](https://azure.microsoft.com/pricing/free-trial/)。

- **Azure CLI 2.0 或 Azure 雲端殼層**-安裝下列產品在其中輸入 Azure 命令的其中一個：

    - [Azure CLI 2.0](/cli/azure/install-azure-cli?view=azure-cli-latest) -可讓您從命令或終端機視窗中執行 Azure 的命令。
    - [Azure 雲端殼層](/azure/cloud-shell/quickstart.md)-瀏覽器架構殼層經驗。 Cloud Shell 以 Azure 管理工作為考量，提供以瀏覽器存取命令列的體驗。

## <a name="install-a-jenkins-server-on-azure-using-the-jenkins-marketplace-image"></a>使用 Jenkins Marketplace 映像在 Azure 上安裝 Jenkins 伺服器

Jenkins 支援的 Jenkins 伺服器委派工作是為了讓單一 Jenkins 安裝來裝載大量專案，或提供不同的環境所需的一個或多個代理程式的組建位置的模型，或測試。 本節中的步驟會引導您安裝和設定 Azure 上的 Jenkins 伺服器。

[!INCLUDE [jenkins-install-from-azure-marketplace-image](../../includes/jenkins-install-from-azure-marketplace-image.md)]

## <a name="connect-to-the-jenkins-server-running-on-azure"></a>連接到 Jenkins 伺服器在 Azure 上執行

一旦您已在 Azure 上安裝 Jenkins，您需要連接到 Jenkins。 下列步驟會引導您完成設定 SSH 連接到 Jenkins VM 在 Azure 上執行。 

[!INCLUDE [jenkins-connect-to-jenkins-server-running-on-azure](../../includes/jenkins-connect-to-jenkins-server-running-on-azure.md)]

## <a name="update-jenkins-dns"></a>更新 Jenkins DNS

Jenkins 需要知道自己的 URL，它會建立指向本身的連結時。 例如，需要 Jenkins 傳送電子郵件，其中包含直接的連結，來建立結果時，使用的 URL。 

本節會引導您設定 Jenkins URL。

1. 在瀏覽器中，瀏覽至 Jenkins 儀表板在`http://localhost:8080`。

1. 選取**管理 Jenkins**。

    ![管理 Jenkins Jenkins 儀表板中的選項](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-jenkins.png)

1. 選取**設定系統**。

    ![管理 Jenkins Jenkins 儀表板中的外掛程式選項](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-configure-system.png)

1. 在下**Jenkins 位置**，輸入 Jenkins 伺服器的 URL。

1. 選取 [ **儲存**]。

## <a name="update-jenkins-to-allow-java-network-launch-protocol-jnlp"></a>若要允許 Java 網路啟動通訊協定 (JNLP) 更新 Jenkins

Jenkins 代理程式連接與 Jenkins 伺服器透過 Java 網路啟動通訊協定 (JNLP)。 本節說明如何指定 JNLP 代理程式，以便與 Jenkins 伺服器通訊時使用的連接埠。

1. Jenkins 儀表板中選取**管理 Jenkins**。

    ![管理 Jenkins Jenkins 儀表板中的選項](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-jenkins.png)

1. 選取**設定全域安全性群組**。

    ![Jenkins 儀表板中設定全域安全性群組](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-configure-global-security.png)

1. 在下**代理程式**，選取**固定**，和輸入連接埠。 螢幕擷取畫面顯示 12345 連接埠值做為範例。 您應該指定適合您環境的連接埠。

    ![更新 Jenkins 全域安全性設定，以允許 JNLP](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-set-jnlp.png)

1. 選取 [ **儲存**]。

1. 使用 Azure CLI 2.0 或雲端殼層，請輸入下列命令以建立輸入您 Jenkins 網路安全性群組規則：

    ```azurecli
    az network nsg rule create  \
    --resource-group JenkinsResourceGroup \
    --nsg-name JenkinsNSG  \
    --name allow-https \
    --description "Allow access to port 12345 for HTTPS" \
    --access Allow \
    --protocol Tcp  \
    --direction Inbound \
    --priority 102 \
    --source-address-prefix "*"  \
    --source-port-range "*"  \
    --destination-address-prefix "*" \
    --destination-port-range "12345"
    ```

## <a name="create-and-add-an-azure-service-principal-to-the-jenkins-credentials"></a>建立並將 Azure 服務主體加入至 Jenkins 認證

您必須要有 Azure 服務主體，才能部署到 Azure。 下列步驟會引導您建立服務主體 （如果您還沒有其中一個），並更新與服務主體的 Jenkins 的程序。

1. 如果您已經有服務主體 （和知道其訂用帳戶 ID、 租用戶、 appId、 和密碼），您可以略過此步驟。 如果您需要建立安全性主體，請參閱文章[使用 Azure CLI 2.0 建立 Azure 服務主體](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)。 在建立主體時，請記下的值為訂用帳戶 ID、 租用戶、 appId、 和密碼。

1. 選取**認證**。

    ![管理 Jenkins 儀表板中的 [認證] 選項](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-credentials.png)

1. 在下**認證**，選取**系統**。

    ![管理系統 Jenkins 儀表板中的認證選項](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-credentials-system.png)

1. 選取**全域認證 （未限制）**。

    ![管理 Jenkins 儀表板中的全域系統認證選項](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-credentials-global.png)

1. 選取**新增某些認證**。

    ![將認證新增到 Jenkins 儀表板](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-adding-credentials.png)

1. 在**種類**下拉式清單中選取**Microsoft Azure 服務主體**，讓頁面以顯示特定欄位來加入服務主體。 然後，提供要求的值，如下所示：

    - **範圍**-選取的選項**Global (Jenkins 節點、 所有子項目，項目等。)**.
    - **訂用帳戶 ID** -使用您指定執行時的 Azure 訂用帳戶識別碼`az account set`。
    - **用戶端識別碼**-使用`appId`從傳回的值`az ad sp create-for-rbac`。
    - **用戶端密碼**-使用`password`從傳回的值`az ad sp create-for-rbac`。
    - **租用戶識別碼**-使用`tenant`從傳回的值`az ad sp create-for-rbac`。
    - **Azure 環境**-選取`Azure`。
    - **識別碼**-輸入`myTestSp`。 稍後在本教學課程會使用此值。
    - **描述**-（選擇性） 輸入的描述值，這個主體。

    ![Jenkins 儀表板中指定新的服務主體的內容](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-principal-properties.png)

1. 當您輸入定義主體的資訊之後時，您可以選擇性地選取**確認服務主體**以確保一切運作正常。 如果您的服務主體已正確定義，您看到訊息指出，「 已成功驗證 Microsoft Azure 服務主體。 」 下面**描述**欄位。

1. 當您完成時，選取**確定**即可將主體加入至 Jenkins。 Jenkins 儀表板上顯示新加入的主體**全域認證**頁面。

## <a name="create-an-azure-resource-group-for-your-azure-container-instances"></a>為您的 Azure 容器執行個體建立 Azure 資源群組

Azure 容器執行個體必須放在 Azure 資源群組。 Azure 資源群組是存放 Azure 方案相關資源的容器。

使用 Azure CLI 2.0 或雲端 Shell 中，輸入下列命令來建立資源群組，稱為`JenkinsAciResourceGroup`位置`eastus`:

```azurecli
az group create --name JenkinsAciResourceGroup --location eastus
```

完成後，請`az group create`命令會顯示類似下列的範例結果：

```JSON
{
  "id": "/subscriptions/<subscriptionId>/resourceGroups/JenkinsAciResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "JenkinsAciResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="install-the-azure-container-agents-plugin-for-jenkins"></a>安裝 Azure 容器代理程式外掛程式的 Jenkins

如果您已經安裝 Azure 容器代理程式外掛程式，您可以略過本節。

Jenkins 代理程式所建立的 Azure 資源群組之後，下列步驟說明如何安裝 Azure 容器代理程式外掛程式：

1. Jenkins 儀表板中選取**管理 Jenkins**。

    ![管理 Jenkins Jenkins 儀表板中的選項](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-jenkins.png)

1. 選取**管理外掛程式**。

    ![管理 Jenkins Jenkins 儀表板中的外掛程式選項](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-plugins.png)

1. 選取**可用**。

    ![Jenkins 儀表板中檢視可用的 Jenkins 外掛程式選項](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-view-available-plugins.png)

1. 輸入`Azure Container Agents`到**篩選**文字方塊。 （當您輸入的文字篩選器清單）。

    ![篩選可用的 Jenkins 外掛程式 Jenkins 儀表板中](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-filter-available-plugins.png)

1. 選取此核取方塊旁的  **Azure 容器代理程式**外掛程式，以及其中一個安裝選項。 為了此示範中，已經選取**不需重新啟動安裝**選項。

    ![安裝 Azure 容器代理程式外掛程式從 Jenkins 儀表板](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-install-aks-agent-plugin.png)

1.  選取要安裝所需的 plugin(s) 選項之後，Jenkins 儀表板會顯示頁面詳細說明您要安裝的狀態。

    ![安裝 Azure 容器代理程式外掛程式從 Jenkins 儀表板的安裝狀態](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-install-aks-agent-plugin-confirmation.png)

    若要傳回 Jenkins 儀表板的主頁面，選取**返回的最上層頁面**。

## <a name="configure-the-azure-container-agents-plugin"></a>設定 Azure 容器代理程式外掛程式

安裝 Azure 容器代理程式外掛程式之後，本節會引導您完成設定 Jenkins 儀表板中的外掛程式。

1. Jenkins 儀表板中選取**管理 Jenkins**。

    ![管理 Jenkins Jenkins 儀表板中的選項](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-jenkins.png)

1. 選取**設定系統**。

    ![管理 Jenkins Jenkins 儀表板中的外掛程式選項](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-configure-system.png)

1. 找出**雲端**底部的區段頁面上，且從**加入新的雲端**下拉式清單中選取**Azure 容器執行個體**。

    ![加入新的雲端提供者從 Jenkins 儀表板](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-cloud-provider.png)

1. 在**Azure 容器執行個體**區段中，指定下列值：

    - **雲端名稱**-（選擇性的這個值預設為自動產生的名稱。）指定此執行個體的名稱。 
    - **Azure 認證**-選取下拉式箭號，然後選取`myTestSp`識別 Azure 服務主體的項目先前所建立。
    - **資源群組**-選取下拉式箭號，然後選取`JenkinsAciResourceGroup`識別 Azure 容器執行個體的資源群組的項目先前所建立。

    ![定義 Azure 容器執行個體屬性](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-aci-properties.png)

1. 選取**新增容器範本**下拉箭頭，然後選取**Aci 容器範本**。

1. 在**Aci 容器範本**區段中，指定下列值：

    - **名稱**-輸入`ACI-container`。
    - **標籤**-輸入`ACI-container`。
    - **Docker 映像**-輸入`cloudbees/jnlp-slave-with-java-build-tools`

    ![定義 Azure 容器執行個體的映像內容](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-aci-image-properties.png)

1. 選取 [進階]。

1. 選取**保留策略**下拉箭頭，然後選取**容器閒置保留策略**。 選取此選項，Jenkins 會保留代理程式，直到沒有新的工作在代理程式上執行，而且已經經過指定的閒置時間。

    ![定義 Azure 容器執行個體保留策略](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-aci-retention-strategy.png)

1. 選取 [ **儲存**]。

## <a name="create-the-spring-petclinic-application-job-in-jenkins"></a>建立可在 Jenkins Spring PetClinic 應用程式作業

下列步驟會引導您完成建立 Jenkins 作業-freestyle 專案的建置 Spring PetClinic 應用程式的方式。

1. Jenkins 儀表板中選取**新項目**。

    ![Jenkins 儀表板中的新項目功能表選項](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-item.png)

1. 輸入`myPetClinicProject`項目名稱，然後選取**Freestyle 專案**。

    ![Jenkins 儀表板中的新 freestyle 專案](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-freestyle-project.png)

1. 選取 [確定] 。

1. 選取**一般**索引標籤，然後指定下列值：

    - **限制可以執行專案**-選取此選項。
    - **標記運算式**-輸入`ACI-container`。 當您結束欄位時，會顯示訊息，確認標籤由上一個步驟中建立的雲端組態。

    ![新的 freestyle 專案 Jenkins 儀表板中的一般設定](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-freestyle-project-general.png)

1. 選取**原始程式碼管理**索引標籤，然後指定下列值：

    - **來源的程式碼管理**-選取**Git**。
    - **儲存機制 URL** -Spring PetClinic 範例應用程式 GitHub 儲存機制中輸入下列 URL: `https://github.com/spring-projects/spring-petclinic.git`。

1. 選取**建置**索引標籤，然後執行下列工作：

    - 選取**加入建置步驟**下拉箭頭，然後選取**叫用最上層的 Maven 目標**。

    - 如**目標**，輸入`package`。

1. 選取**儲存**以儲存新專案的定義。

## <a name="build-the-spring-petclinic-application-job-in-jenkins"></a>建置在 Jenkins Spring PetClinic 應用程式工作

就可以建置您的專案 ！ 本節說明如何從 Jenkins 儀表板建置專案。

1. Jenkins 儀表板中選取`myPetClinicProject`。

    ![選取要從 Jenkins 儀表板中建置專案。](./media/azure-container-agents-plugin-run-container-as-an-agent/select-project-to-build.png)

1. 選取**現在就建置**。 

    ![建置專案，從 Jenkins 儀表板。](./media/azure-container-agents-plugin-run-container-as-an-agent/build-project.png)

1. 當您在 Jenkins 啟動建置時，組建已排入佇列。 Azure 容器代理程式時，如果組建無法執行直到啟動 Azure 容器代理程式並回到線上。 之前，您會看到訊息指出代理程式名稱和建置，正在擱置中的事實。 （此程序會花費大約五分鐘，但時，才需要您使用組建代理程式第一次。 後續建置會更快，因為代理程式在該點在線上。）

    ![組建已排入佇列，直到建立代理程式並回到線上。](./media/azure-container-agents-plugin-run-container-as-an-agent/build-pending.png)

    在建置開始時，一旦理髮師柵欄進度列表示組建是否正在執行：

    ![一旦您看到理髮師柵欄進度列，組建正在執行。](./media/azure-container-agents-plugin-run-container-as-an-agent/build-running.png)

1. 當理髮師柵欄進度列消失時，選取的組建編號旁的箭號。 從內容功能表中，選取**主控台輸出**。

    ![按一下主控台記錄檔 功能表項目，以檢視組建資訊。](./media/azure-container-agents-plugin-run-container-as-an-agent/build-console-log-menu.png)

1. 主控台記錄資訊從建置流程所發出。 若要檢視 （包括您建立 Azure 的代理程式上從遠端執行的組建資訊） 的所有組建資訊，請選取**完整記錄**。

    ![按一下以檢視組建的詳細的資訊的完整記錄連結。](./media/azure-container-agents-plugin-run-container-as-an-agent/build-console-log.png)

    完整的記錄檔中顯示更多詳細資料的組建資訊：

    ![完整的記錄檔中顯示更多詳細資料的組建資訊。](./media/azure-container-agents-plugin-run-container-as-an-agent/build-console-log-full.png)
    
1. 若要檢視組建的配置，捲動到底部的記錄檔。

    ![建置配置在底部顯示組建記錄檔。](./media/azure-container-agents-plugin-run-container-as-an-agent/build-disposition.png)

## <a name="clean-up-azure-resources"></a>清除 Azure 資源

在此教學課程中，您可以建立包含兩個 Azure 資源群組內的資源： 
    - `JenkinsResourceGroup`-包含 Jenkins 伺服器的 Azure 資源。
    - `JenkinsAciResourceGroup`-包含 Jenkins 代理程式的 Azure 資源。
    
如果您不再需要的任何資源使用 Azure 資源群組中，您可以刪除資源群組使用`az group delete`命令，如下所示 (取代&lt;resourceGroup > 您想要的資源群組名稱預留位置delete):

```azurecli
az group delete -n <resourceGroup>
```

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [若要查看最新的文件和範例的 Azure 中樞上造訪 Jenkins](https://docs.microsoft.com/en-us/azure/jenkins/)