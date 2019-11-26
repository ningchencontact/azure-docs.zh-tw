---
title: 使用重新導向連接到適用於 MySQL 的 Azure 資料庫
description: 本文說明如何設定應用程式，以使用重新導向連接到適用於 MySQL 的 Azure 資料庫。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 11/15/2019
ms.openlocfilehash: eed52c444bbcad60b4e629cf476c285f53c9f515
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233736"
---
# <a name="connect-to-azure-database-for-mysql-with-redirection"></a>使用重新導向連接到適用於 MySQL 的 Azure 資料庫

本主題說明如何使用重新導向模式將應用程式連線到您的適用於 MySQL 的 Azure 資料庫伺服器。 重新導向的目標是允許應用程式直接連線到後端伺服器節點，以減少用戶端應用程式和 MySQL 伺服器之間的網路延遲。

> [!IMPORTANT]
> PHP [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure)中的重新導向支援目前為預覽狀態。

## <a name="before-you-begin"></a>開始之前
登入 [Azure 入口網站](https://portal.azure.com)。 建立具有引擎版本5.6、5.7 或8.0 的適用於 MySQL 的 Azure 資料庫伺服器。 如需詳細資訊，請參閱[如何從入口網站建立適用於 MySQL 伺服器的 Azure 資料庫](quickstart-create-mysql-server-database-using-azure-portal.md)或[如何使用 CLI 建立適用於 MySQL 伺服器的 Azure 資料庫](quickstart-create-mysql-server-database-using-azure-cli.md)。

目前只有在啟用 SSL 時，才支援重新導向。 如需如何設定 SSL 的詳細資訊，請參閱[使用 SSL 與適用於 MySQL 的 Azure 資料庫](https://docs.microsoft.com/azure/mysql/howto-configure-ssl#step-3-enforcing-ssl-connections-in-azure)。 

## <a name="php"></a>PHP

### <a name="ubuntu-linux"></a>Ubuntu Linux

#### <a name="prerequisites"></a>先決條件 
- PHP 版本 7.2.15 + 和 7.3.2 +
- PHP 梨 
- php-mysql
- 已啟用 SSL 的適用於 MySQL 的 Azure 資料庫伺服器

1. 使用[PECL](https://pecl.php.net/package/mysqlnd_azure)安裝[mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) 。

    ```bash
    sudo pecl install mysqlnd_azure
    ```

2. 執行下列程式，找出延伸目錄（`extension_dir`）：

    ```bash
    php -i | grep "extension_dir"
    ```

3. 將目錄變更為傳回的資料夾，並確定 `mysqlnd_azure.so` 位於此資料夾中。 

4. 執行下列程式，找出 .ini 檔案的資料夾： 

    ```bash
    php -i | grep "dir for additional .ini files"
    ```

5. 將目錄變更為這個傳回的資料夾。 

6. 建立 `mysqlnd_azure`的新 .ini 檔案。 請確定名稱的字母順序是在 mysqnld 之後，因為模組是根據 ini 檔案的名稱順序載入。 例如，如果 `mysqlnd` .ini 的名稱是 `10-mysqlnd.ini`，請將 mysqlnd ini 命名為 `20-mysqlnd-azure.ini`。

7. 在新的 .ini 檔案中，新增下列幾行以啟用重新導向。

    ```bash
    extension=mysqlnd_azure
    mysqlnd_azure.enabled=on
    ```

### <a name="windows"></a>Windows

#### <a name="prerequisites"></a>先決條件 
- PHP 版本 7.2.15 + 和 7.3.2 +
- php-mysql
- 已啟用 SSL 的適用於 MySQL 的 Azure 資料庫伺服器

1. 藉由執行下列命令來判斷您是否正在執行 x64 或 x86 版本的 PHP：

    ```cmd
    php -i | findstr "Thread"
    ```

2. 從符合您 PHP 版本的[PECL](https://pecl.php.net/package/mysqlnd_azure)下載對應的 x64 或 x86 版本的[mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) DLL。 

3. 解壓縮 zip 檔案，並尋找名為 `php_mysqlnd_azure.dll`的 DLL。

4. 執行下列命令，找出延伸目錄（`extension_dir`）：

    ```cmd
    php -i | find "extension_dir"s
    ```

5. 將 `php_mysqlnd_azure.dll` 檔案複製到步驟4中傳回的目錄。 

6. 使用下列命令，找出包含 `php.ini` 檔案的 PHP 資料夾：

    ```cmd
    php -i | find "Loaded Configuration File"
    ```

7. 修改 `php.ini` 檔案並新增下列額外的行，以啟用重新導向。 

    在 [動態擴充] 區段底下： 
    ```cmd
    extension=mysqlnd_azure
    ```
    
    在 [模組設定] 區段底下：     
    ```cmd 
    [mysqlnd_azure]
    mysqlnd_azure.enabled=on
    ```

### <a name="confirm-redirection"></a>確認重新導向

您也可以使用下列範例 PHP 程式碼來確認重新導向。 建立名為 `mysqlConnect.php` 的 PHP 檔案，並貼上下列程式碼。 以您自己的名稱、使用者名稱和密碼來補救伺服器。 
 
 ```php
<?php
$host = '<yourservername>.mysql.database.azure.com';
$username = '<yourusername>@<yourservername>';
$password = '<yourpassword>';
$db_name = 'testdb';
  echo "mysqlnd_azure.enabled: ", ini_get("mysqlnd_azure.enabled") == true?"On":"Off", "\n";
  $db = mysqli_init();
  $link = mysqli_real_connect ($db, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL);
  if (!$link) {
     die ('Connect error (' . mysqli_connect_errno() . '): ' . mysqli_connect_error() . "\n");
  }
  else {
    echo $db->host_info, "\n"; //if redirection succeeds, the host_info will differ from the hostname you used used to connect
    $res = $db->query('SHOW TABLES;'); //test query with the connection
    print_r ($res);
$db->close();
  }
 ?>
 ```

## <a name="next-steps"></a>後續步驟
如需連接字串的詳細資訊，請參閱[連接字串](howto-connection-string.md)。

