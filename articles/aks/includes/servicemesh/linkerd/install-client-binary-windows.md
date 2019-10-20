---
author: paulbouwer
ms.service: container-service
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 2a1249d134c23f47f939913fa1c9887d2a8e1f63
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2019
ms.locfileid: "72600864"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>下載並安裝 Linkerd Linkerd 用戶端二進位檔

在 Windows 上以 PowerShell 為基礎的 shell 中，使用 `Invoke-WebRequest` 下載 Linkerd 版本，如下所示：

```powershell
# Specify the Linkerd version that will be leveraged throughout these instructions
$LINKERD_VERSION="stable-2.6.0"

# Enforce TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-windows.exe" -OutFile "linkerd2-cli-$LINKERD_VERSION-windows.exe"
```

@No__t_0 用戶端二進位檔會在您的用戶端電腦上執行，並可讓您與 Linkerd 服務網格互動。 使用下列命令，在 Windows 上以 PowerShell 為基礎的 shell 中安裝 Linkerd `linkerd` 用戶端二進位檔。 這些命令會將 `linkerd` 用戶端二進位檔複製到 Linkerd 資料夾，然後透過您的 `PATH`，以立即（在目前的 shell 中）和永久（跨 shell 重新開機）來使用它。 您不需要更高的（系統管理員）許可權來執行這些命令，也不需要重新開機 shell。

```powershell
# Copy linkerd.exe to C:\Linkerd
New-Item -ItemType Directory -Force -Path "C:\Linkerd"
Copy-Item -Path ".\linkerd2-cli-$LINKERD_VERSION-windows.exe" -Destination "C:\Linkerd\linkerd.exe"

# Add C:\Linkerd to PATH. 
# Make the new PATH permanently available for the current User, and also immediately available in the current shell.
$PATH = [environment]::GetEnvironmentVariable("PATH", "User") + "; C:\Linkerd\"
[environment]::SetEnvironmentVariable("PATH", $PATH, "User") 
[environment]::SetEnvironmentVariable("PATH", $PATH)
```