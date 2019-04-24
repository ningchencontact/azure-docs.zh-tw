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
ms.date: 03/11/2019
ms.openlocfilehash: b53cd54afdf6243769602971ab77145cfa9ba9cc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60506814"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>透過整合服務環境 (ISE) 從 Azure Logic Apps 存取 Azure 虛擬網路資源

> [!NOTE]
> 這項功能處於[*公開預覽*](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

有時候，您的 Logic Apps 和整合帳戶會需要存取安全資源 (例如虛擬機器 (VM)) 及 [Azure 虛擬網路](../virtual-network/virtual-networks-overview.md) 中的其他系統或服務。 若要設定此存取權，請[建立整合服務環境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)，以便執行邏輯應用程式和整合帳戶。 當您建立的 ISE 時，Azure 部署的私用和隔離執行個體的 Logic Apps 服務到您的 Azure 虛擬網路。 此私人執行個體使用專用資源 (例如儲存體)，並會與公用「全域」Logic Apps 服務分別執行。 用來分隔您隔離的私人執行個體和公用的全域執行個體也有助於減少其他 Azure 租用戶可能必須在您的應用程式的效能，這就是所謂的影響[「 吵雜芳鄰 」 效果](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors)。

建立您的 ISE 之後, 當您建立您的邏輯應用程式或整合帳戶中，您可以選取您的 ISE 做為您的邏輯應用程式或整合帳戶的位置：

![選取整合服務環境](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

邏輯應用程式現在可以直接存取系統內，或已連線到您的虛擬網路，使用任何這些項目：

* ISE 建立版本的連接器，該系統，例如，SQL Server
* 內建的觸發程序或動作，例如 HTTP 觸發程序或動作
* 自訂連接器

本概觀說明關於如何 ISE 可讓您的 logic apps 的更多詳細資料，並整合帳戶直接存取您的 Azure 虛擬網路，並比較 ISE 和全域的 Logic Apps 服務之間的差異。
對於不會連線至虛擬網路，或沒有 ISE 版本連接器的內部部署系統，您可以連接到這些系統所[設定及使用內部部署資料閘道](../logic-apps/logic-apps-gateway-install.md)。

> [!IMPORTANT]
> Logic Apps、內建動作，以及在您 ISE 中執行的連接器會使用不同定價方案，而非使用量式定價方案。 如需詳細資訊，請參閱 [Logic Apps 定價](../logic-apps/logic-apps-pricing.md)。

<a name="difference"></a>

## <a name="isolated-versus-global"></a>隔離式與全域

當您在 Azure 中建立的整合式的服務環境 (ISE) 時，您可以選取您想要的 Azure 虛擬網路*插入*您 ISE。 Azure 接著會插入，或部署到您的虛擬網路私人 Logic Apps 服務的執行個體。 這個動作會建立一個隔離式環境，您可以在其中建立邏輯應用程式並在專用資源上執行。 當您建立邏輯應用程式時，您可以選取您的 ISE 做為您的應用程式的位置，讓邏輯應用程式的直接存取您的虛擬網路和在該網路中的資源。

ISE 中的 Logic Apps 提供與全域 Logic Apps 服務相同的使用者體驗及相似功能。 您不僅可以在全域 Logic Apps 服務中使用相同的內建動作和連接器，也可以使用 ISE 專用連接器。 例如，以下為一些標準連接器，提供在 ISE 中執行的版本：

* Azure Blob 儲存體、檔案儲存體及表格儲存體
* Azure 佇列、Azure 服務匯流排、Azure 事件中樞和 IBM MQ
* FTP 和 SFTP-SSH
* SQL Server、SQL 資料倉儲、Azure Cosmos DB
* AS2、X12 及 EDIFACT

ISE 與非 ISE 連接器的差異是觸發與動作執行的位置：

* 在您的 ISE 中，內建觸發程序或動作 (例如 HTTP) 一律在與您的邏輯應用程式相同的 ISE 中執行。

* 針對提供兩個版本的連接器：其中一個版本會在 ISE 中執行，另一個版本則會在全域 Logic Apps 服務中執行。  

  具備 **ISE** 標籤的連接器一律會在與您 Logic App 相同的 ISE 中執行。 不具備 **ISE** 標籤的連接器則會在全域 Logic Apps 服務中執行。

  ![選取 ISE 連接器](./media/connect-virtual-network-vnet-isolated-environment-overview/select-ise-connectors.png)

* 在 ISE 中執行的連接器也可以在全域 Logic Apps 服務中使用。

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>使用 ISE 的整合帳戶

您可以使用整合帳戶搭配整合服務環境 (ISE) 內的邏輯應用程式。 不過，這些整合帳戶必須使用「相同的 ISE」作為連結的邏輯應用程式。 ISE 中的 Logic Apps 只能參考位於相同 ISE 中的整合帳戶。 當您建立整合帳戶時，您可以選取您的 ISE，作為您整合帳戶的位置。

## <a name="get-support"></a>取得支援

* 如有問題，請瀏覽 <a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">Azure Logic Apps 論壇</a>。
* 若要提交或票選功能構想，請造訪 <a href="https://aka.ms/logicapps-wish" target="_blank">Logic Apps 使用者意見反應網站</a>。

## <a name="next-steps"></a>後續步驟

* 了解如何[從隔離式 Logic Apps 連線到 Azure 虛擬網路](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* 深入了解 [Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)
* 了解 [Azure 服務的虛擬網路整合](../virtual-network/virtual-network-for-azure-services.md)
