---
title: SSL 連線能力-適用於 MariaDB 的 Azure 資料庫
description: 用來設定適用於 MariaDB 的 Azure 資料庫與相關聯應用程式以適當使用 SSL 連線的資訊
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: b7206db24c813c8f273dd57407c43974932ff110
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2019
ms.locfileid: "74772022"
---
# <a name="ssl-connectivity-in-azure-database-for-mariadb"></a>適用於 MariaDB 的 Azure 資料庫中的 SSL 連線能力
適用於 MariaDB 的 Azure 資料庫支援使用安全通訊端層 (SSL)，將資料庫伺服器連接至用戶端應用程式。 在您的資料庫伺服器和用戶端應用程式之間強制使用 SSL 連線，可將兩者之間的資料流加密，有助於抵禦「中間人」攻擊。

## <a name="default-settings"></a>預設設定
根據預設，資料庫服務應該會設定為在連線到 MariaDB 時需要 SSL 連接。  建議盡可能地避免停用 SSL 選項。

透過 Azure 入口網站與 CLI 佈建新的適用於 MariaDB 的 Azure 資料庫伺服器時，預設會強制執行 SSL 連線。

Azure 入口網站中會顯示多種程式設計語言的連接字串。 這些連接字串包含連接到您資料庫所需的 SSL 參數。 在 Azure 入口網站中選取您的伺服器。 在下 [設定] 標題之下，選取 [連接字串]。 SSL 參數會根據連接器而有所不同，例如，"ssl=true" 或 "sslmode=require" 或 "sslmode=required" 及其他變化。

若要了解如何在開發應用程式時啟用或停用 SSL 連接，請參閱[如何設定 SSL](howto-configure-ssl.md)。

## <a name="next-steps"></a>後續步驟
- 深入了解[伺服器防火牆規則](concepts-firewall-rules.md)
- 了解如何[設定 SSL](howto-configure-ssl.md)。
