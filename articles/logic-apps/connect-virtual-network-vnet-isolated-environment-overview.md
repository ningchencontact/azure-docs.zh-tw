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
ms.date: 05/06/2019
ms.openlocfilehash: 1ef8c8eec3865f2a6e363e7da1dbda9504b81c05
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65546409"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>透過整合服務環境 (ISE) 從 Azure Logic Apps 存取 Azure 虛擬網路資源

有時候，您的 logic apps 與整合帳戶需要存取受保護的資源，例如虛擬機器 (Vm) 和其他系統或服務內[Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)。 若要設定此存取權，您可以[建立*integration service 環境*(ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)您可以在此執行您的 logic apps，並建立您的整合帳戶。

當您建立的 ISE 時，Azure 部署的私用和隔離執行個體的 Logic Apps 服務到您的 Azure 虛擬網路。 此私人執行個體使用專用資源 (例如儲存體)，並會與公用「全域」Logic Apps 服務分別執行。 用來分隔您隔離的私人執行個體和公用的全域執行個體也有助於減少其他 Azure 租用戶可能必須在您的應用程式的效能，這就是所謂的影響[「 吵雜芳鄰 」 效果](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors)。

建立您的 ISE 之後, 當您建立您的邏輯應用程式或整合帳戶中，您可以選取您的 ISE 做為您的邏輯應用程式或整合帳戶的位置：

![選取整合服務環境](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

邏輯應用程式現在可以直接存取系統內，或已連線到您的虛擬網路，使用任何這些項目：

* **ISE**-標示為適用於該系統，例如 SQL Server 連接器
* A **Core**-標示為內建的觸發程序或動作，例如 HTTP 觸發程序或動作
* 自訂連接器

本概觀說明關於如何 ISE 可讓您的 logic apps 的更多詳細資料，並整合帳戶直接存取您的 Azure 虛擬網路，並比較 ISE 和全域的 Logic Apps 服務之間的差異。

> [!NOTE]
> Logic apps、 內建的觸發程序、 內建動作和執行 ISE 使用定價方案的耗用量為基礎的定價方案從不同的連接器。 如需詳細資訊，請參閱 [Logic Apps 定價](../logic-apps/logic-apps-pricing.md)。

<a name="difference"></a>

## <a name="isolated-versus-global"></a>隔離式與全域

當您在 Azure 中建立的整合式的服務環境 (ISE) 時，您可以選取您想要的 Azure 虛擬網路*插入*您 ISE。 Azure 接著會插入，或部署到您的虛擬網路私人 Logic Apps 服務的執行個體。 這個動作會建立一個隔離式環境，您可以在其中建立邏輯應用程式並在專用資源上執行。 當您建立邏輯應用程式時，您可以選取您的 ISE 做為您的應用程式的位置，讓邏輯應用程式的直接存取您的虛擬網路和在該網路中的資源。

ISE 中的 Logic Apps 提供與全域 Logic Apps 服務相同的使用者體驗及相似功能。 不只可以使用同一個內建的觸發程序、 內建動作和連接器，從全域的邏輯應用程式服務，但您也可以使用 ISE 特定的連接器。 例如，以下是一些標準的連接器，提供在 ISE 中執行的版本：

* Azure Blob 儲存體、檔案儲存體及表格儲存體
* Azure 佇列、Azure 服務匯流排、Azure 事件中樞和 IBM MQ
* FTP 和 SFTP-SSH
* SQL Server、SQL 資料倉儲、Azure Cosmos DB
* AS2、X12 及 EDIFACT

ISE 與非 ISE 連接器的差異是觸發與動作執行的位置：

* 在 ISE 中，內建的觸發程序和動作，例如 HTTP、 一律相同在 ISE 中執行做為邏輯應用程式，然後顯示**Core**標籤。

  ![選取 「 核心 」 內建的觸發程序和動作](./media/connect-virtual-network-vnet-isolated-environment-overview/select-core-built-in-actions-triggers.png)

* 在 ISE 中執行的連接器已公開裝載全域的 Logic Apps 服務中可用的版本。 提供兩個版本，連接器的連接器**ISE**標籤一律都在相同的 ISE 和邏輯應用程式中執行。 不具備 **ISE** 標籤的連接器則會在全域 Logic Apps 服務中執行。

  ![選取 ISE 連接器](./media/connect-virtual-network-vnet-isolated-environment-overview/select-ise-connectors.png)

### <a name="access-to-on-premises-data-sources"></a>在內部部署資料來源的存取權

對於連線到 Azure 虛擬網路的內部部署系統，ISE 插入該網路，因此您的邏輯應用程式可以直接存取這些系統，使用任何這些項目：

* 該系統，例如，SQL Server 的 ISE 版本連接器
  
* HTTP 動作
  
* 自訂連接器

  * 如果您有需要則內部部署資料閘道的自訂連接器，您建立這些外部 ISE 的連接器，在 ISE 中的邏輯應用程式也可以使用這些連接器。
  
  * 在 ISE 中建立自訂連接器不使用內部部署資料閘道。 不過，這些連接器，可以直接存取內部部署資料來源連接到裝載 ISE 的虛擬網路。 因此，在 ISE 中的邏輯應用程式很可能不需要部署資料閘道與這些資源進行通訊時。

對於不會連線至虛擬網路，或沒有 ISE 版本連接器的內部部署系統，您必須先[設定的內部部署資料閘道](../logic-apps/logic-apps-gateway-install.md)之前您邏輯應用程式可以連接至這些系統。

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>使用 ISE 的整合帳戶

您可以使用整合帳戶搭配整合服務環境 (ISE) 內的邏輯應用程式。 不過，這些整合帳戶必須使用「相同的 ISE」  作為連結的邏輯應用程式。 ISE 中的 Logic Apps 只能參考位於相同 ISE 中的整合帳戶。 當您建立整合帳戶時，您可以選取您的 ISE，作為您整合帳戶的位置。

## <a name="next-steps"></a>後續步驟

* 了解如何[從隔離式 Logic Apps 連線到 Azure 虛擬網路](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* 深入了解 [Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)
* 了解 [Azure 服務的虛擬網路整合](../virtual-network/virtual-network-for-azure-services.md)
