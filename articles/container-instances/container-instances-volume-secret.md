---
title: 在 Azure 容器執行個體中掛接秘密磁碟區
description: 了解如何掛接秘密磁碟區，以儲存供您的容器執行個體存取的機密資訊
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 02/08/2018
ms.author: marsma
ms.openlocfilehash: e20dc3b37bc766b2a8a352eb62c03f5e2ee14c52
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2018
---
# <a name="mount-a-secret-volume-in-azure-container-instances"></a>在 Azure 容器執行個體中掛接秘密磁碟區

了解如何在您的容器執行個體中掛接秘密磁碟區，以供您容器群組中的容器儲存和擷取機密資訊。

> [!NOTE]
> 目前只有 Linux 容器才能掛接「祕密」磁碟區。 雖然我們致力於將所有功能帶入 Windows 容器，但是您可以在 [Azure 容器執行個體配額和區域可用性](container-instances-quotas.md)中找到目前的平台差異。

## <a name="secret-volume"></a>祕密磁碟區

您可以使用秘密磁碟區，將機密資訊提供給容器群組中的容器。 秘密磁碟區會將您的指定密碼儲存在磁碟區內的檔案中，然後容器群組中的容器就可以存取這些檔案。 透過使用秘密磁碟區中的密碼，您可以避免將 SSH 金鑰或資料庫認證等機密資料放入您的應用程式程式碼。

所有秘密磁碟區都由 RAM 型檔案系統 [tmpfs][tmpfs] 支援；其內容永遠不會寫入靜態儲存區。

## <a name="mount-a-secret-volume"></a>掛接秘密磁碟區

若要在容器執行個體中掛接祕密磁碟區，您必須使用 [Azure Resource Manager 範本](/azure/templates/microsoft.containerinstance/containergroups)進行部署。

首先，填入範本的容器群組 `properties` 區段中的 `volumes` 陣列。 接下來，針對您想要掛接祕密磁碟區所在容器群組中的每個容器，填入容器定義之 `properties` 區段中的 `volumeMounts` 陣列。

例如，下列 Resource Manager 範本會建立一個由單一容器組成的容器群組。 容器會掛接由兩個 Base64 編碼密碼所組成的秘密磁碟區。

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-secret.json -->
[!code-json[volume-secret](~/azure-docs-json-samples/container-instances/aci-deploy-volume-secret.json)]

若要使用 Azure Resource Manager 範本來查看容器執行個體部署範例，請參閱[在 Azure 容器執行個體中部署多個容器群組](container-instances-multi-container-group.md)。

## <a name="next-steps"></a>後續步驟

了解如何在 Azure 容器執行個體中掛接其他類型的磁碟區：

* [在 Azure 容器執行個體中掛接 Azure 檔案共用](container-instances-volume-azure-files.md)
* [在 Azure 容器執行個體中掛接 emptyDir 磁碟區](container-instances-volume-emptydir.md)
* [在 Azure 容器執行個體中掛接 gitRepo 磁碟區](container-instances-volume-gitrepo.md)

<!-- LINKS - External -->
[tmpfs]: https://wikipedia.org/wiki/Tmpfs