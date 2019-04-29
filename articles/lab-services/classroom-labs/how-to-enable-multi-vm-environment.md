---
title: 啟用 Azure 實驗室服務中的多個 VM 環境 |Microsoft Docs
description: 了解如何使用範本中的虛擬機器的 Azure 實驗室服務教室實驗室內的多個虛擬機器建立環境。
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
ms.date: 03/18/2019
ms.author: spelluru
ms.openlocfilehash: 6faf32232c42f863bff52fdfb3c0714aee8e9b88
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60702394"
---
# <a name="create-an-environment-with-multiple-vms-inside-a-template-vm-of-a-classroom-lab"></a>建立教室實驗室 VM 的範本內的多個 Vm 環境
目前 Azure 實驗室服務可讓您將在實驗室中的其中一個範本虛擬機器設定，並將一份複本提供給每個使用者。 但是，如果您是教授，教導如何設定防火牆或伺服器上的 IT 類別時，您可能需要在其中多部虛擬機器可以彼此通訊透過網路環境提供每位學生。

巢狀虛擬化可讓您建立實驗室範本的虛擬機器內的多個 VM 環境。 發行範本會提供在實驗室中的每位使用者使用其內的多個 Vm 設定虛擬機器。

## <a name="what-is-nested-virtualization"></a>什麼是巢狀虛擬化？
巢狀虛擬化可讓您建立的虛擬機器中的虛擬機器。 巢狀虛擬化透過 HYPER-V，並僅可用於 Windows Vm。

如需巢狀虛擬化的詳細資訊，請參閱下列文章：

- [在 Azure 中的巢狀虛擬化](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
- [如何啟用 Azure VM 中的巢狀虛擬化](../../virtual-machines/windows/nested-virtualization.md)

## <a name="use-nested-virtualization-in-azure-lab-services"></a>Azure 實驗室服務中使用巢狀虛擬化
重要的步驟如下：

1. 建立**大型**大小**Windows**實驗室範本電腦。 
2. 連接到它並[啟用巢狀虛擬化](../../virtual-machines/windows/nested-virtualization.md)。


下列程序可讓您詳細的步驟： 

1. 如果您還沒有，請建立實驗室帳戶。 如需指示，請參閱[教學課程：設定實驗室帳戶使用 Azure 實驗室服務](tutorial-setup-lab-account.md)。
2. 瀏覽至 [Azure 實驗室服務網站](https://labs.azure.com)。 
3. 選取 [登入]，然後輸入您的認證。 Azure 實驗室服務支援組織帳戶和 Microsoft 帳戶。 
4. 在 [新增實驗室] 視窗中，執行下列動作： 
    1. 指定實驗室的**名稱**。 
    2. 指定實驗室中的**虛擬機器數目**上限。 建立實驗室之後或在現有的實驗室中，您可以增加或減少 VM 數目。 如需詳細資訊，請參閱[更新實驗室中的 VM 數目](how-to-configure-student-usage.md#update-number-of-virtual-machines-in-lab)。
    6. 選取 [ **儲存**]。

        ![建立教室實驗室](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. 在 [選取虛擬機器規格] 頁面上，執行下列步驟：
    1. 選取 **大**大小的虛擬機器 (Vm)，在實驗室中建立。 目前，只有大型支援巢狀虛擬化。
    2. 選擇的虛擬機器映像**Windows 映像**。 只有在 Windows 機器上使用巢狀虛擬化。 
    3. 選取 [下一步] 。

        ![指定 VM 規格](../media/how-to-enable-multi-vm-environment/large-windows-vm.png)    
5. 在 [設定認證] 頁面上，指定實驗室中所有 VM 的預設認證。 
    1. 指定實驗室中所有 VM 的 [使用者名稱]。
    2. 指定使用者的 [密碼]。 

        > [!IMPORTANT]
        > 請記下使用者名稱和密碼。 它們將不會再次顯示。
    3. 選取 [建立] 。 

        ![設定認證](../media/tutorial-setup-classroom-lab/set-credentials.png)
6. 在 [設定範本] 頁面上，您會看到實驗室建立程序的狀態。 在實驗室中建立範本最多需要 20 分鐘。 

    ![設定範本](../media/tutorial-setup-classroom-lab/configure-template.png)
7. 範本的設定完成後，您會看到下列頁面： 

    ![在範本頁面完成後加以設定](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)
8. 在 **設定範本**頁面上，選取**Connect**連接到的範本來設定巢狀虛擬化的 VM。 您也可以設定更新版本之後完成此精靈中的步驟。 
9. 在範本的虛擬機器，設定 巢狀虛擬化並設定虛擬網路與多部虛擬機器。 如需詳細的逐步指示，請參閱 <<c0> [ 如何啟用 Azure VM 中的巢狀虛擬化](../../virtual-machines/windows/nested-virtualization.md)。 以下是步驟的快速摘要： 
    1. 啟用 HYPER-V 功能，在範本的虛擬機器。
    2. 設定巢狀的虛擬機器的網際網路連線的內部虛擬網路
    3. 建立虛擬機器，透過 HYPER-V 管理員
    4. 將 IP 位址指派給虛擬機器
10. 選取 [範本] 頁面上的 [下一步]。 
11. 在 [發佈範本] 頁面上，執行下列動作。 
    1. 若要立即發佈範本，請選取 [發佈]。  

        > [!WARNING]
        > 在您發佈時，即無法取消發佈。 
    2. 若要稍後再發佈，請選取 [儲存以便稍後使用]。 您可以在精靈完成之後發佈範本 VM。 如需如何在精靈完成之後設定和發佈的詳細資訊，請參閱[如何管理教室實驗室](how-to-manage-classroom-labs.md)一文中的[發佈範本](how-to-create-manage-template.md#publish-the-template-vm)小節。

        ![發佈範本](../media/how-to-enable-multi-vm-environment/publish-template-page.png)
11. 您會看到範本的**發佈進度**。 此程序最多可能需要一小時。 

    ![發佈範本 - 進度](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. 範本成功發佈後，您會看到下列頁面。 選取 [完成] 。

    ![發佈範本 - 成功](../media/tutorial-setup-classroom-lab/publish-success.png)
1. 您會看到實驗室的**儀表板**。 
    
    ![教室實驗室儀表板](../media/how-to-enable-multi-vm-environment/dashboard.png)


## <a name="next-steps"></a>後續步驟

現在，每個使用者會取得包含多個 VM 環境內的單一虛擬機器。 若要了解如何將使用者新增至實驗室，並將註冊連結傳送給他們，請參閱下列文章：[將使用者新增至實驗室](tutorial-setup-classroom-lab.md#add-users-to-the-lab)。