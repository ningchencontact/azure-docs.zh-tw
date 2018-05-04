---
title: 使用 Azure 容器執行個體作為 Jenkins 建置代理程式
description: 了解如何使用 Azure 容器執行個體作為 Jenkins 建置代理程式。
services: container-instances
author: neilpeterson
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 04/20/2018
ms.author: nepeters
ms.openlocfilehash: dbe418db8fb3d73739a30b60703f15b3dc47b291
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2018
---
# <a name="use-azure-container-instances-as-a-jenkins-build-agent"></a>使用 Azure 容器執行個體作為 Jenkins 建置代理程式

Azure 容器執行個體可提供隨選高載的隔離環境，以便執行容器化工作負載。 Azure 容器執行個體因為這些特性，而成為大規模執行 Jenkins 建置作業的絕佳平台。 本文件逐步說明如何部署和使用透過 ACI 預先設定為建置目標的 Jenkins 伺服器。

如需 Azure 容器執行個體的詳細資訊，請參閱[關於 Azure 容器執行個體][about-aci]。

## <a name="deploy-jenkins-server"></a>部署 Jenkins 伺服器

在 Azure 入口網站中，選取 [建立資源] 並搜尋 [Jenkins]。 選取發行者為 **Microsoft** 的 Jenkins 供應項目，然後選取 [建立]。

在 [基本資料] 表單上輸入下列資訊，然後在完成時按一下 [確定]。

- **名稱** - Jenkins 部署的名稱。
- **使用者名稱** - 此使用者名稱作為 Jenkins 虛擬機器的管理員使用者。
- **驗證類型** - 建議使用 SSH 公開金鑰。 若已選取，請複製貼入在登入 Jenkins 虛擬機器時所要使用的 SSH 公開金鑰。
- **訂用帳戶** - 選取 Azure 訂用帳戶。
- **資源群組** - 建立新的資源群組或選取現有的資源群組。
- **位置** - 選取 Jenkins 伺服器的位置。

![Jenkins 入口網站部署基本設定](./media/container-instances-jenkins/jenkins-portal-01.png)

在 [其他設定] 表單上，完成下列項目：

- **大小** - 為您的 Jenkins 虛擬機器選取適當大小選項。
- **VM 磁碟類型** - 針對 Jenkins 指定 HDD (硬碟) 或 SSD (固態硬碟)。
- **虛擬網路** - (選擇性) 選取 [虛擬網路] 以修改預設設定。
- **子網路** - 選取 [子網路]、確認資訊，然後選取 [確定]。
- **公用 IP 位址** - 選取 [公用 IP 位址] 可讓您提供自訂名稱、設定 SKU 和指派方法。
- **網域名稱標籤** - 指定一個值，為 Jenkins 虛擬機器建立完整指定的 URL 值。
- **Jenkins 版本類型** - 從選項中選取所需的版本類型：[LTS]、[每週建置] 或 [Azure 已驗證]。

![Jenkins 入口網站部署額外設定](./media/container-instances-jenkins/jenkins-portal-02.png)

針對 [服務主體整合]，選取 [自動 (MSI)]，讓 [Azure 受控服務識別][managed-service-identity] 為 Jenkins 執行個體自動建立驗證身分識別。 選取 [手動] 以提供您自己的服務主體認證。

雲端代理程式可為 Jenkins 建置作業設定雲端式平台。 基於本文件的緣故，請選取 ACI。 利用 ACI 雲端代理程式，每個 Jenkins 建置作業都是在 Azure 容器執行個體中執行。

![Jenkins 入口網站部署雲端整合設定](./media/container-instances-jenkins/jenkins-portal-03.png)

完成整合設定之後，請按一下 [確定]，然後按一下驗證摘要上的 [確定]。 按一下 [使用規定] 摘要上的 [建立]。 部署 Jenkins 伺服器需要幾分鐘的時間。

## <a name="configure-jenkins"></a>設定 Jenkins

在 Azure 入口網站中，瀏覽至 Jenkins 資源群組、選取 Jenkins 虛擬機器，並且記下 DNS 名稱。

![Jenkins 登入指示](./media/container-instances-jenkins/jenkins-portal-fqdn.png)

瀏覽至 Jenkins VM 的 DNS 名稱並複製所傳回的 SSH 字串。

![Jenkins 登入指示](./media/container-instances-jenkins/jenkins-portal-04.png)

在您的開發系統上開啟終端機工作階段，然後貼入上一個步驟中的 SSH 字串。 將 'username' 更新為在部署 Jenkins 伺服器時指定的使用者名稱。

