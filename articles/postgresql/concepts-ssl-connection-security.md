---
title: Azure 資料庫中設定 SSL 連線能力，適用於 PostgreSQL-單一伺服器
description: 指示和資訊來設定 Azure Database for PostgreSQL-，單一伺服器和相關聯的應用程式，以適當使用 SSL 連接。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 686adfb2998eff10ef4b9f378163b164ba970c56
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461847"
---
# <a name="configure-ssl-connectivity-in-azure-database-for-postgresql---single-server"></a>Azure 資料庫中設定 SSL 連線能力，適用於 PostgreSQL-單一伺服器
適用於 PostgreSQL 的 Azure 資料庫偏好使用安全通訊端層 (SSL)，來將用戶端應用程式連接到 PostgreSQL 服務。 強制執行您的資料庫伺服器和用戶端應用程式之間的 SSL 連線，可以協助防止加密伺服器與您的應用程式之間的資料流 」--攔截 」 攻擊。

預設會將 PostgreSQL 資料庫服務設定為需要 SSL 連接。 您可以選擇停用需要 SSL，如果您的用戶端應用程式不支援 SSL 連線能力。 

## <a name="enforcing-ssl-connections"></a>強制使用 SSL 連線
針對透過 Azure 入口網站和 CLI 佈建之所有適用於 PostgreSQL 的 Azure 資料庫伺服器，預設會啟用強制執行 SSL 連接。 

同樣地，您在 Azure 入口網站的伺服器下方 [連接字串] 設定中預先定義的連接字串，會包含通用語言使用 SSL 連接到您資料庫伺服器所需的必要參數。 SSL 參數會根據連接器而有所不同，例如，"ssl=true" 或 "sslmode=require" 或 "sslmode=required" 及其他變化。

## <a name="configure-enforcement-of-ssl"></a>設定強制執行 SSL
您可以選擇性地停用強制執行 SSL 連線能力。 Microsoft Azure 建議您一律啟用 [強制執行 SSL 連接]  設定，以取得增強的安全性。

### <a name="using-the-azure-portal"></a>使用 Azure 入口網站
瀏覽您適用於 PostgreSQL 的 Azure 資料庫伺服器，然後按一下 [連接安全性]  。 使用切換按鈕來啟用或停用 [強制使用 SSL 連線]  設定。 然後按一下 [ **儲存**]。 

![連接安全性：停用強制執行 SSL](./media/concepts-ssl-connection-security/1-disable-ssl.png)

您可以檢視 [概觀]  頁面來查看 [SSL 強制執行狀態]  指標，藉以確認設定。

### <a name="using-azure-cli"></a>使用 Azure CLI
您可以在 Azure CLI 中分別使用 `Enabled` 或 `Disabled` 值，來啟用或停用 **sssl-enforcement** 參數。

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>確定您的應用程式或架構支援 SSL 連線
針對其資料庫服務使用 PostgreSQL 某些應用程式架構進行不啟用 SSL 預設在安裝期間。 如果您的 PostgreSQL 伺服器強制使用 SSL 連線，但應用程式不會設定為使用 SSL，應用程式可能無法連接到您的資料庫伺服器。 請參閱您的應用程式文件，以了解如何啟用 SSL 連線。


## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>需要憑證驗證才能啟用 SSL 連線能力的應用程式
在某些情況下，應用程式需要從受信任憑證授權單位 (CA) 憑證檔案 (.cer) 所產生的本機憑證檔，才能安全地連接。 若要連接到 Azure Database for PostgreSQL 伺服器是位於憑證 https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem 。 下載憑證檔案，並將它儲存到您慣用的位置。 

### <a name="connect-using-psql"></a>使用 psql 來連線
下列範例示範如何連接到 PostgreSQL 伺服器使用 psql 命令列公用程式。 使用`sslmode=verify-full`來強制執行 SSL 憑證是否通過驗證的連接字串設定。 傳遞的本機憑證檔案路徑`sslrootcert`參數。

以下是 psql 連線字串的範例：
```
psql "sslmode=verify-full sslrootcert=BaltimoreCyberTrustRoot.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=myusern@mydemoserver"
```

> [!TIP]
> 確認此值傳遞至`sslrootcert`符合您所儲存的憑證的檔案路徑。


## <a name="next-steps"></a>後續步驟
檢閱中的各種應用程式連線選項[適用於 PostgreSQL 的 Azure 資料庫的連線庫](concepts-connection-libraries.md)。
