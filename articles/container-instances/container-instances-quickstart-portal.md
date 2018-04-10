---
title: 快速入門 - 使用 Azure 入口網站建立您的第一個 Azure Container Instances 容器
description: 在本快速入門中，您會使用 Azure 入口網站在 Azure Container Instances 中部署容器
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: quickstart
ms.date: 04/02/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: cb0c8c5f5730ae1f7a2e9b38c3ef3e04ee8cde67
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2018
---
# <a name="quickstart-create-your-first-container-in-azure-container-instances"></a>快速入門：在 Azure Container Instances 中建立您的第一個容器

Azure Container Instances 可讓您輕鬆地在 Azure 中建立和管理容器。 在本快速入門中，您會在 Azure 中建立容器，並使用公用 IP 位址向網際網路公開此容器。在本快速入門中，您會在 Azure 中建立容器，並使用公用 IP 位址向網際網路公開此容器。 這項作業是使用 Azure 入口網站來完成。 只要按幾下，您就能在瀏覽器中看到下列結果：

![在瀏覽器中檢視使用 Azure Container Instances 所部署的應用程式][aci-portal-07]

## <a name="log-in-to-azure"></a>登入 Azure

在 http://portal.azure.com 上登入 Azure 入口網站。

## <a name="create-a-container-instance"></a>建立容器執行個體

選取 [建立資源] > [容器] > [Azure 容器執行個體]。

![開始在 Azure 入口網站中建立新的容器執行個體][aci-portal-01]

在 [容器名稱]、[容器映像] 和 [資源群組] 文字方塊中輸入下列值。 將其他的值保留預設值，然後按一下 [確定]。

* 容器名稱：`mycontainer`
* 容器映像：`microsoft/aci-helloworld`
* 資源群組：`myResourceGroup`

![在 Azure 入口網站中設定新容器執行個體的基本設定][aci-portal-03]

您可以在 Azure Container Instances 中建立 Windows 和 Linux 容器。 在本快速入門中，我們將保留 **Linux** 的預設設定，因為我們已在上一個步驟中指定以 Linux 為基礎的容器 (`microsoft/aci-helloworld`)。

將 [設定] 中的其他設定保留預設值，然後按一下 [確定] 以驗證設定。

![在 Azure 入口網站中設定新的容器執行個體][aci-portal-04]

當驗證完成時，系統會向您顯示容器設定的摘要。 選取 [確定] 以提交容器部署要求。

![Azure 入口網站中新容器執行個體的設定摘要][aci-portal-05]

當部署開始時，系統會在入口網站儀表板上放置一個磚，以指出部署進度。 部署完成時，此磚會更新以向您顯示新的 **mycontainer-myc1** 容器群組。

![Azure 入口網站中新容器執行個體的建立進度][aci-portal-08]

選取 [mycontainer-myc1] 容器群組以顯示容器群組屬性。 記下容器群組的 [IP 位址]，以及容器的 [狀態]。

![Azure 入口網站中的容器群組概觀][aci-portal-06]

一旦容器轉換為 [正在執行] 狀態，請瀏覽至您在上一個步驟中記下的 IP 位址，以顯示裝載在新容器中的應用程式。

![在瀏覽器中檢視使用 Azure Container Instances 所部署的應用程式][aci-portal-07]

## <a name="delete-the-container"></a>刪除容器
完成容器的作業後，請選取 **mycontainer-myc1** 容器群組，然後按一下 [刪除]。

![刪除 Azure 入口網站中的容器執行個體][aci-portal-09]

這會啟動確認對話方塊，出現提示時請選取 [是]。

![Azure 入口網站中容器執行個體的刪除確認][aci-portal-10]

<!-- IMAGES -->
[aci-portal-01]: ./media/container-instances-quickstart-portal/qs-portal-01.png
<!--[aci-portal-02]: ./media/container-instances-quickstart-portal/qs-portal-02.png-->
[aci-portal-03]: ./media/container-instances-quickstart-portal/qs-portal-03.png
[aci-portal-04]: ./media/container-instances-quickstart-portal/qs-portal-04.png
[aci-portal-05]: ./media/container-instances-quickstart-portal/qs-portal-05.png
[aci-portal-06]: ./media/container-instances-quickstart-portal/qs-portal-06.png
[aci-portal-07]: ./media/container-instances-quickstart-portal/qs-portal-07.png
[aci-portal-08]: ./media/container-instances-quickstart-portal/qs-portal-08.png
[aci-portal-09]: ./media/container-instances-quickstart-portal/qs-portal-09.png
[aci-portal-10]: ./media/container-instances-quickstart-portal/qs-portal-10.png

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已透過來自公用 Docker Hub 存放庫中的映像建立 Azure 容器執行個體。 如果您想要自行建置容器，並使用 Azure Container Registry 將它部署至 Azure Container Instances，請繼續進行 Azure Container Instances 教學課程。

> [!div class="nextstepaction"]
> [Azure Container Instances 教學課程](./container-instances-tutorial-prepare-app.md)