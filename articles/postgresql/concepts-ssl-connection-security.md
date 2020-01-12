---
title: SSL-適用於 PostgreSQL 的 Azure 資料庫-單一伺服器
description: 有關如何為適用於 PostgreSQL 的 Azure 資料庫單一伺服器設定 SSL 連線能力的指示和資訊。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 4f7fd3ea1c83e1bf5183aedf4fe894809884414c
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2020
ms.locfileid: "75903515"
---
# <a name="configure-ssl-connectivity-in-azure-database-for-postgresql---single-server"></a>在適用於 PostgreSQL 的 Azure 資料庫-單一伺服器中設定 SSL 連線能力
適用於 PostgreSQL 的 Azure 資料庫偏好使用安全通訊端層 (SSL)，來將用戶端應用程式連接到 PostgreSQL 服務。 在您的資料庫伺服器和用戶端應用程式之間強制使用 SSL 連線，可將伺服器與應用程式之間的資料流程加密，以協助防止「攔截式」攻擊。

預設會將 PostgreSQL 資料庫服務設定為需要 SSL 連接。 如果您的用戶端應用程式不支援 SSL 連線能力，您可以選擇停用需要 SSL。 

## <a name="enforcing-ssl-connections"></a>強制使用 SSL 連線
針對透過 Azure 入口網站和 CLI 佈建之所有適用於 PostgreSQL 的 Azure 資料庫伺服器，預設會啟用強制執行 SSL 連接。 

同樣地，在 Azure 入口網站中，您的伺服器下方的 [連接字串] 設定中所預先定義的連接字串，也包含通用語言使用 SSL 連線至資料庫伺服器所需的參數。 SSL 參數會根據連接器而有所不同，例如，"ssl=true" 或 "sslmode=require" 或 "sslmode=required" 及其他變化。

## <a name="configure-enforcement-of-ssl"></a>設定強制使用 SSL
您可以選擇性地停用強制執行 SSL 連線能力。 Microsoft Azure 建議您一律啟用 [強制執行 SSL 連接] 設定，以取得增強的安全性。

> [!NOTE]
> 適用於 PostgreSQL 的 Azure 資料庫目前支援的 TLS 版本為 TLS 1.0、TLS 1.1、TLS 1.2。

### <a name="using-the-azure-portal"></a>使用 Azure 入口網站
瀏覽您適用於 PostgreSQL 的 Azure 資料庫伺服器，然後按一下 [連接安全性]。 使用切換按鈕來啟用或停用 [強制使用 SSL 連線] 設定。 然後按一下 [ **儲存**]。 

![連接安全性：停用強制執行 SSL](./media/concepts-ssl-connection-security/1-disable-ssl.png)

您可以檢視 [概觀] 頁面來查看 [SSL 強制執行狀態] 指標，以確認此設定。

### <a name="using-azure-cli"></a>使用 Azure CLI
您可以在 Azure CLI 中分別使用 `Enabled` 或 `Disabled` 值，來啟用或停用 **sssl-enforcement** 參數。

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>確定您的應用程式或架構支援 SSL 連線
某些針對其資料庫服務使用於 postgresql 的應用程式架構，在安裝期間預設不會啟用 SSL。 如果您的于 postgresql 伺服器強制執行 SSL 連線，但未針對 SSL 設定應用程式，則應用程式可能無法連線到您的資料庫伺服器。 請參閱您的應用程式文件，以了解如何啟用 SSL 連線。


## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>需要憑證驗證才能啟用 SSL 連線能力的應用程式
在某些情況下，應用程式需要從受信任憑證授權單位 (CA) 憑證檔案 (.cer) 所產生的本機憑證檔，才能安全地連接。 用來連接到適用於 PostgreSQL 的 Azure 資料庫伺服器的憑證位於 https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem 。 下載憑證檔案，並將它儲存到您慣用的位置。 

### <a name="connect-using-psql"></a>使用 psql 連接
下列範例示範如何使用 psql 命令列公用程式連線到您的于 postgresql 伺服器。 使用 [`sslmode=verify-full` 連接字串] 設定，以強制執行 SSL 憑證驗證。 將本機憑證檔案路徑傳遞至 `sslrootcert` 參數。

以下是 psql 連接字串的範例：
```
psql "sslmode=verify-full sslrootcert=BaltimoreCyberTrustRoot.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=myusern@mydemoserver"
```

> [!TIP]
> 確認傳遞給 `sslrootcert` 的值符合您儲存之憑證的檔案路徑。


## <a name="next-steps"></a>後續步驟
在[適用於 PostgreSQL 的 Azure 資料庫的連接程式庫](concepts-connection-libraries.md)中，檢查各種應用程式連接選項。
