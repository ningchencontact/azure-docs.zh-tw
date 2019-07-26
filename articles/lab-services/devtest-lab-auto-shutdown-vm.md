---
title: 在 Azure DevTest Labs 中設定 VM 的 autoshutdown 設定 |Microsoft Docs
description: 瞭解如何設定虛擬機器 (VM) 的 autoshutdown 設定, 讓 VM 在未使用的情況下自動關閉。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2019
ms.author: spelluru
ms.openlocfilehash: 934e8fd71c901c89f328c777103a8cb39bf21ac4
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/19/2019
ms.locfileid: "68361566"
---
# <a name="configure-autoshutdown-settings-for-a-vm-in-azure-devtest-labs"></a>在 Azure DevTest Labs 中設定 VM 的 autoshutdown 設定
Azure DevTest Labs 可讓您管理每個實驗室的原則 (設定)，以控制實驗室的成本並儘可能避免浪費。 本文說明如何設定實驗室帳戶的 autoshutdown 原則, 並在實驗室帳戶中設定實驗室的 autoshutdown 設定。 若要了解如何設定每個實驗室原則，請參閱[在 Azure DevTest Labs 中定義實驗室原則](devtest-lab-set-lab-policy.md)。  

## <a name="autoshutdown-policy-for-a-lab"></a>實驗室的 Autoshutdown 原則
身為實驗室擁有者，您可以在實驗室中設定所有 VM 的關機排程。 如此一來，您可經由執行未使用 (閒置) 的機器來節省成本。 您可以在所有的實驗室 VM 上集中強制執行關機原則，但也可讓實驗室使用者免於為其個別機器設定排程。 這項功能可讓您設定實驗室排程的原則，從完全無法控制，以至完全控制實驗室使用者。 身為實驗室擁有者，您可以採取下列步驟來設定此原則：

1. 在實驗室首頁上，選取 [設定與原則]。
2. 左側功能表的 [排程] 區段中選取 [自動關機原則]。
3. 選取其中一個選項。 下列各節提供有關這些選項的更多詳細資料:設定原則只適用于在實驗室中建立的新 Vm, 而不會套用至已存在的 Vm。 

    ![自動關閉原則選項](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

## <a name="configure-autoshutdown-settings-for-a-lab"></a>設定實驗室的 autoshutdown 設定
Autoshutdown 原則可讓您指定此實驗室的 Vm 關閉時間, 協助將實驗室浪費降至最低。

若要檢視 (及變更) 實驗室的原則，請依照下列步驟操作：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取 [所有服務]，然後從清單中選取 [DevTest Labs]。
3. 從實驗室清單中，選取所需的實驗室。   
4. 選取 [組態和原則]。

    ![原則設定窗格](./media/devtest-lab-set-lab-policy/policies-menu.png)
5. 在實驗室的 [設定**與原則**] 窗格中, 選取 [排程] 底下的 [**自動關閉**]。
   
    ![Autoshutdown](./media/devtest-lab-set-lab-policy/auto-shutdown.png)
6. 選取 [開啟] 來啟用此原則，以及選取 [關閉] 來停用它。
7. 如果您啟用這個原則，請指定時間 (和時區) 以關閉目前實驗室中的所有 VM。
8. 針對在指定 autoshutdown 時間之前15分鐘傳送通知的選項, 指定 **[是] 或 [** **否**]。 如果您選擇 [是]，請輸入 Webhook URL 端點或電子郵件地址，指定您要在哪裡張貼或傳送通知。 使用者會收到通知，並且給予延遲關機的選項。 如需詳細資訊, 請參閱[通知](#notifications)一節。 
9. 選取 [ **儲存**]。

    根據預設，這個原則一經啟用，就會套用到目前實驗室的所有 VM。 若要從特定的 VM 移除此設定, 請開啟 VM 的管理窗格並變更其**Autoshutdown**設定。

## <a name="configure-autoshutdown-settings-for-a-vm"></a>設定 VM 的 autoshutdown 設定

### <a name="user-sets-a-schedule-and-can-opt-out"></a>使用者可設定排程，且可退出
如果已針對實驗室設定此原則選項, 使用者可以覆寫或選擇不進行實驗室排程。 此選項可讓實驗室使用者完全控制其 VM 的自動關機排程。 實驗室使用者在其 VM 自動關機排程頁面中看不到任何變更。

![自動關閉原則選項-1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>使用者可設定排程，但無法退出
如果已針對實驗室設定此原則選項, 使用者就可以覆寫實驗室排程。 不過，他們無法選擇不使用自動關機原則。 此選項可確保您實驗室中的每部機器都會依循自動關機排程。 實驗室使用者可以更新其 VM 的自動關機排程，以及設定關機通知。

![自動關閉原則選項-2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>使用者無法控制實驗室管理員所設定的排程
如果已針對實驗室設定此原則選項, 使用者就無法覆寫或選擇不進行實驗室排程。 此選項可讓實驗室管理員完全控制實驗室中每部機器的排程。 實驗室使用者只可以設定其 VM 的自動關機通知。

![自動關閉原則選項-3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

## <a name="notifications"></a>通知
一旦由實驗室擁有者設定 autoshutdown 之後, 如果有任何 Vm 會受到影響, 通知就會在 autoshutdown 觸發前15分鐘傳送給實驗室使用者。 此選項可讓實驗室使用者有機會在關閉前儲存其工作。 此通知也會提供每個 VM 的連結, 以進行下列動作:

- 在此時間略過 autoshutdown
- 將 autoshutdown 延遲一小時或2小時, 讓它們可以繼續在 VM 上運作。

通知會透過設定的網頁攔截端點或實驗室擁有者在 autoshutdown 設定中所指定的電子郵件地址傳送。 Webhook 可讓您建立或設定訂閱特定事件的整合。 觸發其中一個事件時, DevTest Labs 會將 HTTP POST 承載傳送至 webhook 的已設定 URL。 如需 webhook 的詳細資訊，請參閱[建立 webhook 或 API Azure 函式](../azure-functions/functions-create-a-web-hook-or-api-function.md)。 

我們建議您使用 web 勾點, 因為各種應用程式 (例如, 時差、Azure Logic Apps 等等) 都有廣泛的支援。這可讓您執行自己的傳送通知方式。 如需使用 Azure Logic Apps 從電子郵件接收 autoshutdown 通知的範例, 請參閱[建立可接收電子郵件通知的邏輯應用程式](devtest-lab-auto-shutdown.md#create-a-logic-app-that-receives-email-notifications)。 


## <a name="next-steps"></a>後續步驟
請參閱[在 Azure DevTest Labs 中管理實驗室的 autoshutdown 原則](devtest-lab-auto-shutdown.md)
