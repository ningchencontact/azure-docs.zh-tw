---
title: 適用於 MySQL 的 Azure 資料庫管理預存程式
description: 瞭解適用於 MySQL 的 Azure 資料庫中的哪些預存程式有助於設定複寫中的資料、設定時區和終止查詢。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 09/19/2019
ms.openlocfilehash: 34598278ac7f432c5976de86eaf5dcf477c7e81a
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2019
ms.locfileid: "71970328"
---
# <a name="azure-database-for-mysql-management-stored-procedures"></a>適用於 MySQL 的 Azure 資料庫管理預存程式

預存程式可在適用於 MySQL 的 Azure 資料庫伺服器上使用，以協助管理您的 MySQL 伺服器。 這包括管理伺服器的連線、查詢，以及設定資料複寫。  

## <a name="data-in-replication-stored-procedures"></a>資料入複寫預存程式

資料帶入複寫可讓您將來自在內部部署執行的 MySQL 伺服器、虛擬機器中或由其他雲端提供者所代管的資料庫服務的資料，同步處理到適用於 MySQL 的 Azure 資料庫服務。

下列預存程序可用來設定或移除主體和複本之間的資料輸入複寫。

|**預存程序名稱**|**輸入參數**|**輸出參數**|**使用方式注意事項**|
|-----|-----|-----|-----|
|*mysql.az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|N/A|若要使用 SSL 模式傳送資料，將 CA 憑證的內容傳入 master_ssl_ca 參數。 </br><br>如果不使用 SSL 傳輸資料，將空字串傳入 master_ssl_ca 參數。|
|*mysql.az_replication _start*|N/A|N/A|開始複寫。|
|*mysql.az_replication _stop*|N/A|N/A|停止複寫。|
|*mysql.az_replication _remove_master*|N/A|N/A|移除主體與複本之間的複寫關聯性。|
|*mysql.az_replication_skip_counter*|N/A|N/A|略過一個複寫錯誤。|

若要在適用於 MySQL 的 Azure 資料庫的主伺服器和複本之間設定資料入複寫，請參閱[如何設定資料入](howto-data-in-replication.md)複寫。

## <a name="other-stored-procedures"></a>其他已儲存的程序

適用於 MySQL 的 Azure 資料庫可以使用下列預存程式來管理您的伺服器。

|**預存程序名稱**|**輸入參數**|**輸出參數**|**使用方式注意事項**|
|-----|-----|-----|-----|
|*mysql. az _kill*|processlist_id|N/A|相當於[`KILL CONNECTION`](https://dev.mysql.com/doc/refman/8.0/en/kill.html)命令。 終止連接正在執行的任何語句之後，將會終止與所提供之 processlist_id 相關聯的連接。|
|*mysql. az _kill_query*|processlist_id|N/A|相當於[`KILL QUERY`](https://dev.mysql.com/doc/refman/8.0/en/kill.html)命令。 將會終止目前正在執行連接的語句。 讓連接本身保持運作。|
|*mysql. az _load_timezone*|N/A|N/A|載入時區資料表，以允許將 `time_zone` 參數設定為已命名的值（例如 「美國/太平洋」）。|

## <a name="next-steps"></a>後續步驟
- 瞭解如何設定複寫[中的資料](howto-data-in-replication.md)
- 瞭解如何使用時區[資料表](howto-server-parameters.md#working-with-the-time-zone-parameter)
