---
title: Azure 資料庫中設定 SSL 連線能力，適用於 PostgreSQL-單一伺服器
description: 指示和資訊來設定 Azure Database for PostgreSQL-，單一伺服器和相關聯的應用程式，以適當使用 SSL 連接。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 56611267872ca79d7d2fe3a08c9b9f49a9b1840b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65067418"
---
# <a name="configure-ssl-connectivity-in-azure-database-for-postgresql---single-server"></a>Azure 資料庫中設定 SSL 連線能力，適用於 PostgreSQL-單一伺服器
適用於 PostgreSQL 的 Azure 資料庫偏好使用安全通訊端層 (SSL)，來將用戶端應用程式連接到 PostgreSQL 服務。 在您的資料庫伺服器和用戶端應用程式之間強制使用 SSL 連線，可將伺服器與應用程式之間的資料流加密，有助於抵禦「中間人」攻擊。

預設會將 PostgreSQL 資料庫服務設定為需要 SSL 連接。 (選擇性) 如果您的用戶端應用程式不支援 SSL 連線能力，您可以將需要 SSL 才能連線到資料庫服務的功能停用。 

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
許多針對其資料庫服務使用 PostgreSQL 的常見應用程式架構 (例如 Drupal 和 Django)，都預設在安裝期間不啟用 SSL。 必須在安裝之後，或透過應用程式特定的 CLI 命令，啟用 SSL 連線能力。 如果您的 PostgreSQL 伺服器正強制執行 SSL 連接，且未正確設定相關聯的應用程式，則應用程式可能無法連接到您的資料庫伺服器。 請參閱您的應用程式文件，以了解如何啟用 SSL 連接。


## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>需要憑證驗證才能啟用 SSL 連線能力的應用程式
在某些情況下，應用程式需要從受信任憑證授權單位 (CA) 憑證檔案 (.cer) 所產生的本機憑證檔，才能安全地連接。 請參閱下列步驟來取得 .cer 檔案、解碼憑證，並將它繫結至您的應用程式。

### <a name="download-the-certificate-file-from-the-certificate-authority-ca"></a>從憑證授權單位 (CA) 下載憑證檔 
您可以在[這裡](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt)找到要透過 SSL 與您適用於 PostgreSQL 之 Azure 資料庫伺服器通訊所需的憑證。 本機下載憑證檔。

### <a name="install-a-cert-decoder-on-your-machine"></a>在電腦上安裝憑證解碼器 
您可以使用[OpenSSL](https://github.com/openssl/openssl)解碼安全地連線到您的資料庫伺服器的應用程式所需的憑證檔案。 若要了解如何安裝 OpenSSL，請參閱[OpenSSL 的安裝指示](https://github.com/openssl/openssl/blob/master/INSTALL)。 


### <a name="decode-your-certificate-file"></a>將憑證檔案解碼
下載的根 CA 檔案是加密格式。 使用 OpenSSL 來將憑證檔案解碼。 若要這樣做，請執行此 OpenSSL 命令：

```
openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
```

### <a name="connecting-to-azure-database-for-postgresql-with-ssl-certificate-authentication"></a>使用 SSL 憑證驗證連接至適用於 PostgreSQL 的 Azure 資料庫
既然您已成功將憑證解碼，您現在可以透過 SSL 安全地連接到資料庫伺服器。 若要允許伺服器憑證驗證，憑證必須放置於使用者主目錄上的 ~/.postgresql/root.crt 檔案中 (在 Microsoft Windows 上，此檔案會命名為 %APPDATA%\postgresql\root.crt)。 

#### <a name="connect-using-psql"></a>使用 psql 來連線
下列範例會示範如何使用 psql 命令列公用程式，來成功地連線到 PostgreSQL 伺服器。 使用所建立的 `root.crt` 檔案和 `sslmode=verify-ca` 或 `sslmode=verify-full` 選項。

使用 PostgreSQL 命令列介面，執行下列命令：
```bash
psql "sslmode=verify-ca sslrootcert=root.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=mylogin@mydemoserver"
```
如果成功，您應該會收到下列輸出：
```bash
Password for user mylogin@mydemoserver:
psql (9.6.2)
WARNING: Console code page (437) differs from Windows code page (1252)
     8-bit characters might not work correctly. See psql reference
     page "Notes for Windows users" for details.
SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-SHA384, bits: 256, compression: off)
Type "help" for help.

postgres=>
```

## <a name="next-steps"></a>後續步驟
在[適用於 PostgreSQL 的 Azure 資料庫的連線庫](concepts-connection-libraries.md)之後，檢閱各種應用程式連線能力選項。