一旦連線，請執行下列命令來擷取初始管理員密碼。

```
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

讓 SSH 工作階段和通道保持執行狀態，並且在瀏覽器中巡覽至 http://localhost:8080。 將初始管理員密碼貼到欄位中，如下列影像所示。 完成時選取 [繼續]。

![將 Jenkins 解除鎖定](./media/container-instances-jenkins/jenkins-portal-05.png)

選取 [安裝建議的外掛程式] 以安裝所有建議的 Jenkins 外掛程式。

![安裝 Jenkins 外掛程式](./media/container-instances-jenkins/jenkins-portal-06.png)

建立新的管理員使用者帳戶。 此帳戶用來登入和使用您的 Jenkins 執行個體。

![建立 Jenkins 使用者](./media/container-instances-jenkins/jenkins-portal-07.png)

完成時選取 [儲存並完成]，然後選取 [開始使用 Jenkins] 來完成設定。

Jenkins 現在已設定並準備要建置和部署程式碼。 在此範例中，簡單的 Java 應用程式用來示範在 Azure 容器執行個體上建置 Jenkins。

## <a name="create-build-job"></a>建立建置作業

使用容器映像作為 Jenkins 建置目標時，您需要指定一個映像，其中包含成功建置所需的全部工具。 若要指定映像，請選取 [管理 Jenkins] > [設定系統] 並向下捲動至 [雲端] 區段。 在此範例中，將 Docker 映像值更新為 `microsoft/java-on-azure-jenkins-slave`。

完成後，按一下 [儲存] 以返回 Jenkins 儀表板。

![Jenkins 雲端組態](./media/container-instances-jenkins/jenkins-aci-image.png)

立即建立 Jenkins 建置作業。 選取 [新增項目]，提供組件專案名稱 (例如 `aci-java-demo`)，選取 [Freestyle 專案]，然後按一下 [確定]。

![建立 Jenkins 作業](./media/container-instances-jenkins/jenkins-new-job.png)

在 [一般] 之下，確定已選取 [限制可以執行這個專案的位置]。 針對 [標籤運算式] 輸入 `linux`。 此組態可確保在 ACI 雲端上執行此建置作業。

![建立 Jenkins 作業](./media/container-instances-jenkins/jenkins-job-01.png)

在 [原始程式碼管理] 之下，選取 `Git` 並輸入 `https://github.com/spring-projects/spring-petclinic.git` 作為存放庫 URL。 此 GitHub 存放庫包含範例應用程式程式碼。

![將原始程式碼新增至 Jenkins 作業](./media/container-instances-jenkins/jenkins-job-02.png)

在 [建置] 之下，選取 [新增建置步驟] 並選取 `Invoke top-level Maven targets`。 輸入 `package` 作為建置步驟目標。

![新增 Jenkins 建置步驟](./media/container-instances-jenkins/jenkins-job-03.png)

完成時選取 [儲存]  。

## <a name="run-the-build-job"></a>執行建置作業

若要測試建置作業並將 Azure 容器執行個體當作建置平台觀測，請以手動方式開始建置。

選取 [立即建置] 以開始建置作業。 作業需要幾分鐘的時間才能啟動，在執行時，您應會看到類似下列影像的狀態。

![新增 Jenkins 建置步驟](./media/container-instances-jenkins/jenkins-job-status.png)

在執行作業時，開啟 Azure 入口網站並查看 Jenkins 資源群組。 您應會看到已建立一個 Azure 容器執行個體。 Jenkins 作業正在這個執行個體內部執行。

![在 ACI 中建置 Jenkins](./media/container-instances-jenkins/jenkins-aci.png)

由於 Jenkins 執行的作業數目超過已設定的 Jenkins 執行程式數目 (預設值為 2)，所以會建立多個 Azure 容器執行個體。

![在 ACI 中建置 Jenkins](./media/container-instances-jenkins/jenkins-aci-multi.png)

完成所有建置作業後，就會移除 Azure 容器執行個體。

![在 ACI 中建置 Jenkins](./media/container-instances-jenkins/jenkins-aci-none.png)

## <a name="next-steps"></a>後續步驟

若要深入了解 Azure 上的 Jenkins，請參閱 [Azure 與 Jenkins][jenkins-azure]。

<!-- LINKS - internal -->
[about-aci]: ./container-instances-overview.md
[jenkins-azure]: ../jenkins/overview.md
[managed-service-identity]: ../active-directory/managed-service-identity/overview.md