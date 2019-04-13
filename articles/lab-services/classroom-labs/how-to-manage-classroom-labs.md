---
title: 管理 Azure 實驗室服務中的教室實驗室 | Microsoft Docs
description: 了解如何建立和設定教室實驗室、檢視所有教室實驗室、與實驗室使用者共用註冊連結，或刪除實驗室。
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
ms.date: 02/07/2019
ms.author: spelluru
ms.openlocfilehash: 332f899f3502f34e46b4f158a6980dc96248140e
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2019
ms.locfileid: "59544019"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>管理 Azure 實驗室服務中的教室實驗室 
本文說明如何建立和刪除教室實驗室。 它也示範如何檢視實驗室帳戶中的所有教室實驗室。 

## <a name="prerequisites"></a>必要條件
若要在實驗室帳戶中設定教室實驗室，您必須是實驗室帳戶中 [實驗室建立者] 角色的成員。 您用來建立實驗室帳戶的帳戶會自動新增至此角色。 實驗室擁有者可以使用下列文章中的步驟，將其他使用者新增至「實驗室建立者」角色：[將使用者新增至實驗室建立者角色](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role)。

## <a name="create-a-classroom-lab"></a>建立教室實驗室

1. 瀏覽至 [Azure 實驗室服務網站](https://labs.azure.com)。 請注意，Internet Explorer 11 尚不支援。 
2. 選取 [登入]。 選取或輸入**使用者識別碼** (其為實驗室帳戶中具有**實驗室建立者**角色的成員)，然後輸入密碼。 Azure 實驗室服務支援組織帳戶和 Microsoft 帳戶。 
3. 在 [新增實驗室] 視窗中，執行下列動作： 
    1. 指定實驗室的**名稱**。 
    2. 指定實驗室中的**虛擬機器數目**上限。 您可以稍後增加或減少實驗室中虛擬機器的數目。 
    6. 選取 [ **儲存**]。

        ![建立教室實驗室](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. 在 [選取虛擬機器規格] 頁面上，執行下列步驟：
    1. 選取在實驗室中建立的虛擬機器 (VM) [大小]。 目前允許使用 [小]、[中]、[大] 及 [GPU] 大小。
    2. 選取您要在其中建立 VM 的 [區域]。 
    3. 選取要用來在實驗室中建立 VM 的 [VM 映像]。 如果您選取 Linux 映像，您會看到可啟用遠端桌面連接的選項。 如需詳細資料，請參閱[啟用 Linux 遠端桌面連線](how-to-enable-remote-desktop-linux.md)。
    4. 選取 [下一步] 。

        ![指定 VM 規格](../media/tutorial-setup-classroom-lab/select-vm-specifications.png)    
5. 在 [設定認證] 頁面上，指定實驗室中所有 VM 的預設認證。 
    1. 指定實驗室中所有 VM 的 [使用者名稱]。
    2. 指定使用者的 [密碼]。 

        > [!IMPORTANT]
        > 請記下使用者名稱和密碼。 它們將不會再次顯示。
    3. 選取 [建立] 。 

        ![設定認證](../media/tutorial-setup-classroom-lab/set-credentials.png)
6. 在 [設定範本] 頁面上，您會看到實驗室建立程序的狀態。 在實驗室中建立範本最多需要 20 分鐘。 實驗室中的範本是所有使用者的虛擬機器據以建立的基底虛擬機器映像。 設定範本虛擬機器以便能確實設定您要提供給實驗室使用者的虛擬機器。  

    ![設定範本](../media/tutorial-setup-classroom-lab/configure-template.png)
7. 範本的設定完成後，您會看到下列頁面： 

    ![在範本頁面完成後加以設定](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)
8. 以下是本教學課程中的選擇性步驟： 
    1. 選取 [啟動] 以啟動範本 VM。
    2. 選取 [連線] 以連線至範本 VM。 如果是 Linux 範本 VM，您可選擇是否要使用 SSH 或 RDP 連線 (若已啟用 RDP)。
    3. 在您的範本 VM 上安裝並設定軟體。 
    4. **停止** VM。  
    5. 輸入範本的 [描述]

        ![接著在 [設定範本] 頁面中](../media/tutorial-setup-classroom-lab/configure-template-next.png)
9. 選取 [範本] 頁面上的 [下一步]。 
10. 在 [發佈範本] 頁面上，執行下列動作。 
    1. 若要立即發佈範本，請選取 [我了解我無法在發佈之後修改範本。*此程序只能執行一次，最多可能需要一個小時]* 核取方塊，然後選取 [發佈]。  發佈範本，讓範本 VM 的執行個體可供實驗室使用者使用。

        > [!WARNING]
        > 在您發佈時，即無法取消發佈。 
    2. 若要稍後再發佈，請選取 [儲存以便稍後使用]。 您可以在精靈完成之後發佈範本 VM。 如需如何在精靈完成之後設定和發佈的詳細資訊，請參閱[如何管理教室實驗室](how-to-manage-classroom-labs.md)一文中的發佈範本小節。

        ![發佈範本](../media/tutorial-setup-classroom-lab/publish-template.png)
11. 您會看到範本的**發佈進度**。 此程序最多可能需要一小時。 

    ![發佈範本 - 進度](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. 範本成功發佈後，您會看到下列頁面。 選取 [完成] 。

    ![發佈範本 - 成功](../media/tutorial-setup-classroom-lab/publish-success.png)
1. 您會看到實驗室的**儀表板**。 
    
    ![教室實驗室儀表板](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)
4. 切換到 [虛擬機器] 頁面，確認您看到虛擬機器處於 [未指派] 狀態。 這些虛擬機器尚未指派給任何學生。 它們應處於 [已停止] 狀態。 您可以在此頁面上啟動學生 VM、連線到 VM、停止 VM，以及刪除 VM。 您可以在此頁面啟動 VM，或是讓學生啟動 VM。 

    ![已停止狀態的虛擬機器](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)


## <a name="view-all-classroom-labs"></a>檢視所有教室實驗室
1. 瀏覽至 [Azure 實驗室服務入口網站](https://labs.azure.com)。
2. 選取 [登入]。 選取或輸入**使用者識別碼** (其為實驗室帳戶中具有**實驗室建立者**角色的成員)，然後輸入密碼。 Azure 實驗室服務支援組織帳戶和 Microsoft 帳戶。 
3. 確認您在所選取的實驗室帳戶中看到所有實驗室。 

    ![所有實驗室](../media/how-to-manage-classroom-labs/all-labs.png)
3. 使用頂端的下拉式清單選取其他的實驗室帳戶。 您會看到所選取實驗室帳戶中的實驗室。 

## <a name="delete-a-classroom-lab"></a>刪除教室實驗室
1. 在實驗室的磚上，選取角落的三個點 (...)。 

    ![選取實驗室](../media/how-to-manage-classroom-labs/select-three-dots.png)
2. 選取 [刪除] 。 

    ![刪除按鈕](../media/how-to-manage-classroom-labs/delete-button.png)
3. 在 [刪除實驗室] 對話方塊中，選取 [刪除]。 

    ![[刪除] 對話方塊](../media/how-to-manage-classroom-labs/delete-lab-dialog-box.png)

## <a name="switch-to-another-classroom-lab"></a>切換到另一個教室實驗室
若要從目前的教室實驗室切換到另一個教室實驗室，請選取頂端實驗室帳戶中的實驗室下拉式清單。

![從頂端的下拉式清單中選取實驗室](../media/how-to-manage-classroom-labs/switch-lab.png)


## <a name="next-steps"></a>後續步驟
請參閱下列文章：

- [以實驗室擁有者身分設定及發佈範本](how-to-create-manage-template.md)
- [以實驗室擁有者身分設定及控制實驗室的使用方式](how-to-configure-student-usage.md)
- [以實驗室使用者的身分存取教室實驗室](how-to-use-classroom-lab.md)

