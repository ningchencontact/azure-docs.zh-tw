---
title: 包含檔案
description: 包含檔案
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 01/28/2019
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: 8439da94c770bee313a1ae1d1da5df30683cd2ad
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/23/2019
ms.locfileid: "66155718"
---
## <a name="cli-shell"></a>CLI Shell

建議使用 [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) 執行 CLI 命令。 **Cloud Shell** 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 Cloud Shell 中已預先安裝和設定共用 Azure 工具，以便您搭配自己的帳戶使用。 它可讓您彈性地選擇最適合您工作方式的殼層體驗。 Linux 使用者可以選擇 Bash 體驗，而 Windows 使用者可以選擇 PowerShell。

如果您選擇在本機安裝和使用 CLI，本文會要求您執行 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。 

### <a name="login"></a>登入

若要開始使用 CLI shell (在雲端或本機)，請執行 `az login` 以建立與 Azure 的連線。

如果 CLI 可以開啟預設瀏覽器，它會執行這項操作，並載入登入頁面。 否則，您需要開啟瀏覽器頁面，並遵循命令列中的指示，在瀏覽器中瀏覽至 https://aka.ms/devicelogin 之後，輸入授權碼。

### <a name="specify-location-of-files"></a>指定檔案位置

許多媒體服務 CLI 命令可讓您使用檔案名稱傳遞參數。 如果您使用 **Cloud Shell**，您可以將檔案上傳至您的 clouddrive (使用 Bash 或 PowerShell)。 

![上傳檔案]

無論您使用本機 CLI 還是 **Cloud Shell**，都必須根據您所使用的 OS 或 Cloud Shell (Bash 或 PowerShell) 指定檔案路徑。 以下是一些範例：

檔案的相對路徑 (所有 OS)

* `@"mytestfile.json"`
* `@"../mytestfile.json"`

Linux/Mac 和 Windows OS 上的絕對檔案路徑

* `@ "/usr/home/mytestfile.json"`
*   `@"c:\tmp\user\mytestfile.json"`

如果命令要求檔案路徑，請使用 `{file}`。 例如： `az ams transform create -a amsaccount -g resourceGroup -n custom --preset .\customPreset.json`。 <br/> 如果命令要載入指定檔案，請使用 `@{file}`。 例如： `az ams account-filter create -a amsaccount -g resourceGroup -n filterName --tracks @tracks.json`。

[上傳檔案]: ./media/media-services-cli/upload-download-files.png
