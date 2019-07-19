---
title: 下載 Azure SDK for PHP
description: 了解如何下載和安裝 Azure SDK for PHP。
documentationcenter: php
services: app-service\web
author: allclark
manager: douge
editor: ''
ms.assetid: bac355ac-4c25-42f4-8273-c5112eafa8d4
ms.service: app-service-web
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 06/01/2016
ms.author: yaya
ms.openlocfilehash: 9dd336ef973b603108aad62c90e4dab385d20317
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/15/2019
ms.locfileid: "67873888"
---
# <a name="download-the-azure-sdk-for-php"></a>下載 Azure SDK for PHP

## <a name="overview"></a>總覽

Azure SDK for PHP 內含的元件可讓您開發、部署及管理適用於 Azure 的 PHP 應用程式。 尤其是 Azure SDK for PHP 包含下列各項：

* **適用於 Azure 的 PHP 用戶端程式庫**。 這些類別庫所提供的介面可供存取 Azure 功能，例如資料管理服務和雲端服務。
* **適用於 Mac、Linux 和 Windows 的 Azure 命令列介面 (Azure CLI)** 。 這是一組命令，可用於部署和管理 Azure 服務，例如 Azure 網站和 Azure 虛擬機器。 Azure CLI 可在任何平台上運作，包括 Mac、Linux 和 Windows。
* **Azure PowerShell (僅限 Windows)** 。 這是一組 PowerShell Cmdlet，可用於部署和管理 Azure 服務，例如雲端服務和虛擬機器。
* **Azure 模擬器 (僅限 Windows)** 。 計算和儲存體模擬器都是雲端服務和資料管理服務的本機模擬器，可讓您在本機測試應用程式。 Azure 模擬器只能在 Windows 上執行。

下列各節說明如何下載和安裝上述元件。

本主題中的指示假設您已安裝[PHP][install-php] 。

> [!NOTE]
> 您必須有 PHP 5.5 或更新版本，才能使用適用於 Azure 的 PHP 用戶端程式庫。
>
>

## <a name="php-client-libraries-for-azure"></a>適用於 Azure 的 PHP 用戶端程式庫

適用於 Azure 的 PHP 用戶端程式庫提供了一個介面，以便從任何作業系統存取 Azure 功能，例如資料管理服務和雲端服務。 您可以透過編輯器安裝這些程式庫。

如需如何使用適用于 Azure 的 PHP 用戶端程式庫的詳細資訊, 請參閱[如何使用 Blob 服務][blob-service], [How to Use the Table Service][table-service]和[如何使用佇列服務][佇列-服務]。

### <a name="install-via-composer"></a>透過編輯器安裝

1. [安裝 Git][install-git]。 在 Windows 中，您也需要將 Git 可執行檔新增至 PATH 環境變數。

2. 在專案的根目錄中建立名為 **composer.json** 的檔案，並新增下列程式碼：

        {
            "require": {
                "microsoft/windowsazure": "^0.4"
            }
        }

3. 在您的專案根目錄中下載 **[composer.phar][composer-phar]** 。

4. 開啟命令提示字元，在專案根目錄中執行此命令

        php composer.phar install

## <a name="azure-powershell-and-azure-emulators"></a>Azure PowerShell 和 Azure 模擬器

Azure PowerShell 是一組 PowerShell Cmdlet，可用於部署和管理 Azure 服務 (例如雲端服務和虛擬機器)。 Azure 模擬器是雲端服務和資料管理服務的模擬器，可讓您在本機測試應用程式。 只有 Windows 支援這些元件。

安裝 Azure PowerShell 和 Azure 模擬器的建議方式是使用[Microsoft Web Platform Installer][download-wpi]。 請注意，您也可以選擇安裝其他開發元件，例如 PHP、SQL Server、適用於 SQL Server for PHP 的 Microsoft 驅動程式和 WebMatrix。

如需如何使用 Azure PowerShell 的詳細資訊, 請參閱[如何使用 Azure PowerShell][powershell-tools]。

## <a name="azure-cli"></a>Azure CLI

Azure CLI 是一組命令，可用於部署和管理 Azure 服務，例如 Azure 網站和 Azure 虛擬機器。 如需安裝 Azure CLI 的相關資訊，請參閱 [安裝 Azure CLI](cli-install-nodejs.md)。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱 [PHP 開發人員中心](https://azure.microsoft.com/develop/php/)。

[install-php]: https://www.php.net/manual/en/install.php
[composer-github]: https://github.com/composer/composer
[composer-phar]: https://getcomposer.org/composer.phar
[nodejs-org]: https://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
[download-wpi]: https://go.microsoft.com/fwlink/?LinkId=253447
[mac-installer]: https://go.microsoft.com/fwlink/?LinkId=252249
[blob-service]: https://go.microsoft.com/fwlink/?LinkId=252714
[table-service]: https://go.microsoft.com/fwlink/?LinkId=252715
[queue-service]: https://go.microsoft.com/fwlink/?LinkId=252716
[azure cli]: https://go.microsoft.com/fwlink/?LinkId=252717
[powershell-tools]: https://go.microsoft.com/fwlink/?LinkId=252718
[php-sdk-github]: https://go.microsoft.com/fwlink/?LinkId=252719
[install-git]: https://git-scm.com/book/en/Getting-Started-Installing-Git
