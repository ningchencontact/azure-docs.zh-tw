---
title: 在適用於 PostgreSQL 的 Azure 資料庫中設定 SSL 超大規模資料庫（Citus）
description: 用於設定適用於 PostgreSQL 的 Azure 資料庫超大規模資料庫（Citus）和相關聯應用程式以適當使用 SSL 連線的指示和資訊。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: be02c9114f30a63fe710cfd2fc56347bfc68fab5
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2019
ms.locfileid: "71273723"
---
# <a name="configure-ssl-in-azure-database-for-postgresql---hyperscale-citus"></a>在適用於 PostgreSQL 的 Azure 資料庫中設定 SSL 超大規模資料庫（Citus）
超大規模資料庫（Citus）協調器節點的用戶端應用程式連接需要安全通訊端層（SSL）。 在您的資料庫伺服器和用戶端應用程式之間強制使用 SSL 連線，可將伺服器與應用程式之間的資料流程加密，以協助防止「攔截式」攻擊。

## <a name="enforcing-ssl-connections"></a>強制使用 SSL 連線
針對透過 Azure 入口網站布建的所有適用於 PostgreSQL 的 Azure 資料庫伺服器，預設會啟用 [強制執行 SSL 連線]。 

同樣地，在 Azure 入口網站中，您的伺服器下方的 [連接字串] 設定中所預先定義的連接字串，也包含通用語言使用 SSL 連線至資料庫伺服器所需的參數。 SSL 參數會根據連接器而有所不同，例如，"ssl=true" 或 "sslmode=require" 或 "sslmode=required" 及其他變化。

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>確定您的應用程式或架構支援 SSL 連線
某些針對其資料庫服務使用於 postgresql 的應用程式架構，在安裝期間預設不會啟用 SSL。 如果您的于 postgresql 伺服器強制執行 SSL 連線，但未針對 SSL 設定應用程式，則應用程式可能無法連線到您的資料庫伺服器。 請參閱您的應用程式文件，以了解如何啟用 SSL 連線。

## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>需要憑證驗證才能啟用 SSL 連線能力的應用程式
在某些情況下，應用程式需要從受信任憑證授權單位 (CA) 憑證檔案 (.cer) 所產生的本機憑證檔，才能安全地連接。 用來連接到適用於 PostgreSQL 的 Azure 資料庫超大規模資料庫（Citus）的憑證位於 https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem 。 下載憑證檔案，並將它儲存到您慣用的位置。

### <a name="connect-using-psql"></a>使用 psql 連接
下列範例顯示如何使用 psql 命令列公用程式連線到您的超大規模資料庫（Citus）協調器節點。 使用 [ `sslmode=verify-full`連接字串] 設定來強制執行 SSL 憑證驗證。 將本機憑證檔案路徑傳遞給`sslrootcert`參數。

以下是 psql 連接字串的範例：
```
psql "sslmode=verify-full sslrootcert=DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=citus user=citus password=your_pass"
```
> [!TIP]
> 確認傳遞給`sslrootcert`的值符合您儲存之憑證的檔案路徑。

## <a name="next-steps"></a>後續步驟
使用[適用於 PostgreSQL 的 Azure 資料庫超大規模資料庫（Citus）中的防火牆規則](concepts-hyperscale-firewall-rules.md)進一步提升安全性。
