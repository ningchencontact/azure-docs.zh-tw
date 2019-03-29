---
title: 重新啟動使用 Azure CLI 適用於 PostgreSQL 的 Azure 資料庫
description: 這篇文章說明如何重新啟動 Azure Database for PostgreSQL 伺服器使用 Azure CLI
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 3/28/2019
ms.openlocfilehash: 51b3011c040db8576c13868f9fac26cb1e431515
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2019
ms.locfileid: "58623284"
---
# <a name="restart-azure-database-for-postgresql-server-using-the-azure-cli"></a>重新啟動使用 Azure CLI 適用於 PostgreSQL 的 Azure 資料庫
本主題說明如何重新啟動適用於 PostgreSQL 的 Azure 資料庫伺服器。 您可能會為了進行維護而需要重新啟動伺服器，進而在伺服器執行作業時導致短暫中斷。

如果服務忙碌中，系統會阻止伺服器重新啟動。 例如，該服務可能正在處理先前要求的作業，例如調整虛擬核心。
 
完成重新啟動所需的時間取決於 PostgreSQL 復原程序。 若要減少重新啟動時間，建議您先盡量減少伺服器上發生的活動數量，再進行重新啟動。

## <a name="prerequisites"></a>必要條件
若要完成本操作說明指南，您需要：
- [適用於 PostgreSQL 的 Azure 資料庫伺服器](quickstart-create-server-up-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> 本操作說明指南會要求您使用 Azure CLI 2.0 版或更新版本。 若要確認版本，請在 Azure CLI 命令提示字元中輸入 `az --version`。 若要安裝或升級，請參閱[安裝 Azure CLI]( /cli/azure/install-azure-cli)。


## <a name="restart-the-server"></a>重新啟動伺服器

重新啟動伺服器，使用下列命令：

```azurecli-interactive
az postgres server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>後續步驟

深入了解[如何設定 Azure 資料庫中的參數，適用於 PostgreSQL](howto-configure-server-parameters-using-cli.md)