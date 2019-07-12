---
title: 使用 Azure 檔案儲存體設定
description: 如何設定及連線到 Windows 容器中 App Service 上的 Azure 檔案。
author: msangapu-msft
manager: gwallace
ms.service: app-service
ms.workload: web
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu-msft
ms.openlocfilehash: ea6555e8459b8f372a73d7f943e9a96523d4c791
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789042"
---
# <a name="configure-azure-files-in-a-windows-container-on-app-service"></a>在 App Service 上的 Windows 容器中設定 Azure 檔案

> [!NOTE]
> 這篇文章適用於自訂的 Windows 容器。 若要在部署至 App Service _Linux_，請參閱[從 Azure 儲存體提供內容](./containers/how-to-serve-content-from-azure-storage.md)。
>

本指南說明如何存取 Azure 儲存體，在 Windows 容器中。 只有[Azure 檔案共用](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli)並[Premium 檔案共用](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-premium-fileshare)支援。 您可以使用 Azure 檔案共用此使用方法中。 好處包括受保護的內容、內容可攜性、可存取多個應用程式，以及多個傳輸方法。

## <a name="prerequisites"></a>先決條件

- [Azure CLI](/cli/azure/install-azure-cli) (2.0.46 或更新版本)。
- [在 Azure App Service 中現有的 Windows 容器應用程式](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-windows-container)
- [建立 Azure 檔案共用](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli)
- [將檔案上傳至 Azure 檔案共用](https://docs.microsoft.com/azure/storage/files/storage-files-deployment-guide)

> [!NOTE]
> Azure 檔案為非預設儲存體，並分開計費，不包括在 web 應用程式。 它不支援使用防火牆設定，因為基礎結構限制。
>

## <a name="link-storage-to-your-web-app-preview"></a>將儲存體連結到您的 Web 應用程式 (預覽)

 若要掛接 Azure 檔案共用 App Service 應用程式中的目錄，您使用[ `az webapp config storage-account add` ](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add)命令。 儲存體類型必須是 azure 檔案。

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory of form c:<directory name> >
```

針對任何其他您想要連結至 Azure 檔案的目錄共用，您應該這麼做。

## <a name="verify"></a>驗證

一旦 Azure 檔案共用連結至 web 應用程式，您可以藉由執行下列命令來確認此：

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```


## <a name="next-steps"></a>後續步驟

- [將 ASP.NET 應用程式移轉至 Azure App Service 使用 Windows 容器 （預覽）](app-service-web-tutorial-windows-containers-custom-fonts.md)。