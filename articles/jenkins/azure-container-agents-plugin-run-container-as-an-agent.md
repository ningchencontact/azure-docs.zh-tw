---
title: 使用 Jenkins 和 Azure 容器執行個體在 Azure 中建置專案
description: 了解如何使用適用於 Jenkins 的 Azure 容器代理程式外掛程式，在 Azure 中搭配 Azure 容器執行個體建置專案
author: tomarcher
manager: rloutlaw
ms.service: multiple
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: tarcher
ms.custom: Jenkins
ms.openlocfilehash: a18136afc096f8315310fae134d3f683994b6d0a
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/17/2018
---
# <a name="build-a-project-in-azure-using-jenkins-and-azure-container-instances"></a>使用 Jenkins 和 Azure 容器執行個體在 Azure 中建置專案

Azure 容器執行個體可讓您快速啟動及執行，不需要佈建虛擬機器或採用高階服務即可完成。 Azure 容器執行個體會根據您需要的容量提供以秒計費，對於暫時性工作負載 (例如執行建置) 是非常適合的選項。

您會了解如何：
> [!div class="checklist"]
> * 在 Azure 上安裝及設定 Jenkins 伺服器
> * 安裝及設定適用於 Jenkins 的 Azure 容器代理程式外掛程式
> * 使用 Azure 容器執行個體來建置 [Spring PetClinic 範例應用程式](https://github.com/spring-projects/spring-petclinic) \(英文\)

## <a name="prerequisites"></a>先決條件

- **Azure 訂用帳戶**：若要深入了解 Azure 購買選項，請參閱[如何購買 Azure](https://azure.microsoft.com/pricing/purchase-options/) 或[免費試用一個月](https://azure.microsoft.com/pricing/free-trial/)。

- **Azure CLI 2.0 或 Azure Cloud Shell**：安裝下列其中一項產品以輸入 Azure 命令：

    - [Azure CLI 2.0](/cli/azure/install-azure-cli?view=azure-cli-latest)：可讓您從命令視窗或終端機視窗中執行 Azure 命令。
    - [Azure Cloud Shell](/azure/cloud-shell/quickstart.md)：以瀏覽器為基礎的殼層體驗。 Cloud Shell 以 Azure 管理工作為考量，提供以瀏覽器存取命令列的體驗。

## <a name="install-a-jenkins-server-on-azure-using-the-jenkins-marketplace-image"></a>使用 Jenkins Marketplace 映像在 Azure 上安裝 Jenkins 伺服器

Jenkins 支援由 Jenkins 伺服器將工作委派給一或多個代理程式的模型，這可讓單一 Jenkins 安裝裝載大量的專案，或是針對建置或測試需求提供不同的環境。 本節中的步驟會引導您在 Azure 上安裝和設定 Jenkins 伺服器。

[!INCLUDE [jenkins-install-from-azure-marketplace-image](../../includes/jenkins-install-from-azure-marketplace-image.md)]

## <a name="connect-to-the-jenkins-server-running-on-azure"></a>連線到在 Azure 上執行的 Jenkins 伺服器

當您在 Azure 上安裝 Jenkins 之後，您必須連線到 Jenkins。 下列步驟將會逐步引導您針對在 Azure 上執行的 Jenkins VM 設定 SSH 連線。 

[!INCLUDE [jenkins-connect-to-jenkins-server-running-on-azure](../../includes/jenkins-connect-to-jenkins-server-running-on-azure.md)]

## <a name="update-jenkins-dns"></a>更新 Jenkins DNS

當 Jenkins 要建立指向自己本身的連結時，它必須知道其專屬的 URL。 例如，當 Jenkins 傳送包含建置結果直接連結的電子郵件時，就必須使用該 URL。 

本節將逐步引導您設定 Jenkins URL。

1. 在瀏覽器中，瀏覽至 Jenkins 儀表板 (位於 `http://localhost:8080`)。

1. 選取 [Manage Jenkins] \(管理 Jenkins\)。

    ![Jenkins 儀表板中的 [Manage Jenkins] \(管理 Jenkins\) 選項](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-jenkins.png)

1. 選取 [Configure System] \(設定系統\)。

    ![Jenkins 儀表板中的 [Configure System] \(設定系統\) 外掛程式選項](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-configure-system.png)

1. 在 [Jenkins Location] \(Jenkins 位置\) 底下，輸入您 Jenkins 伺服器的 URL。

1. 選取 [ **儲存**]。

## <a name="update-jenkins-to-allow-java-network-launch-protocol-jnlp"></a>更新 Jenkins 以允許 Java Network Launch Protocol (JNLP)

> [!NOTE]
> 本教學課程說明如何設定 JNLP 代理程式。 從 0.4.x 版開始，[Jenkins Azure Container 代理程式外掛程式](https://plugins.jenkins.io/azure-container-agents)也支援 SSH。
>
>

Jenkins 代理程式是透過 Java Network Launch Protocol (JNLP) 與 Jenkins 伺服器連線。 本節說明如何指定 JNLP 代理程式與 Jenkins 伺服器通訊時所要使用的連接埠。

1. 在 Jenkins 儀表板中，選取 [Manage Jenkins] \(管理 Jenkins\)。

    ![Jenkins 儀表板中的 [Manage Jenkins] \(管理 Jenkins\) 選項](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-jenkins.png)

1. 選取 [Configure Global Security] \(設定全域安全性\)。

    ![Jenkins 儀表板中的 [Configure global security] \(設定全域安全性\)](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-configure-global-security.png)

1. 在 [Agents] \(代理程式\) 底下，選取 [Fixed] \(固定\)，並輸入連接埠。 螢幕擷取畫面顯示連接埠值 12345 作為範例。 您應該指定適合您環境的連接埠。

    ![更新 Jenkins 全域安全性設定以允許 JNLP](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-set-jnlp.png)

1. 選取 [ **儲存**]。

1. 使用 Azure CLI 2.0 或 Cloud Shell，輸入下列命令以建立適用於 Jenkins 網路安全性群組的輸入規則：

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

## <a name="create-and-add-an-azure-service-principal-to-the-jenkins-credentials"></a>建立 Azure 服務主體並新增至 Jenkins 認證

您必須要有 Azure 服務主體，才能部署到 Azure。 下列步驟將引導您完成建立服務主體 (如果您還沒有服務主體) 並透過服務主體更新 Jenkins 的程序。

1. 如果您已經有服務主體 (且知道其訂用帳戶識別碼、租用戶、應用程式識別碼及密碼)，則可以略過此步驟。 如果您需要建立安全性主體，請參閱[使用 Azure CLI 2.0 來建立 Azure 服務主體](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)一文。 建立主體時，請記下訂用帳戶識別碼、租用戶、應用程式識別碼及密碼的值。

1. 選取 [Credentials] \(認證\)。

    ![Jenkins 儀表板中的 [Manage credentials] \(管理認證\) 選項](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-credentials.png)

1. 在 [Credentials] \(認證\) 底下，選取 [System] \(系統\)。

    ![Jenkins 儀表板中的 [Manage system credentials] \(管理系統認證\) 選項](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-credentials-system.png)

1. 選取 [Global credentials (unrestricted)] \(全域認證 (不受限)\)。

    ![Jenkins 儀表板中的 [Manage global system credentials] \(管理全域系統認證\) 選項](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-credentials-global.png)

1. 選取 [Adding some credentials] \(新增某些認證)\。

    ![Jenkins 儀表板中的 [Add credentials] \(新增認證\)](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-adding-credentials.png)

1. 在 [Kind] \(種類\) 下拉式清單中，選取 [Microsoft Azure Service Principal] \(Microsoft Azure 服務主體\)，讓頁面顯示用來新增服務主體的特定欄位。 然後，依下列方式提供要求的值：

    - **Scope** (範圍)：選取 [Global (Jenkins, nodes, items, all child items, etc.)] \(全域 (Jenkins、節點、項目、所有子項目等)\)。
    - **Subscription ID** (訂用帳戶識別碼)：使用您在執行 `az account set` 時指定的 Azure 訂用帳戶識別碼。
    - **Client ID** (用戶端識別碼)：使用 `az ad sp create-for-rbac` 傳回的 `appId` 值。
    - **Client Secret** (用戶端密碼)：使用 `az ad sp create-for-rbac` 傳回的 `password` 值。
    - **Tenant ID** (租用戶識別碼)：使用 `az ad sp create-for-rbac` 傳回的 `tenant` 值。
    - **Azure Environment** (Azure 環境)：選取 `Azure`。
    - **ID** (識別碼)：輸入 `myTestSp`。 本教學課程稍後會再使用此值。
    - **Description** (描述)：(選擇性) 輸入此主體的描述值。

    ![在 Jenkins 儀表板中指定新服務主體屬性](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-principal-properties.png)

1. 當您輸入定義主體的資訊之後，可以選擇性地選取 [Verify Service Principal] \(驗證服務主體\) 以確保一切運作正常。 如果已正確定義服務主體，您會看見訊息 "Successfully verified the Microsoft Azure Service Principal" (已成功驗證 Microsoft Azure 服務主體)。 顯示在 [Description] \(描述\) 欄位底下。

1. 當您完成後，選取 [OK] \(確定\) 以將主體新增至 Jenkins。 Jenkins 儀表板的 [Global Credentials] \(全域認證\) 頁面上會顯示新加入的主體。

## <a name="create-an-azure-resource-group-for-your-azure-container-instances"></a>為您的 Azure 容器執行個體建立 Azure 資源群組

Azure 容器執行個體必須放置在 Azure 資源群組中。 Azure 資源群組是存放 Azure 方案相關資源的容器。

使用 Azure CLI 2.0 或 Cloud Shell 輸入下列命令，以在位置 `eastus` 建立稱為 `JenkinsAciResourceGroup` 的資源群組：

```azurecli
az group create --name JenkinsAciResourceGroup --location eastus
```

完成時，`az group create` 命令會顯示類似下列範例的結果：

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

## <a name="install-the-azure-container-agents-plugin-for-jenkins"></a>安裝適用於 Jenkins 的 Azure 容器代理程式外掛程式

如果您已經安裝 Azure 容器代理程式外掛程式，則可以略過本節。

當您為 Jenkins 代理程式建立 Azure 資源群組之後，下列步驟將說明如何安裝 Azure 容器代理程式外掛程式：

1. 在 Jenkins 儀表板中，選取 [Manage Jenkins] \(管理 Jenkins\)。

    ![Jenkins 儀表板中的 [Manage Jenkins] \(管理 Jenkins\) 選項](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-jenkins.png)

1. 選取 [Manage Plugins] \(管理外掛程式\)。

    ![Jenkins 儀表板中的 [Configure System] \(設定系統\) 外掛程式選項](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-plugins.png)

1. 選取 [Available] \(可用\)。

    ![在 Jenkins 儀表板中檢視可用的 Jenkins 外掛程式選項](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-view-available-plugins.png)

1. 將 `Azure Container Agents` 輸入至 [Filter] \(篩選\) 文字方塊。 (清單會隨著您輸入文字而進行篩選)。

    ![在 Jenkins 儀表板中篩選可用的 Jenkins 外掛程式](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-filter-available-plugins.png)

1. 選取 [Azure Container Agents] \(Azure 容器代理程式\) 外掛程式旁的核取方塊，以及其中一個安裝選項。 基於此示範的目的，選取 [Install without restart] \(安裝但不重新啟動\) 選項。

    ![從 Jenkins 儀表板安裝 Azure 容器代理程式外掛程式](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-install-aks-agent-plugin.png)

1.  選取安裝所需外掛程式的選項之後，Jenkins 儀表板會顯示詳細說明要安裝項目之狀態的頁面。

    ![從 Jenkins 儀表板安裝 Azure 容器代理程式外掛程式的安裝狀態](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-install-aks-agent-plugin-confirmation.png)

    若要返回 Jenkins 儀表板的主頁面，請選取 [Go back to the top page] \(返回最上層頁面\)。

## <a name="configure-the-azure-container-agents-plugin"></a>設定 Azure 容器代理程式外掛程式

安裝 Azure 容器代理程式外掛程式之後，本節將引導您在 Jenkins 儀表板內設定外掛程式。

1. 在 Jenkins 儀表板中，選取 [Manage Jenkins] \(管理 Jenkins\)。

    ![Jenkins 儀表板中的 [Manage Jenkins] \(管理 Jenkins\) 選項](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-jenkins.png)

1. 選取 [Configure System] \(設定系統\)。

    ![Jenkins 儀表板中的 [Configure System] \(設定系統\) 外掛程式選項](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-configure-system.png)

1. 找出位於頁面底部的 [Cloud] \(雲端\) 區段，然後在 [Add a new cloud] \(新增新的雲端\) 下拉式清單中，選取 [Azure Container Instance] \(Azure 容器執行個體\)。

    ![從 Jenkins 儀表板新增新的雲端提供者](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-cloud-provider.png)

1. 在 [Azure Container Instance] \(Azure 容器執行個體\) 區段中，指定下列值：

    - **Cloud name** (雲端名稱)：(選擇性，因為此值預設為自動產生的名稱)。指定此執行個體的名稱。 
    - **Azure Credential** (Azure 認證)：選取下拉箭頭，然後選取能識別出您稍早建立之 Azure 服務主體的 `myTestSp` 項目。
    - **Resource Group** (資源群組)：選取下拉箭頭，然後選取能識別出您稍早建立之 Azure 容器執行個體資源群組的 `JenkinsAciResourceGroup` 項目。

    ![定義 Azure 容器執行個體屬性](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-aci-properties.png)

1. 選取 [Add Container Template] \(新增容器範本\) 下拉箭頭，然後選取 [Aci Container Template] \(Aci 容器範本\)。

1. 在 [Aci Container Template] \(Aci 容器範本\) 區段中，指定下列值：

    - **Name** (名稱)：輸入 `ACI-container`。
    - **Labels** (標籤)：輸入 `ACI-container`。
    - **Docker Image** (Docker 映像)：輸入 `cloudbees/jnlp-slave-with-java-build-tools`

    ![定義 Azure 容器執行個體映像屬性](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-aci-image-properties.png)

1. 選取 [進階]。

1. 選取 [Retention Strategy] \(保留期策略\) 下拉箭頭，並選取 [Container Idle Retention Strategy] \(容器閒置保留期策略\)。 透過選取此選項，Jenkins 會使代理程式保持運作，直到沒有新作業在代理程式上執行，且已經過指定的閒置時間為止。

    ![定義 Azure 容器執行個體保留期策略](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-aci-retention-strategy.png)

1. 選取 [ **儲存**]。

## <a name="create-the-spring-petclinic-application-job-in-jenkins"></a>在 Jenkins 中建立 Spring PetClinic 應用程式作業

下列步驟將引導您透過 (以自由式專案的形式) 建立 Jenkins 作業，來建置 Spring PetClinic 應用程式。

1. 在 Jenkins 儀表板中，選取 [New Item] \(新增項目\)。

    ![Jenkins 儀表板中的 [New item] \(新增項目\) 功能表選項](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-item.png)

1. 輸入 `myPetClinicProject` 作為項目名稱，並選取 [Freestyle Project] \(自由式專案\)。

    ![在 Jenkins 儀表板中新增 [Freestyle project] \(自由式專案\)](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-freestyle-project.png)

1. 選取 [確定] 。

1. 選取 [General] \(一般\) 索引標籤，並指定下列值：

    - **Restrict where project can be run** (限制可執行專案的位置)：選取此選項。
    - **Label Expression** (標籤運算式)：輸入 `ACI-container`。 當您離開欄位時，系統會顯示一則訊息，確認標籤是由在上個步驟中所建立的雲端設定所提供。

    ![Jenkins 儀表板中新自由式專案的 [General] \(一般\) 設定](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-freestyle-project-general.png)

1. 選取 [Source Code Management] \(原始程式碼管理\) 索引標籤，並指定下列值：

    - **Source Code Management** (原始程式碼管理)：選取 [Git]。
    - **Repository URL** (存放庫 URL)：輸入下列適用於 Spring PetClinic 範例應用程式 GitHub 存放庫的 URL：`https://github.com/spring-projects/spring-petclinic.git`。

1. 選取 [Build] \(建置\) 索引標籤，並執行下列工作：

    - 選取 [Add build step] \(新增建置步驟\) 下拉箭頭，然後選取 [Invoke top-level Maven targets] \(叫用最上層 Maven 目標\)。

    - 針對 [Goals] \(目標\)，輸入 `package`。

1. 選取 [Save] \(儲存\) 以儲存新的專案定義。

## <a name="build-the-spring-petclinic-application-job-in-jenkins"></a>在 Jenkins 中建置 Spring PetClinic 應用程式作業

是時候建置專案了！ 本節說明如何從 Jenkins 儀表板建置專案。

1. 在 Jenkins 儀表板中，選取 `myPetClinicProject`。

    ![從 Jenkins 儀表板選取要建置的專案。](./media/azure-container-agents-plugin-run-container-as-an-agent/select-project-to-build.png)

1. 選取 [Build now] \(立即建置\)。 

    ![從 Jenkins 儀表板建置專案。](./media/azure-container-agents-plugin-run-container-as-an-agent/build-project.png)

1. 當您在 Jenkins 中開始某個組建時，該組建將會排入佇列。 針對 Azure 容器代理程式的情況，在 Azure 容器代理程式啟動且上線之前，系統將不會執行該組建。 在那之前，您會看到指出代理程式名稱，以及組建正處於擱置中的訊息。 (此程序會花費大約五分鐘，但只有在您首次針對組建使用代理程式時才會發生。 後續組建將會較快，因為代理程式那時將已經上線)。

    ![在代理程式已建立且已上線後，才會將組建排入佇列。](./media/azure-container-agents-plugin-run-container-as-an-agent/build-pending.png)

    組建開始之後，理髮店招牌進度列會指出正在執行組建：

    ![看到理髮店招牌進度列便代表組建正在執行。](./media/azure-container-agents-plugin-run-container-as-an-agent/build-running.png)

1. 當理髮店招牌進度列消失時，選取組建編號旁的箭號。 從操作功能表中，選取 [Console output] \(主控台輸出\)。

    ![按一下 [Console Log] \(主控台記錄\) 功能表項目以檢視組建資訊。](./media/azure-container-agents-plugin-run-container-as-an-agent/build-console-log-menu.png)

1. 主控台記錄資訊是從建置程序發出。 若要檢視所有的組建資訊 (包括從您所建立的 Azure 代理程式上遠端執行之組建的相關資訊)，請選取 [Full log] \(完整記錄\)。

    ![按一下 [Full log] \(完整記錄\) 連結以檢視詳細的組建資訊。](./media/azure-container-agents-plugin-run-container-as-an-agent/build-console-log.png)

    完整記錄會顯示更詳細的組建資訊：

    ![完整記錄顯示更詳細的建置資訊。](./media/azure-container-agents-plugin-run-container-as-an-agent/build-console-log-full.png)
    
1. 若要檢視組建的配置，請捲動至記錄底部。

    ![顯示在建置記錄底部的組建配置。](./media/azure-container-agents-plugin-run-container-as-an-agent/build-disposition.png)

## <a name="clean-up-azure-resources"></a>清除 Azure 資源

在本教學課程中，您已建立包含在兩個 Azure 資源群組內的資源： 
    - `JenkinsResourceGroup`：包含適用於 Jenkins 伺服器的 Azure 資源。
    - `JenkinsAciResourceGroup`：包含適用於 Jenkins 代理程式的 Azure 資源。
    
如果您不再需要使用 Azure 資源群組中的任何資源，則可以如下所示使用 `az group delete` 命令來刪除資源群組 (請將 &lt;resourceGroup> 預留位置取代為您要刪除的資源群組名稱)：

```azurecli
az group delete -n <resourceGroup>
```

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [瀏覽 Azure 中樞上的 Jenkins 以查看最新文章及範例](https://docs.microsoft.com/azure/jenkins/)