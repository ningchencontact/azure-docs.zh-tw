---
author: paulbouwer
ms.service: container-service
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: a4090172e926f21db01a8e374d8c4bb1c80402c7
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/19/2019
ms.locfileid: "74197353"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>下載並安裝 Linkerd Linkerd 用戶端二進位檔

在 MacOS 上以 bash 為基礎的 shell 中，使用 `curl` 下載 Linkerd 版本，如下所示：

```bash
# Specify the Linkerd version that will be leveraged throughout these instructions
LINKERD_VERSION=stable-2.6.0

curl -sLO "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-darwin"
```

`linkerd` 用戶端二進位檔會在您的用戶端電腦上執行，並可讓您與 Linkerd 服務網格互動。 使用下列命令，將 Linkerd `linkerd` 用戶端二進位檔安裝在 MacOS 上以 bash 為基礎的 shell 中。 這些命令會將 `linkerd` 用戶端二進位檔複製到 `PATH` 中的標準使用者程式位置。

```bash
sudo cp ./linkerd2-cli-$LINKERD_VERSION-darwin /usr/local/bin/linkerd
sudo chmod +x /usr/local/bin/linkerd
```

如果您想要讓 Linkerd `linkerd` 用戶端二進位檔的命令列完成，請依照下列方式進行設定：

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && linkerd completion bash > ~/completions/linkerd.bash
source ~/completions/linkerd.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/linkerd.bash" >> ~/.bashrc
```
