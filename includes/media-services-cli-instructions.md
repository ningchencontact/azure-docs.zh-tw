---
title: 包含檔案
description: 包含檔案
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 01/25/2019
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: b335cf996de41f4eea15af1899a0c6654c2f679f
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2019
ms.locfileid: "55104978"
---
## <a name="open-cli-shell"></a>開啟 CLI Shell

建議使用 [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) 執行 CLI 命令。 **Cloud Shell** 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 Cloud Shell 中已預先安裝和設定共用 Azure 工具，以便您搭配自己的帳戶使用。 只要選取 [複製] 按鈕即可複製程式碼，將它貼到 Cloud Shell 中，然後按 Enter 鍵加以執行。 以下有幾種開啟 Cloud Shell 的方式：

如果您選擇在本機安裝和使用 CLI，本文會要求您執行 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。 

### <a name="login"></a>登入

若要開始使用 CLI shell (在雲端或本機)，請執行 `az login` 以建立與 Azure 的連線。

如果 CLI 可以開啟預設瀏覽器，它會執行這項操作，並載入登入頁面。 否則，您需要開啟瀏覽器頁面，並遵循命令列中的指示，在瀏覽器中瀏覽至 https://aka.ms/devicelogin 之後，輸入授權碼。

### <a name="specify-location-of-files"></a>指定檔案位置

許多媒體服務 CLI 命令可讓您使用檔案名稱傳遞參數。 

如果您是使用 **Azure Cloud Shell**，請將檔案上傳至 **Azure Cloud Shell**。 您可以在 [shell] 視窗頂端找到 [上傳/下載檔案] 按鈕。 接著，請參考類似以下的檔案：`@{FileName}.` 

![上傳檔案]

若您在本機使用 Azure CLI，請指定完整檔案路徑。 例如： `@c:\tracks.json`。


[上傳檔案]: ./media/media-services-cli/upload-download-files.png
