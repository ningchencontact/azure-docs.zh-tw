---
title: 在 Azure 容器執行個體中掛接 Azure 檔案磁碟區
description: 了解如何掛接 Azure 檔案磁碟區來保存 Azure 容器執行個體的狀態
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 07/08/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: bc09aa500743d608c0a3a7a379fe9584c9c55e9b
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657630"
---
# <a name="mount-an-azure-file-share-in-azure-container-instances"></a>在 Azure 容器執行個體中掛接 Azure 檔案共用

根據預設，Azure 容器執行個體都是無狀態的。 如果容器損毀或停止，其所有狀態都會遺失。 若要在容器超過存留期後保存其狀態，您必須從外部存放區掛接磁碟區。 本文示範如何掛接使用 [Azure 檔案](../storage/files/storage-files-introduction.md)建立的 Azure 檔案共用，以便與 Azure 容器執行個體搭配使用。 Azure 檔案提供雲端中完全受控的檔案共用，可透過業界標準伺服器訊息區 (SMB) 通訊協定來存取。 將 Azure 檔案共用與 Azure 容器執行個體搭配使用，可提供類似於將 Azure 檔案共用與 Azure 虛擬機器搭配使用的檔案共用功能。

> [!NOTE]
> 目前只有 Linux 容器才能掛接 Azure 檔案共用。 雖然我們正努力帶入 Windows 容器中的所有功能，您可以找到目前的平台的差異[概觀](container-instances-overview.md#linux-and-windows-containers)。

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

我們已知道共用名稱 (在上述指令碼中定義為 acishare  )，因此只差儲存體帳戶金鑰，此金鑰可以使用下列命令來找到：

```azurecli-interactive
STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" --output tsv)
echo $STORAGE_KEY
```

## <a name="deploy-container-and-mount-volume---cli"></a>部署容器及掛接磁碟區-CLI

若要使用 Azure CLI，Azure 檔案共用掛接為磁碟區容器中，指定的共用和磁碟區掛接點當您建立的容器[az 容器建立][az-container-create]。 如果您已遵循上述步驟，您可以使用下列命令來掛接先前建立的共用並建立容器：

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

`--dns-name-label`值必須是唯一您用來建立容器執行個體的 Azure 區域內。 如果您在執行命令時收到 **DNS 名稱標籤**錯誤訊息，請更新上方命令中的值。

## <a name="manage-files-in-mounted-volume"></a>管理已掛接磁碟區中的檔案

容器啟動後，您可以使用 Microsoft 部署簡單的 web 應用程式[aci hellofiles][aci-hellofiles] image to create small text files in the Azure file share at the mount path you specified. Obtain the web app's fully qualified domain name (FQDN) with the [az container show][az-container-show]命令：

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP --name hellofiles --query ipAddress.fqdn --output tsv
```

在儲存之後使用應用程式的文字，您可以使用[Azure 入口網站][portal]or a tool like the [Microsoft Azure Storage Explorer][storage-explorer]擷取和檢查寫入至檔案共用的檔案。

## <a name="deploy-container-and-mount-volume---yaml"></a>部署容器及掛接磁碟區-YAML

您也可以部署容器群組，然後掛接磁碟區容器，以使用 Azure CLI 中的， [YAML 範本](container-instances-multi-container-yaml.md)。 部署由多個容器所組成的容器群組時，偏好經由 YAML 範本進行部署。

下列 YAML 範本會定義容器群組中的一個容器，以建立具有`aci-hellofiles`映像。 容器會掛接 Azure 檔案共用*acishare*先前建立的磁碟區。 需要，請輸入裝載檔案共用的儲存體帳戶的名稱和儲存體金鑰。 

如同 CLI 的範例，`dnsNameLabel`值必須是唯一您用來建立容器執行個體的 Azure 區域內。 如有需要請更新 YAML 檔案中的值。

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

若要部署使用 YAML 範本，將上述的 YAML 儲存到名為的檔案`deploy-aci.yaml`，然後執行[az 容器建立][az-container-create]命令搭配`--file`參數：

```azurecli
# Deploy with YAML template
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```
## <a name="deploy-container-and-mount-volume---resource-manager"></a>部署容器及掛接磁碟區-Resource Manager

CLI 和 YAML 除了部署之外，您可以部署容器群組，並掛接磁碟區容器，使用 Azure 中的[Resource Manager 範本](/azure/templates/microsoft.containerinstance/containergroups)。

首先，填入範本的容器群組 `properties` 區段中的 `volumes` 陣列。 

然後，針對您想要掛接磁碟區中每個容器，填入`volumeMounts`陣列中`properties`容器定義的區段。

下列 Resource Manager 範本會定義容器群組中的一個容器，以建立具有`aci-hellofiles`映像。 容器會掛接 Azure 檔案共用*acishare*先前建立的磁碟區。 需要，請輸入裝載檔案共用的儲存體帳戶的名稱和儲存體金鑰。 

如同先前的範例，`dnsNameLabel`值必須是唯一您用來建立容器執行個體的 Azure 區域內。 如有需要請更新在範本中的值。

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

若要部署使用 Resource Manager 範本，將先前的 JSON 儲存到名為的檔案`deploy-aci.json`，然後執行[az 群組部署建立][az-group-deployment-create]命令搭配`--template-file`參數：

```azurecli
# Deploy with Resource Manager template
az group deployment create --resource-group myResourceGroup --template-file deploy-aci.json
```


## <a name="mount-multiple-volumes"></a>掛接多個磁碟區

若要在容器執行個體中掛接多個磁碟區，您必須使用 [Azure Resource Manager 範本](/azure/templates/microsoft.containerinstance/containergroups)或 YAML 檔案進行部署。 若要使用的範本或 YAML 檔案，提供共用詳細資料，以及定義磁碟區填入`volumes`陣列中`properties`範本區段。 

比方說，如果您建立名為兩個 Azure 檔案共用*share1*並*share2*儲存體帳戶中*myStorageAccount*，則`volumes`陣列在 Resource Manager範本會出現如下所示：

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