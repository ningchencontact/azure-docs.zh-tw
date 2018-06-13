---
title: 快速入門 - 使用 Azure 入口網站建立您的第一個 Azure Container Instances 容器
description: 在本快速入門中，您會使用 Azure 入口網站在 Azure Container Instances 中部署容器
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: quickstart
ms.date: 05/11/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 6aa6fb27b2aa7c8b9614e5812fadc629b1e185f8
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2018
ms.locfileid: "34076233"
---
# <a name="quickstart-create-your-first-container-in-azure-container-instances"></a>快速入門：在 Azure Container Instances 中建立您的第一個容器

Azure Container Instances 能讓您在 Azure 中輕鬆建立及管理 Docker 容器，不需要佈建虛擬機器或採用高階服務即可完成。 在本快速入門中，您會使用 Azure 入口網站在 Azure 中建立容器，並使用完整網域名稱 (FQDN) 向網際網路公開此容器。 在設定幾項設定後，您就能在瀏覽器中看到下列結果：

![在瀏覽器中檢視使用 Azure Container Instances 所部署的應用程式][aci-portal-07]

## <a name="sign-in-to-azure"></a>登入 Azure

在 https://portal.azure.com 登入 Azure 入口網站。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [[免費帳戶]][azure-free-account]。

## <a name="create-a-container-instance"></a>建立容器執行個體

選取 [建立資源] > [容器] > [容器執行個體]。

![開始在 Azure 入口網站中建立新的容器執行個體][aci-portal-01]

在 [容器名稱]、[容器映像] 和 [資源群組] 文字方塊中輸入下列值。 將其他的值保留預設值，然後選取 [確定]。

* 容器名稱：`mycontainer`
* 容器映像：`microsoft/aci-helloworld`
* 資源群組：`myResourceGroup`

![在 Azure 入口網站中設定新容器執行個體的基本設定][aci-portal-03]

您可以在 Azure Container Instances 中建立 Windows 和 Linux 容器。 在本快速入門中，保留 **Linux** 的預設設定，以部署以 Linux 為基礎的 `microsoft/aci-helloworld` 映像。

在 [組態] 下，為您的容器指定 [DNS 名稱標籤]。 此名稱在您建立容器執行個體所在的 Azure 區域中必須是唯一的。 您的容器將可在 `<dns-name-label>.<region>.azurecontainer.io` 讓他人公開存取。

將 [設定] 中的其他設定保留預設值，然後選取 [確定] 以驗證設定。

![在 Azure 入口網站中設定新的容器執行個體][aci-portal-04]

當驗證完成時，系統會向您顯示容器設定的摘要。 選取 [確定] 以提交容器部署要求。

![Azure 入口網站中新容器執行個體的設定摘要][aci-portal-05]

當部署開始時，系統會在入口網站儀表板上顯示一個圖格，以指出部署正在進行中。 部署完成後，該圖格會顯示您新的容器執行個體。

![Azure 入口網站中新容器執行個體的建立進度][aci-portal-08]

選取 **mycontainer** 容器執行個體以顯示其屬性。 請記下容器執行個體的 [FQDN] (完整網域名稱)，以及其 [狀態]。

![Azure 入口網站中的容器群組概觀][aci-portal-06]

當其 [狀態] 成為 [執行中] 時，請在瀏覽器中瀏覽至容器的 FQDN。

![在瀏覽器中檢視使用 Azure Container Instances 所部署的應用程式][aci-portal-07]

恭喜！ 在進行幾項設定後，您即已部署可在 Azure 容器執行個體中公開存取的應用程式。

## <a name="view-container-logs"></a>檢視容器記錄

在對您的容器或其執行的應用程式排解問題時，檢視的容器執行個體的記錄會很有幫助。

若要檢視容器的記錄，請在 [設定] 下選取 [容器]，然後選取 [記錄]。 您應該會看到您在瀏覽器中檢視應用程式時產生的 HTTP GET 要求。

![Azure 入口網站中的容器記錄][aci-portal-11]

## <a name="clean-up-resources"></a>清除資源

完成容器的作業後，請選取 *mycontainer* 容器執行個體的 [概觀]，然後選取 [刪除]。

![刪除 Azure 入口網站中的容器執行個體][aci-portal-09]

在確認對話方塊出現時，選取 [是]。

![Azure 入口網站中容器執行個體的刪除確認][aci-portal-10]

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已透過來自公用 Docker Hub 登錄中的映像建立 Azure 容器執行個體。 如果您想要自行建置容器映像，並從私人的 Azure 容器登錄將其部署至 Azure 容器執行個體，請繼續進行 Azure 容器執行個體教學課程。

> [!div class="nextstepaction"]
> [Azure 容器執行個體教學課程](./container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[aci-portal-01]: ./media/container-instances-quickstart-portal/qs-portal-01.png
[aci-portal-03]: ./media/container-instances-quickstart-portal/qs-portal-03.png
[aci-portal-04]: ./media/container-instances-quickstart-portal/qs-portal-04.png
[aci-portal-05]: ./media/container-instances-quickstart-portal/qs-portal-05.png
[aci-portal-06]: ./media/container-instances-quickstart-portal/qs-portal-06.png
[aci-portal-07]: ./media/container-instances-quickstart-portal/qs-portal-07.png
[aci-portal-08]: ./media/container-instances-quickstart-portal/qs-portal-08.png
[aci-portal-09]: ./media/container-instances-quickstart-portal/qs-portal-09.png
[aci-portal-10]: ./media/container-instances-quickstart-portal/qs-portal-10.png
[aci-portal-11]: ./media/container-instances-quickstart-portal/qs-portal-11.png

<!-- LINKS - External -->
[azure-free-account]: https://azure.microsoft.com/free/