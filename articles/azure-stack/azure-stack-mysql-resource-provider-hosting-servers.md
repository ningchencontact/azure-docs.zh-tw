---
title: Azure Stack 上的 MySQL 主控伺服器 | Microsoft Docs
description: 如何新增 MySQL 執行個體以便透過 MySQL 配接器資源提供者佈建
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: quying
ms.lastreviewed: 10/16/2018
ms.openlocfilehash: e1a00ea36efa6af816c371f5498085fc2cf491b6
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56165020"
---
# <a name="add-hosting-servers-for-the-mysql-resource-provider"></a>為 SMySQL 資源提供者新增主控伺服器

您可以使用 [Azure Stack](azure-stack-poc.md) 內 VM 上的 MySQL 主控伺服器執行個體，或 Azure Stack 環境外 VM 上的執行個體，只要 MySQL 資源提供者能夠連線到該執行個體均可。

> [!NOTE]
> MySQL 資源提供者應建立在預設提供者訂用帳戶中，而 MySQL 主控伺服器則應建立在可計費的使用者訂用帳戶中。 資源提供者伺服器不應該用來裝載使用者資料庫。

MySQL 5.6、5.7 和 8.0 版可用於您的主控伺服器。 MySQL RP 不支援 caching_sha2_password 驗證；將下一個版本中加入。 MySQL 8.0 伺服器必須設定為使用 mysql_native_password。 也支援 MariaDB。

## <a name="connect-to-a-mysql-hosting-server"></a>連線到 MySQL 主控伺服器

確定您擁有具備系統管理員權限之帳戶的認證。 若要新增主控伺服器，請遵循下列步驟：

1. 以服務管理員身分登入 Azure Stack 操作員入口網站。
2. 選取 [所有服務]。
3. 在 [管理資源] 類別下方，選取 [MySQL 主控伺服器] > [+新增]。 這會開啟 [新增 MySQL 主控伺服器] 對話方塊，如下列螢幕擷取畫面所示。

   ![設定主控伺服器](./media/azure-stack-mysql-rp-deploy/mysql-add-hosting-server-2.png)

4. 提供 MySQL 伺服器執行個體的連線詳細資料。

   * 針對 [MySQL 主控伺服器名稱]，提供完整網域名稱 (FQDN) 或有效的 IPv4 位址。 請勿使用簡短的 VM 名稱。
   * 由於未提供預設的 MySQL 執行個體，因此您必須指定**主控伺服器的大小 (以 GB 為單位)**。 輸入的大小應該接近資料庫伺服器的容量。
   * 保留 [訂用帳戶] 的預設設定。
   * 針對 [資源群組]，您可以建立新群組或使用現有的群組。

   > [!NOTE]
   > 如果租用戶和管理 Azure Resource Manager 可以存取 MySQL 執行個體，您就可以讓資源提供者控制此執行個體。 但是，MySQL 執行個體「必須」專門配置給資源提供者。

5. 選取 [SKU] 以開啟 [建立 SKU] 對話方塊。

   ![建立 MySQL SKU](./media/azure-stack-mysql-rp-deploy/mysql-new-sku.png)

   SKU 的 [名稱] 應反映 SKU 屬性，以便使用者將其資料庫部署到適當的 SKU。

6. 選取 [確定] 以建立 SKU。
> [!NOTE]
> 最多需要一小時才能在入口網站中看到 SKU。 您必須等到 SKU 部署完畢並開始執行之後，才能建立資料庫。

7. 在 [新增 MySQL 主控伺服器] 下方，選取 [建立]。

新增伺服器時，必須將它們指派給新的或現有的 SKU，以將服務供應項目差異化。 例如，您可以使用 MySQL 企業執行個體來提供增加的資料庫和自動備份。 您可以為組織中不同部門保留此高效能伺服器。

## <a name="security-considerations-for-mysql"></a>MySQL 的安全性考量

下列資訊適用於 RP 和 MySQL 主控伺服器：

* 請確定所有主控伺服器都設定為使用 TLS 1.2 通訊。 請參閱[將 MySQL 設定為使用加密連線](https://dev.mysql.com/doc/refman/5.7/en/using-encrypted-connections.html)。
* 採用[透明資料加密](https://dev.mysql.com/doc/mysql-secure-deployment-guide/5.7/en/secure-deployment-data-encryption.html)。
* MySQL RP 不支援 caching_sha2_password 驗證。

## <a name="increase-backend-database-capacity"></a>增加後端資料庫容量

您可以藉由在 Azure Stack 入口網站中部署更多 MySQL 伺服器，來增加後端資料庫的容量。 將這些伺服器新增到新的或現有的 SKU。 如果您將伺服器新增到現有的 SKU，請確保伺服器特性與 SKU 中的其他伺服器相同。

## <a name="sku-notes"></a>SKU 注意事項
使用可描述 SKU 中伺服器容量的 SKU 名稱，例如容量與效能。 名稱可作為輔助，來幫助使用者將其資料庫部署至適當的 SKU。 例如，您可以使用 SKU 名稱依下列特性區分服務供應項目：
  
* 高容量
* 高效能
* 高可用性

最佳作法是，SKU 中的所有主控伺服器應具有相同的資源和效能特性。

無法將 SKU 指派給特定使用者或群組。

最多需要一小時才能在入口網站中看到 SKU。 在完整建立 SKU 前，使用者無法建立資料庫。

若要編輯 SKU，請移至 [所有服務] > [MySQL 配接器] > [SKU]。 選取要修改的 SKU、進行任何必要的變更，然後按一下 [儲存] 以儲存變更。 若要刪除已不再需要的 SKU，請移至 [所有服務] > [MySQL 配接器] > [SKU]。 以滑鼠右鍵按一下 SKU 名稱，然後選取 [刪除] 將它刪除。

> [!TIP]
> 您可以編輯或刪除相同位置的 MySQL 資源提供者配額。

## <a name="make-mysql-database-servers-available-to-your-users"></a>讓您的使用者可使用 MySQL 資料庫伺服器

建立方案和供應項目，讓使用者使用 MySQL 資料庫伺服器。 將 Microsoft.MySqlAdapter 服務新增到方案，然後建立新配額。 MySQL 不允許限制資料庫的大小。

## <a name="next-steps"></a>後續步驟

[建立 MySQL 資料庫](azure-stack-mysql-resource-provider-databases.md)