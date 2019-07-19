---
title: 透過整合服務環境 (ISE) 從 Azure Logic Apps 存取 Azure 虛擬網路
description: 概略說明整合服務環境 (ISE) 如何協助邏輯應用程式存取 Azure 虛擬網路 (VNET)
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 07/19/2019
ms.openlocfilehash: 3e14604955a64c7a146a947c5c320b42ea3ebcba
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325400"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>透過整合服務環境 (ISE) 從 Azure Logic Apps 存取 Azure 虛擬網路資源

有時候, 您的邏輯應用程式和整合帳戶需要存取受保護的資源, 例如[Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)內的虛擬機器 (vm) 和其他系統或服務。 若要設定此存取權, 您可以[建立*整合服務環境*(ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) , 您可以在其中執行邏輯應用程式, 並建立整合帳戶。

當您建立 ISE 時, Azure  會將 ise 插入您的 azure 虛擬網路, 然後將 Logic Apps 服務的私用和隔離實例部署至您的 azure 虛擬網路。 此私人執行個體使用專用資源 (例如儲存體)，並會與公用「全域」Logic Apps 服務分別執行。 分隔隔離的私用實例和公用全域實例也有助於降低其他 Azure 租使用者對您應用程式效能的影響, 也就是所謂的「[雜訊鄰近](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors)程度」效果。

建立 ISE 之後, 當您移至建立邏輯應用程式或整合帳戶時, 您可以選取您的 ISE 作為邏輯應用程式或整合帳戶的位置:

![選取整合服務環境](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

您的邏輯應用程式現在可以使用下列任何專案, 直接存取您的虛擬網路內或連線的系統:

* 適用于該系統的**ISE**標示連接器, 例如 SQL Server
* **核心**標示的內建觸發程式或動作, 例如 HTTP 觸發程式或動作
* 自訂連接器

本總覽將詳細說明 ISE 如何讓您的邏輯應用程式和整合帳戶直接存取您的 Azure 虛擬網路, 並比較 ISE 與全域 Logic Apps 服務之間的差異。

> [!IMPORTANT]
> 邏輯應用程式、內建觸發程式、內建動作, 以及在您 ISE 中執行的連接器會使用與以耗用量為基礎的定價方案不同的價格方案。 若要瞭解 Ise 的定價和計費方式, 請參閱[Logic Apps 定價模式](../logic-apps/logic-apps-pricing.md#fixed-pricing)。 如需定價費率, 請參閱[Logic Apps 定價](../logic-apps/logic-apps-pricing.md)。
>
> 您的 ISE 也增加了執行持續時間、儲存體保留期、輸送量、HTTP 要求和回應超時、訊息大小, 以及自訂連接器要求的限制。 
> 如需詳細資訊, 請參閱[Azure Logic Apps 的限制和](logic-apps-limits-and-config.md)設定。

<a name="difference"></a>

## <a name="isolated-versus-global"></a>隔離式與全域

當您在 Azure 中建立整合服務環境 (ISE) 時, 您可以選取要在其中*插入*ISE 的 Azure 虛擬網路。 接著, Azure 會將 Logic Apps 服務的私用實例插入或部署至您的虛擬網路。 這個動作會建立一個隔離式環境，您可以在其中建立邏輯應用程式並在專用資源上執行。 當您建立邏輯應用程式時, 請選取您的 ISE 作為應用程式的位置, 讓邏輯應用程式可直接存取您的虛擬網路和該網路中的資源。

ISE 中的 Logic Apps 提供與全域 Logic Apps 服務相同的使用者體驗及相似功能。 您不僅可以從全域 Logic Apps 服務使用相同的內建觸發程式、內建動作和連接器, 還可以使用 ISE 特定的連接器。 例如, 以下是一些標準連接器, 可提供在 ISE 中執行的版本:

* Azure Blob 儲存體、檔案儲存體及表格儲存體
* Azure 佇列、Azure 服務匯流排、Azure 事件中樞和 IBM MQ
* FTP 和 SFTP-SSH
* SQL Server、SQL 資料倉儲、Azure Cosmos DB
* AS2、X12 及 EDIFACT

ISE 與非 ISE 連接器的差異是觸發與動作執行的位置：

* 在您的 ISE 中, 內建的觸發程式和動作 (例如 HTTP) 一律會在與邏輯應用程式相同的 ISE 中執行, 並顯示**核心**標籤。

  ![選取 [核心] 內建觸發程式和動作](./media/connect-virtual-network-vnet-isolated-environment-overview/select-core-built-in-actions-triggers.png)

* 在 ISE 中執行的連接器具有公開裝載的版本, 可在全域 Logic Apps 服務中使用。 針對提供兩個版本的連接器, 具有**ISE**標籤的連接器一律會在與邏輯應用程式相同的 ISE 中執行。 不具備 **ISE** 標籤的連接器則會在全域 Logic Apps 服務中執行。

  ![選取 ISE 連接器](./media/connect-virtual-network-vnet-isolated-environment-overview/select-ise-connectors.png)

ISE 也會針對執行持續時間、儲存體保留、輸送量、HTTP 要求和回應超時、訊息大小和自訂連接器要求提供更多的限制。 如需詳細資訊, 請參閱[Azure Logic Apps 的限制和](logic-apps-limits-and-config.md)設定。

<a name="ise-level"></a>

## <a name="ise-skus"></a>ISE Sku

當您建立 ISE 時, 可以選取 [開發人員 SKU] 或 [Premium SKU]。 以下是這些 Sku 之間的差異:

* **開發人員**

  提供較低成本的 ISE, 供您用來進行實驗、開發和測試, 但不能用於生產或效能測試。 開發人員 SKU 包含內建的觸發程式和動作、標準連接器、企業連接器, 以及每月固定價格的單一[免費層](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)整合帳戶。 不過, 此 SKU 不包含任何服務等級協定 (SLA)、相應增加容量的選項, 或回收期間的冗余, 這表示您可能會遇到延遲或停機時間。

* **高級**

  提供可用於生產環境的 ISE, 並包含 SLA 支援、內建的觸發程式和動作、標準連接器、企業連接器、單一[標準層](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)整合帳戶、相應增加容量的選項, 以及期間的冗余回收每月固定費用。

如需定價費率, 請參閱[Logic Apps 定價](https://azure.microsoft.com/pricing/details/logic-apps/)。 若要瞭解 Ise 的定價和計費方式, 請參閱[Logic Apps 定價模式](../logic-apps/logic-apps-pricing.md#fixed-pricing)。

<a name="on-premises"></a>

## <a name="access-to-on-premises-data-sources"></a>存取內部部署資料來源

對於連線到 Azure 虛擬網路的內部部署系統, 請將 ISE 插入該網路, 讓您的邏輯應用程式可以使用下列任何專案直接存取這些系統:

* 適用于該系統的 ISE 版本連接器, 例如, SQL Server
* HTTP 動作
* 自訂連接器

  * 如果您有需要內部部署資料閘道的自訂連接器, 而且您在 ISE 外建立了這些連接器, ISE 中的邏輯應用程式也可以使用這些連接器。
  
  * 在 ISE 中建立的自訂連接器無法與內部部署資料閘道搭配使用。 不過, 這些連接器可以直接存取連線到裝載 ISE 之虛擬網路的內部部署資料來源。 因此, ISE 中的邏輯應用程式在與這些資源通訊時, 很可能不需要資料閘道。

對於未連線到虛擬網路或沒有 ISE 版本連接器的內部部署系統, 您必須先[設定內部部署資料閘道](../logic-apps/logic-apps-gateway-install.md), 邏輯應用程式才能連接到這些系統。

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>使用 ISE 的整合帳戶

您可以使用整合帳戶搭配整合服務環境 (ISE) 內的邏輯應用程式。 不過，這些整合帳戶必須使用「相同的 ISE」  作為連結的邏輯應用程式。 ISE 中的 Logic Apps 只能參考位於相同 ISE 中的整合帳戶。 當您建立整合帳戶時，您可以選取您的 ISE，作為您整合帳戶的位置。 若要瞭解如何使用 ISE 進行整合帳戶的定價和計費, 請參閱[Logic Apps 定價模式](../logic-apps/logic-apps-pricing.md#fixed-pricing)。 如需定價費率, 請參閱[Logic Apps 定價](https://azure.microsoft.com/pricing/details/logic-apps/)。

## <a name="next-steps"></a>後續步驟

* 了解如何[從隔離式 Logic Apps 連線到 Azure 虛擬網路](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* 深入了解 [Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)
* 了解 [Azure 服務的虛擬網路整合](../virtual-network/virtual-network-for-azure-services.md)
