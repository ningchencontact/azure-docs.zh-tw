---
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/25/2018
ms.author: spelluru
ms.openlocfilehash: b6e0e57881154f5885e9f518363eda3c5b1169a0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60201685"
---
### <a name="install-via-composer"></a>透過編輯器安裝
1. 在專案的根目錄中建立名為 **composer.json** 的檔案，並新增下列程式碼：
   
    ```json
    {
      "require": {
        "microsoft/azure-storage": "*"
      }
    }
    ```
2. 将 [composer.phar][composer-phar] 下载到项目根目录中。
3. 開啟命令提示字元，在專案根目錄中執行下列命令
   
    ```
    php composer.phar install
    ```

或者，移至 GitHub 上的 [Azure 儲存體 PHP 用戶端程式庫][php-sdk-github]來複製原始程式碼。

[php-sdk-github]: https://github.com/Azure/azure-storage-php
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[download-SDK-PHP]: ../articles/php-download-sdk.md
[composer-phar]: http://getcomposer.org/composer.phar
