---
title: 使用 Azure SQL 資料倉儲的 Fivetran 快速入門 | Microsoft Docs
description: 快速開始使用 Fivetran 和 Azure SQL 資料倉儲。
services: sql-data-warehouse
author: hirokib
manager: jrj
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 10/12/2018
ms.author: elbutter
ms.reviewer: craigg
ms.openlocfilehash: 8e738becfe356908af5baffc0ebf225916b2616e
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2018
ms.locfileid: "49355206"
---
# <a name="get-started-quickly-with-fivetran-and-sql-data-warehouse"></a>快速開始使用 Fivetran 和 SQL 資料倉儲

本快速入門假設您已經有 SQL 資料倉儲的既存執行個體。

## <a name="setup-connection"></a>設定連線

1. 尋找完整伺服器名稱和資料庫名稱，以連線到 Azure SQL 資料倉儲。

   [如何從入口網站尋找伺服器名稱和資料庫名稱？](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-connect-overview)

2. 在安裝精靈中，決定您要直接連線到資料庫還是透過 SSH 通道來連線。

   如果您決定直接連線到資料庫，則必須建立防火牆規則以允許存取。 這是最簡單、安全的方法。

   如果您決定透過 SSH 通道進行連線，Fivetran 會連線到網路中的個別伺服器，讓其提供前往資料庫的 SSH 通道。 如果資料庫是在虛擬網路上無法存取的子網路中，則需要這個方法。

3. 在伺服器層級防火牆中新增 "52.0.2.4" IP 位址，以允許從 Fivetran 到 Azure SQL 資料倉儲的連入連線。

   [如何新增伺服器層級防火牆？](https://docs.microsoft.com/azure/sql-data-warehouse/create-data-warehouse-portal#create-a-server-level-firewall-rule)

## <a name="setup-user-credentials"></a>設定使用者認證

使用 SQL Server Management Studio 或所選工具以伺服器管理員使用者的身分連線到 Azure SQL 資料倉儲，並執行下列 SQL 命令以建立 Fivetran 使用者：

在 master 資料庫中：` CREATE LOGIN fivetran WITH PASSWORD = '<password>'; `

在 SQL 資料倉儲資料庫中：

```
CREATE USER fivetran_user_without_login without login;
CREATE USER fivetran FOR LOGIN fivetran;
GRANT IMPERSONATE on USER::fivetran_user_without_login to fivetran;
```

在建立使用者 fivetran 後，對其授與下列倉儲權限：

```
GRANT CONTROL to fivetran;
```

根據要建立資料行存放區索引所需要的記憶體，對所建立的使用者新增合適的資源類別。 例如，Marketo 與 Salesforce 之類的整合因為有大量資料行/大量資料，需要更多記憶體才能建立資料行存放區索引，所以需要較高的資源類別。

建議您使用靜態資源類別。 您可以從資源類別 `staticrc20` 開始，無論您使用哪個效能層級，該類別都會為使用者配置 200 MB。 如果使用目前資源類別時編製資料行存放區索引的作業失敗，我們就必須增加資源類別。

```
EXEC sp_addrolemember '<resource_class_name>', 'fivetran';
```

如需詳細資訊，請參閱[記憶體和並行限制](https://docs.microsoft.com/azure/sql-data-warehouse/memory-and-concurrency-limits#data-warehouse-limits)與[資源類別](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-memory-optimizations-for-columnstore-compression#ways-to-allocate-more-memory)的文件

若要建立資料庫範圍認證 (在從使用 PolyBase 的 Blob 儲存體載入檔案時會用到)，您需要有 CONTROL 權限。

請輸入用來存取 Azure SQL 資料倉儲的認證

1. 主機 (您的伺服器名稱)
2. Port
3. 資料庫
4. 使用者 (使用者名稱應該是 `fivetran@<server_name>`，其中 `<server_name>` 是 Azure 主機 URI 的一部分：`<server_name>.database.windows.net`)
5. 密碼