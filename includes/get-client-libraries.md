---
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/25/2018
ms.author: spelluru
ms.openlocfilehash: 3cd5f59a3dcf3afcd26d16874e7dc77ca0a7e844
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/03/2019
ms.locfileid: "71841677"
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
2. 在您的專案根目錄中下載 **[composer.phar][composer-phar]** 。
3. 開啟命令提示字元，在專案根目錄中執行下列命令
   
    ```
    php composer.phar install
    ```

或者，移至 GitHub 上的[AZURE 儲存體 PHP 用戶端程式庫][php-sdk-github]來複製原始程式碼。

[php-sdk-github]: https://github.com/Azure/azure-storage-php
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[download-SDK-PHP]: https://github.com/Azure/azure-sdk-for-php
[composer-phar]: http://getcomposer.org/composer.phar
