---
author: paulbouwer
ms.service: container-service
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 1c63d02a2207a396183be4fdcc44074977d37b1a
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2019
ms.locfileid: "72530364"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>下載並安裝 Istio istioctl 用戶端二進位檔

在 Windows 上以 PowerShell 為基礎的 shell 中，使用 `Invoke-WebRequest` 下載 Istio 版本，然後以 `Expand-Archive` 解壓縮，如下所示：

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.3.2"

# Enforce TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

@No__t_0 用戶端二進位檔會在您的用戶端電腦上執行，並可讓您與 Istio 服務網格互動。 使用下列命令，在 Windows 上以 PowerShell 為基礎的 shell 中安裝 Istio `istioctl` 用戶端二進位檔。 這些命令會將 `istioctl` 用戶端二進位檔複製到 Istio 資料夾，然後透過您的 `PATH`，以立即（在目前的 shell 中）和永久（跨 shell 重新開機）來使用它。 您不需要更高的（系統管理員）許可權來執行這些命令，也不需要重新開機 shell。

```powershell
# Copy istioctl.exe to C:\Istio
cd istio-$ISTIO_VERSION
New-Item -ItemType Directory -Force -Path "C:\Istio"
Copy-Item -Path .\bin\istioctl.exe -Destination "C:\Istio\"

# Add C:\Istio to PATH. 
# Make the new PATH permanently available for the current User, and also immediately available in the current shell.
$PATH = [environment]::GetEnvironmentVariable("PATH", "User") + "; C:\Istio\"
[environment]::SetEnvironmentVariable("PATH", $PATH, "User") 
[environment]::SetEnvironmentVariable("PATH", $PATH)
```