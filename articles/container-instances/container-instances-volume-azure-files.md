---
title: 在 Azure 容器執行個體中掛接 Azure 檔案磁碟區
description: 了解如何掛接 Azure 檔案磁碟區來保存 Azure 容器執行個體的狀態
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 07/08/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 25cac6a66baeb1587e4b5ba3f0923ca9c4394706
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68325492"
---
# <a name="mount-an-azure-file-share-in-azure-container-instances"></a>在 Azure 容器執行個體中掛接 Azure 檔案共用

根據預設，Azure 容器執行個體都是無狀態的。 如果容器損毀或停止，其所有狀態都會遺失。 若要在容器超過存留期後保存其狀態，您必須從外部存放區掛接磁碟區。 本文示範如何掛接使用 [Azure 檔案](../storage/files/storage-files-introduction.md)建立的 Azure 檔案共用，以便與 Azure 容器執行個體搭配使用。 Azure 檔案提供雲端中完全受控的檔案共用，可透過業界標準伺服器訊息區 (SMB) 通訊協定來存取。 將 Azure 檔案共用與 Azure 容器執行個體搭配使用，可提供類似於將 Azure 檔案共用與 Azure 虛擬機器搭配使用的檔案共用功能。

> [!NOTE]
> 目前只有 Linux 容器才能掛接 Azure 檔案共用。 雖然我們正致力於將所有功能帶入 Windows 容器, 但是您可以在[總覽](container-instances-overview.md#linux-and-windows-containers)中找到目前的平臺差異。

## <a name="create-an-azure-file-share"></a>建立 Azure 檔案共用

在搭配使用 Azure 檔案共用與 Azure 容器執行個體前，您必須先建立 Azure 檔案共用。 請執行下列指令碼來建立儲存體帳戶，以裝載檔案共用和共用本身。 儲存體帳戶名稱必須為全域獨有，指令碼才能為基礎字串加上隨機值。

```azurecli-interactive
# Change these four parameters as needed
ACI_PERS_RESOURCE_GROUP=myResourceGroup
ACI_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
ACI_PERS_LOCATION=eastus
ACI_PERS_SHARE_NAME=acishare

# Create the storage account with the parameters
az storage account create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --location $ACI_PERS_LOCATION \
    --sku Standard_LRS

# Create the file share
az storage share create --name $ACI_PERS_SHARE_NAME --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME
```

## <a name="get-storage-credentials"></a>取得儲存體認證

若要在 Azure 容器執行個體中掛接 Azure 檔案共用來作為磁碟區，您需要三個值：儲存體帳戶名稱、共用名稱和儲存體存取金鑰。

如果您使用上述指令碼，儲存體帳戶名稱會儲存於 $ACI_PERS_STORAGE_ACCOUNT_NAME 變數中。 若要查看帳戶名稱，請輸入：

```console
echo $ACI_PERS_STORAGE_ACCOUNT_NAME
```

我們已知道共用名稱 (在上述指令碼中定義為 acishare)，因此只差儲存體帳戶金鑰，此金鑰可以使用下列命令來找到：

```azurecli-interactive
STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" --output tsv)
echo $STORAGE_KEY
```

## <a name="deploy-container-and-mount-volume---cli"></a>部署容器和載入磁片區-CLI

若要使用 Azure CLI 將 Azure 檔案共用掛接為容器中的磁片區, 請在使用[az container create][az-container-create]建立容器時, 指定共用和磁片區掛接點。 如果您已遵循上述步驟，您可以使用下列命令來掛接先前建立的共用並建立容器：

```azurecli-interactive
az container create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name hellofiles \
    --image mcr.microsoft.com/azuredocs/aci-hellofiles \
    --dns-name-label aci-demo \
    --ports 80 \
    --azure-file-volume-account-name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --azure-file-volume-account-key $STORAGE_KEY \
    --azure-file-volume-share-name $ACI_PERS_SHARE_NAME \
    --azure-file-volume-mount-path /aci/logs/
```

此`--dns-name-label`值在您建立容器實例所在的 Azure 區域中必須是唯一的。 如果您在執行命令時收到 **DNS 名稱標籤**錯誤訊息，請更新上方命令中的值。

## <a name="manage-files-in-mounted-volume"></a>管理已掛接磁碟區中的檔案

一旦容器啟動之後, 您就可以使用透過 Microsoft [aci-aci-hellofiles][aci-hellofiles]映射部署的簡單 web 應用程式, 在 Azure 檔案共用中, 于您指定的掛接路徑建立小型文字檔。 使用[az container show][az-container-show]命令來取得 web 應用程式的完整功能變數名稱 (FQDN):

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP --name hellofiles --query ipAddress.fqdn --output tsv
```

使用應用程式儲存文字之後, 您可以使用[Azure 入口網站][portal]或[Microsoft Azure 儲存體總管][storage-explorer]之類的工具來抓取和檢查寫入檔案共用的檔案。

## <a name="deploy-container-and-mount-volume---yaml"></a>部署容器和掛接磁片區-YAML

您也可以部署容器群組, 並在具有 Azure CLI 和[YAML 範本](container-instances-multi-container-yaml.md)的容器中裝載磁片區。 部署由多個容器所組成的容器群組時，偏好經由 YAML 範本進行部署。

下列 YAML 範本會定義一個容器群組, 其中包含一個以`aci-hellofiles`映射建立的容器。 容器會掛接先前建立為磁片區的 Azure 檔案共用*acishare* 。 在指示的位置, 輸入裝載檔案共用之儲存體帳戶的名稱和儲存體金鑰。 

如同在 CLI 範例中, 此`dnsNameLabel`值在您建立容器實例所在的 Azure 區域中必須是唯一的。 如有需要, 請更新 YAML 檔中的值。

```yaml
apiVersion: '2018-10-01'
location: eastus
name: file-share-demo
properties:
  containers:
  - name: hellofiles
    properties:
      environmentVariables: []
      image: mcr.microsoft.com/azuredocs/aci-hellofiles
      ports:
      - port: 80
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - mountPath: /aci/logs/
        name: filesharevolume
  osType: Linux
  restartPolicy: Always
  ipAddress:
    type: Public
    ports:
      - port: 80
    dnsNameLabel: aci-demo
  volumes:
  - name: filesharevolume
    azureFile:
      sharename: acishare
      storageAccountName: <Storage account name>
      storageAccountKey: <Storage account key>
tags: {}
type: Microsoft.ContainerInstance/containerGroups
```

若要使用 YAML 範本進行部署, 請將上述 YAML 儲存到名`deploy-aci.yaml`為的檔案, 然後使用`--file`參數執行[az container create][az-container-create]命令:

```azurecli
# Deploy with YAML template
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```
## <a name="deploy-container-and-mount-volume---resource-manager"></a>部署容器和載入磁片區-Resource Manager

除了 CLI 和 YAML 部署之外, 您還可以使用 Azure [Resource Manager 範本](/azure/templates/microsoft.containerinstance/containergroups)來部署容器群組, 並在容器中裝載磁片區。

首先，填入範本的容器群組 `properties` 區段中的 `volumes` 陣列。 

然後, 針對您想要在其中掛接磁片`volumeMounts` `properties`區的每個容器, 在容器定義的區段中填入陣列。

下列 Resource Manager 範本會定義一個容器群組, 其中包含一個以`aci-hellofiles`映射建立的容器。 容器會掛接先前建立為磁片區的 Azure 檔案共用*acishare* 。 在指示的位置, 輸入裝載檔案共用之儲存體帳戶的名稱和儲存體金鑰。 

如先前範例所示, 此`dnsNameLabel`值在您建立容器實例所在的 Azure 區域中必須是唯一的。 如有需要, 請更新範本中的值。

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "variables": {
    "container1name": "hellofiles",
    "container1image": "mcr.microsoft.com/azuredocs/aci-hellofiles"
  },
  "resources": [
    {
      "name": "file-share-demo",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2018-10-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "containers": [
          {
            "name": "[variables('container1name')]",
            "properties": {
              "image": "[variables('container1image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "port": 80
                }
              ],
              "volumeMounts": [
                {
                  "name": "filesharevolume",
                  "mountPath": "/aci/logs"
                }
              ]
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [
            {
              "protocol": "tcp",
              "port": "80"
            }
          ],
          "dnsNameLabel": "aci-demo"
        },
        "volumes": [
          {
            "name": "filesharevolume",
            "azureFile": {
                "shareName": "acishare",
                "storageAccountName": "<Storage account name>",
                "storageAccountKey": "<Storage account key>"
            }
          }
        ]
      }
    }
  ]
}
```

若要使用 Resource Manager 範本進行部署, 請將上述 JSON 儲存到名`deploy-aci.json`為的檔案, 然後使用`--template-file`參數執行[az group deployment create][az-group-deployment-create]命令:

```azurecli
# Deploy with Resource Manager template
az group deployment create --resource-group myResourceGroup --template-file deploy-aci.json
```


## <a name="mount-multiple-volumes"></a>掛接多個磁碟區

若要在容器執行個體中掛接多個磁碟區，您必須使用 [Azure Resource Manager 範本](/azure/templates/microsoft.containerinstance/containergroups)或 YAML 檔案進行部署。 若要使用範本或 YAML 檔案, 請提供共用詳細資料, 並在範本的`volumes` `properties`區段中填入陣列來定義磁片區。 

例如, 如果您在儲存體帳戶*myStorageAccount*中建立兩個名為*share1*和*share2*的 Azure 檔案儲存體`volumes`共用, 則 Resource Manager 範本中的陣列會如下所示:

```JSON
"volumes": [{
  "name": "myvolume1",
  "azureFile": {
    "shareName": "share1",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
},
{
  "name": "myvolume2",
  "azureFile": {
    "shareName": "share2",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
}]
```

接下來，針對您想要掛接磁碟區所在容器群組中的每個容器，填入容器定義之 `properties` 區段中的 `volumeMounts` 陣列。 例如，這會掛接兩個先前定義的磁碟區：*myvolume1* 和 *myvolume2*：

```JSON
"volumeMounts": [{
  "name": "myvolume1",
  "mountPath": "/mnt/share1/"
},
{
  "name": "myvolume2",
  "mountPath": "/mnt/share2/"
}]
```

## <a name="next-steps"></a>後續步驟

了解如何在 Azure 容器執行個體中掛接其他類型的磁碟區：

* [在 Azure 容器執行個體中掛接 emptyDir 磁碟區](container-instances-volume-emptydir.md)
* [在 Azure 容器執行個體中掛接 gitRepo 磁碟區](container-instances-volume-gitrepo.md)
* [在 Azure 容器執行個體中掛接秘密磁碟區](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-hellofiles]: https://hub.docker.com/_/microsoft-azuredocs-aci-hellofiles 
[portal]: https://portal.azure.com
[storage-explorer]: https://storageexplorer.com

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create