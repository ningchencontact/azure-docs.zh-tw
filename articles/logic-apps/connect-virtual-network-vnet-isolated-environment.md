---
title: 透過整合服務環境 (ISE) 從 Azure Logic Apps 連線至 Azure 虛擬網路
description: 建立整合服務環境 (ISE) 可讓邏輯應用程式與整合帳戶可以存取 Azure 虛擬網路 (VNET)，同時維持私用並獨立於公用或「全域」Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 12/06/2018
ms.openlocfilehash: b0fd2466d72b1aae65a54b9e9813a5af51bf1672
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/06/2018
ms.locfileid: "52997520"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-through-an-integration-service-environment-ise"></a>透過整合服務環境 (ISE) 從 Azure Logic Apps 連線至 Azure 虛擬網路

> [!NOTE]
> 這項功能目前處於「個人預覽版」階段。 若要要求存取，請在[這裡建立您的加入要求](https://aka.ms/iseprivatepreview)。

針對您邏輯應用程式與整合帳戶需存取 [Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)的案例，建立[整合服務的環境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)。 ISE 是一種隔離的私人環境，它會使用與公用或「全域」Logic Apps 服務分開的專用儲存體和其他資源。 這種區隔也可減少任何其他 Azure 租用戶可能對您應用程式效能造成的影響。 您的 ISE 會「插入」Azure 虛擬網路，然後將 Logic Apps 服務部署到您的虛擬網路。 當您建立邏輯應用程式或整合帳戶時，請選取此 ISE 作為其位置。 然後，您的邏輯應用程式或整合帳戶就可以直接存取虛擬網路中的資源，例如：虛擬機器 (VM)、伺服器、系統及服務。 

![選取整合服務環境](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

本文會示範如何完成這些工作：

* 設定 Azure 虛擬網路的權限，讓私人 Logic Apps 執行個體可以存取您的虛擬網路。

* 建立您的整合服務環境 (ISE)。 

* 建立可以在您 ISE 中執行的邏輯應用程式。 

* 在您的 ISE 中建立邏輯應用程式的整合帳戶。

如需整合服務環境的詳細資訊，請參閱[從 Azure Logic Apps 存取 Azure 虛擬網路資源](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先<a href="https://azure.microsoft.com/free/" target="_blank">註冊免費的 Azure 帳戶</a>。 

* [Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)。 如果您沒有虛擬網路，請了解如何[建立 Azure 虛擬網路](../virtual-network/quick-create-portal.md)。 

* 若要讓邏輯應用程式直接存取您的 Azure 虛擬網路，請[設定角色型存取控制 (RBAC) 權限](#vnet-access)，讓 Logic Apps 服務具有存取虛擬網路的權限。 

* [如何建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知識

<a name="vnet-access"></a>

## <a name="set-virtual-network-permissions"></a>設定虛擬網路權限

當您建立整合服務環境 (ISE) 時，您可以選取要在其中「插入」環境的 Azure 虛擬網路。 但是，您必須在虛擬網路中設定角色型存取控制 (RBAC) 權限，才能選取要插入環境的虛擬網路。 若要設定權限，請將這些特定角色指派給 Azure Logic Apps 服務：

1. 在 [Azure 入口網站](https://portal.azure.com)中，尋找並選取您的虛擬網路。 

1. 在您虛擬網路的功能表中，選取 [存取控制 (IAM)]。 

1. 在 [存取控制 (IAM)] 底下，選擇 [新增角色指派]。 

   ![新增角色](./media/connect-virtual-network-vnet-isolated-environment/set-up-role-based-access-control-vnet.png)

1. 在 [新增角色指派] 窗格中，將必要角色新增至 Azure Logic Apps 服務，如上所述。 

   1. 在 [角色] 底下選取 [網路參與者]。 
   
   1. 在 [存取權指派對象為] 底下，選取 [Azure AD 使用者、群組或服務主體]。

   1. 在 [選取] 底下輸入 **Azure Logic Apps**。 

   1. 在成員清單出現之後，選取 [Azure Logic Apps]。 

      > [!TIP]
      > 如果您找不到此服務，請輸入 Logic Apps 服務的應用程式識別碼：`7cd684f4-8a78-49b0-91ec-6a35d38739ba` 
   
   1. 完成之後，請選擇 [儲存]。

   例如︰

   ![新增角色指派](./media/connect-virtual-network-vnet-isolated-environment/add-contributor-roles.png)

如需詳細資訊，請參閱[虛擬網路存取權限](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)。

<a name="create-environment"></a>

## <a name="create-your-ise"></a>建立您的 ISE

若要建立您的整合服務環境 (ISE)，請遵循下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com)中，於 Azure 主功能表上選取 [建立資源]。

   ![建立新資源](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. 在搜尋方塊中，輸入「整合服務環境」作為篩選條件。
從 [結果] 清單中，選取 [整合服務環境 (預覽)]，然後選擇 [建立]。

   ![選取 [整合服務環境]](./media/connect-virtual-network-vnet-isolated-environment/select-integration-service-environment.png)

   ![選擇 [建立]](./media/connect-virtual-network-vnet-isolated-environment/create-integration-service-environment.png)

1. 為您的環境提供這些詳細資料，然後選擇 [檢閱 + 建立]，例如：

   ![提供環境詳細資料](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | 屬性 | 必要 | 值 | 說明 |
   |----------|----------|-------|-------------|
   | **訂用帳戶** | 是 | <*Azure-subscription-name*> | 要用於環境的 Azure 訂用帳戶 | 
   | **資源群組** | 是 | <*Azure-resource-group-name*> | 您要用來建立環境的 Azure 資源群組 |
   | **整合服務環境名稱** | 是 | <*environment-name*> | 提供給環境的名稱 | 
   | **位置** | 是 | <*Azure-datacenter-region*> | 要用來部署環境的 Azure 資料中心區域 | 
   | **容量** | 是 | 0、1、2、3 | 要用於此 ISE 資源的處理單元數 | 
   | **虛擬網路** | 是 | <*Azure-virtual-network-name*> | 要插入環境的 Azure 虛擬網路，讓該環境中的邏輯應用程式可以存取虛擬網路。 如果您沒有網路，可以在此建立一個。 <p>**重要**：您「只」可以在建立您的 ISE 時執行此插入作業。 但是，在您能夠建立此關聯性之前，請確定您已經[在虛擬網路中為 Azure Logic Apps 設定角色型存取控制](#vnet-access)。 | 
   | **子網路** | 是 | <*IP-address-range*> | ISE 需要四個「空白」子網路。 這些子網路不會委派給任何服務，而且會用來在您的環境中建立資源。 您在建立環境之後「無法變更」這些 IP 範圍。 <p><p>若要建立每個子網路，[請遵循此表格底下的步驟](#create-subnet)。 每個子網路都必須符合下列準則： <p>- 不得存在於所選虛擬網路的相同位址範圍中，也不能在虛擬網路連線的任何其他私人 IP 位址中。 <br>- 使用的名稱不能以數字或連字號開頭。 <br>- 使用[無類別網域間路由選擇 (CIDR) 格式](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)。 <br>- 需要類別 B 位址空間。 <br>- 包含 `/27`。 例如，此處每個子網路會指定 32 位元位址範圍：`10.0.0.0/27`、`10.0.0.32/27`、`10.0.0.64/27` 和 `10.0.0.96/27`。 <br>- 必須是空白的。 |
   |||||

   <a name="create-subnet"></a>

   **建立子網路**

   1. 在 [子網路] 清單底下，選擇 [管理子網路設定]。

      ![管理子網路設定](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet.png)

   1. 在 [子網路] 窗格中，選擇 [子網路]。

      ![新增子網路](./media/connect-virtual-network-vnet-isolated-environment/add-subnet.png)

   1. 在 [新增子網路] 窗格中，提供這項資訊。

      * **名稱**：您的子網路名稱
      * **位址範圍 (CIDR 區塊)**：在您虛擬網路中且使用 CIDR 格式的子網路範圍

      ![新增子網路詳細資料](./media/connect-virtual-network-vnet-isolated-environment/subnet-details.png)

   1. 完成時，選擇 [確定]。

   1. 針對其他三個子網路重複這些步驟。

1. 在 Azure 成功驗證您的 ISE 資訊之後，選擇 [建立]，例如：

   ![驗證成功之後，選擇 [建立]](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Azure 會開始部署您的環境，但此程序「可能」需要長達兩小時的時間才能完成。 
   若要檢查部署狀態，請在 Azure 工具列上選擇通知圖示，這會開啟 [通知] 窗格。

   ![檢查部署狀態](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   成功完成部署時，Azure 就會顯示此通知：

   ![部署成功](./media/connect-virtual-network-vnet-isolated-environment/deployment-success.png)

   > [!NOTE]
   > 如果部署失敗或您刪除了 ISE，Azure「可能」需要最多一小時的時間來釋出您的子網路。 因此，您可能必須稍等，才能在另一個的 ISE 中重複使用這些子網路。

1. 如果 Azure 在部署完成之後不會自動移至您的環境，且若要檢視您的環境，請選擇 [移至資源]。  

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-app---ise"></a>建立邏輯應用程式 - ISE

若要建立使用整合服務環境 (ISE) 的邏輯應用程式，請遵循[如何建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)中的步驟，但具有以下差異： 

* 當您建立邏輯應用程式時，在 [位置] 屬性底下的 [整合服務環境] 區段中選取您的 ISE，例如：

  ![選取整合服務環境](./media/connect-virtual-network-vnet-isolated-environment/create-logic-app-with-integration-service-environment.png)

* 您可以使用同一個內建觸發程序或動作 (例如 HTTP)，以便在與您邏輯應用程式相同的 ISE 中執行。 具有 **ISE** 標籤的連接器也會在與您邏輯應用程式相同的 ISE 中執行。 不具備 **ISE** 標籤的連接器則會在全域 Logic Apps 服務中執行。

  ![選取 ISE 連接器](./media/connect-virtual-network-vnet-isolated-environment/select-ise-connectors.png)

* 將您的 ISE 插入 Azure 虛擬網路後，ISE 中的邏輯應用程式就可以直接存取該虛擬網路中的資源。 針對連線至虛擬網路的內部部署系統，若將 ISE 插入網路，則您的邏輯應用程式就可使用以下任何項目來直接存取這些系統： 

  * 該系統的 ISE 連接器，例如 SQL Server
  
  * HTTP 動作 
  
  * 自訂連接器

  針對未在虛擬網路內或不具備 ISE 連接器的內部部署系統，請先[設定內部部署的資料閘道](../logic-apps/logic-apps-gateway-install.md)。

<a name="create-integration-account-environment"></a>

## <a name="create-integration-account---ise"></a>建立整合帳戶 - ISE

若要在整合服務環境 (ISE) 中搭配使用整合帳戶與邏輯應用程式，該整合帳戶必須使用與邏輯應用程式「相同的環境」。 ISE 中的邏輯應用程式只能參考相同 ISE 中的整合帳戶。 

若要建立使用 ISE 的整合帳戶，請遵循[如何建立整合帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)中的步驟，但 [位置] 屬性除外，因為現在會顯示 [整合服務環境] 區段。 因此，請改為選取您的 ISE，而不是區域，例如：

![選取整合服務環境](./media/connect-virtual-network-vnet-isolated-environment/create-integration-account-with-integration-service-environment.png)

## <a name="get-support"></a>取得支援

* 如有問題，請瀏覽 <a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">Azure Logic Apps 論壇</a>。
* 若要提交或票選功能構想，請造訪 <a href="https://aka.ms/logicapps-wish" target="_blank">Logic Apps 使用者意見反應網站</a>。

## <a name="next-steps"></a>後續步驟

* 深入了解 [Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)
* 了解 [Azure 服務的虛擬網路整合](../virtual-network/virtual-network-for-azure-services.md)
