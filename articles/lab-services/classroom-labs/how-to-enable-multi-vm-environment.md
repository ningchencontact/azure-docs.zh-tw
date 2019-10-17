---
title: 在 Azure 實驗室服務中啟用多個 VM 環境 |Microsoft Docs
description: 瞭解如何在 Azure 實驗室服務的教室實驗室中，使用範本虛擬機器中的多部虛擬機器來建立環境。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2019
ms.author: spelluru
ms.openlocfilehash: 9a86ba803f899e78b2ba9640e6cc317966969e64
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2019
ms.locfileid: "72332371"
---
# <a name="create-an-environment-with-multiple-vms-inside-a-template-vm-of-a-classroom-lab"></a>在教室實驗室的範本 VM 中建立具有多個 Vm 的環境
目前，Azure 實驗室服務可讓您在實驗室中設定一個範本虛擬機器，並將單一複本提供給每個使用者。 但是，如果您是教授如何設定防火牆或伺服器的 IT 課程，您可能需要為每個學生提供一個環境，讓多部虛擬機器可以透過網路彼此溝通。

「嵌套虛擬化」可讓您在實驗室的範本虛擬機器內建立多個 VM 環境。 發佈範本會提供實驗室中的每位使用者，並在其中設定多個 Vm 的虛擬機器。

## <a name="what-is-nested-virtualization"></a>什麼是嵌套的虛擬化？
「嵌套虛擬化」可讓您在虛擬機器中建立虛擬機器。 嵌套虛擬化是透過 Hyper-v 完成，而且只能在 Windows Vm 上使用。

如需有關嵌套虛擬化的詳細資訊，請參閱下列文章：

- [Azure 中的嵌套虛擬化](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
- [如何在 Azure VM 中啟用嵌套虛擬化](../../virtual-machines/windows/nested-virtualization.md)

## <a name="use-nested-virtualization-in-azure-lab-services"></a>在 Azure 實驗室服務中使用嵌套虛擬化
重要步驟如下：

1. 建立適用于實驗室的**大型** **Windows**範本電腦。 
2. 連接到它，並[啟用 [嵌套虛擬化](../../virtual-machines/windows/nested-virtualization.md)]。


下列程式提供詳細的步驟： 

1. 如果您還沒有實驗室帳戶，請建立一個。 如需指示，請參閱[教學課程：使用 Azure 實驗室服務設定實驗室帳戶](tutorial-setup-lab-account.md)。
1. 瀏覽至 [Azure 實驗室服務網站](https://labs.azure.com)。 請注意，目前不支援 Internet Explorer 11。 
2. 選取 [登入]，然後輸入您的認證。 Azure 實驗室服務支援組織帳戶和 Microsoft 帳戶。 
3. 選取 [**新增實驗室**]。 
    
    ![建立教室實驗室](../media/tutorial-setup-classroom-lab/new-lab-button.png)
4. 在 [新增實驗室] 視窗中，執行下列動作： 
    1. 指定實驗室的**名稱**。 
    2. 針對**虛擬機器大小**選取 [**大型（嵌套虛擬化）** ] 或 [**中（嵌套虛擬化）** ]。
    6. 選取您想要使用的 Windows**映射**。 只有在 Windows 機器上才可使用嵌套虛擬化。 
    4. 然後，選取 [下一步]。 

        ![建立教室實驗室](../media/how-to-enable-multi-vm-environment/new-lab-window.png)
    1. 在 [**虛擬機器認證**] 頁面上，為實驗室中的所有 vm 指定預設認證。 指定使用者的**名稱**和**密碼**，然後選取 **[下一步]** 。  

        ![新增實驗室視窗](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

        > [!IMPORTANT]
        > 請記下使用者名稱和密碼。 它們將不會再次顯示。
    3. 在 [**實驗室原則**] 頁面上，輸入在實驗室的排程時間外，為每個使用者配置的時數（**每位使用者的配額**），然後選取 **[完成]** 。 

        ![每位使用者的配額](../media/tutorial-setup-classroom-lab/quota-for-each-user.png)
5. 您應該會看到下列畫面，其中顯示範本 VM 建立的狀態。 在實驗室中建立範本最多需要 20 分鐘。 

    ![建立範本 VM 的狀態](../media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
1. 在 [**範本**] 頁面上，選取工具列上的 [**自訂範本**]。 

    ![自訂範本按鈕](../media/how-to-create-manage-template/customize-template-button.png)
2. 在 [**自訂範本**] 對話方塊中，選取 [**繼續**]。 當您啟動範本並進行變更後，它將不再具有與最後發佈給使用者的虛擬機器相同的設定。 在您再次發佈之前，範本變更不會反映在使用者的現有虛擬機器上。

    ![自訂對話方塊](../media/how-to-create-manage-template/customize-template-dialog.png)
1. 選取工具列上的 **[連線至範本]** 按鈕，以連線至範本 VM 以設定嵌套虛擬化，並遵循指示進行。 如果是 Windows 電腦，您會看到下載 RDP 檔案的選項。 

    ![連線至範本 VM](../media/how-to-create-manage-template/connect-template-vm.png) 
9. 在範本虛擬機器內部，設定「嵌套虛擬化」，並使用多部虛擬機器來設定虛擬網路。 如需詳細的逐步指示，請參閱[如何在 AZURE VM 中啟用嵌套虛擬化](../../virtual-machines/windows/nested-virtualization.md)。 以下是步驟的快速摘要： 
    1. 啟用範本虛擬機器中的 Hyper-v 功能。
    2. 針對嵌套虛擬機器設定具有網際網路連線能力的內部虛擬網路
    3. 透過 Hyper-v 管理員建立虛擬機器
    4. 將 IP 位址指派給虛擬機器
10. 在 [**範本**] 頁面上，選取工具列上的 [**發佈**]。 

    ![發佈範本按鈕](../media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > 在您發佈時，即無法取消發佈。 
8. 在 [**發佈範本**] 頁面上，輸入您要在實驗室中建立的虛擬機器數目，然後選取 [**發佈**]。 

    ![發佈範本-Vm 數目](../media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
11. 您會在頁面上看到發佈範本的**狀態**。 此程序最多可能需要一小時。 

    ![發佈範本 - 進度](../media/tutorial-setup-classroom-lab/publish-template-progress.png)


## <a name="next-steps"></a>後續步驟

現在，每個使用者都會取得包含多個 VM 環境的單一虛擬機器。 若要瞭解如何將使用者新增至實驗室，並將註冊連結傳送給他們，請參閱下列文章：[將使用者新增至實驗室](tutorial-setup-classroom-lab.md#add-users-to-the-lab)。