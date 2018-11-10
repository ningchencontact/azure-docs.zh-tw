---
title: 管理 Azure 實驗室服務中的教室實驗室範本 | Microsoft Docs
description: 了解如何在 Azure 實驗室服務中建立和管理教室實驗室範本。
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
ms.date: 10/25/2018
ms.author: spelluru
ms.openlocfilehash: 3ecbef3b3063ceb413b852f8000b44a85d28d08e
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2018
ms.locfileid: "50142719"
---
# <a name="create-and-manage-a-classroom-template-in-azure-lab-services"></a>在 Azure 實驗室服務中建立和管理教室範本
實驗室中的範本是所有使用者的虛擬機器據以建立的基底虛擬機器映像。 設定範本虛擬機器以便能確實設定您要提供給實驗室使用者的虛擬機器。 您可以提供要給實驗室使用者查看的範本名稱與描述。 然後，發佈範本以便讓範本 VM 的執行個體可供實驗室使用者使用。 當您發佈範本時，Azure 實驗室服務會使用範本在實驗室中建立 VM。 在此程序中建立的 VM 數目與實驗室中允許的使用者數量上限相同 (此上限可在實驗室的使用原則中設定)。 所有虛擬機器都有與範本相同的設定。

本文說明如何在 Azure 實驗室服務的教室實驗室中，建立和管理範本虛擬機器。 

## <a name="publish-a-template-while-creating-a-classroom-lab"></a>在建立教室實驗室時發佈範本
首先，您可以在建立教室實驗室時設定和發佈範本。

1. 瀏覽至 [Azure 實驗室服務網站](https://labs.azure.com)。 
2. 選取 [登入]，然後輸入您的認證。 Azure 實驗室服務支援組織帳戶和 Microsoft 帳戶。 
3. 在 [新增實驗室] 視窗中，執行下列動作： 
    1. 指定實驗室的**名稱**。 
    2. 指定實驗室中允許的**使用者數目**上限。 
    6. 選取 [ **儲存**]。

        ![建立教室實驗室](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. 在 [選取虛擬機器規格] 頁面上，執行下列步驟：
    1. 選取在實驗室中建立的虛擬機器 (VM) [大小]。 
    2. 選取您要在其中建立 VM 的 [區域]。 
    3. 選取要用來在實驗室中建立 VM 的 [VM 映像]。 
    4. 選取 [下一步] 。

        ![指定 VM 規格](../media/tutorial-setup-classroom-lab/select-vm-specifications.png)    
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
8. 以下是本教學課程中的選擇性步驟： 
    1. 選取 [啟動] 以啟動範本 VM。
    2. 選取 [連線] 以連線至範本 VM。 
    3. 在您的範本 VM 上安裝並設定軟體。 
    4. **停止** VM。  
    5. 輸入範本的 [描述]

        ![接著在 [設定範本] 頁面中](../media/tutorial-setup-classroom-lab/configure-template-next.png)
9. 選取 [範本] 頁面上的 [下一步]。 
10. 在 [發佈範本] 頁面上，執行下列動作。 
    1. 若要立即發佈範本，請選取 [我了解我無法在發佈之後修改範本。*此程序只能執行一次，最多可能需要一個小時]* 核取方塊，然後選取 [發佈]。  

        > [!WARNING]
        > 在您發佈時，即無法取消發佈。 
    2. 若要稍後再發佈，請選取 [儲存以便稍後使用]。 您可以在精靈完成之後發佈範本 VM。 如需如何在精靈完成之後設定和發佈的詳細資訊，請參閱[如何管理教室實驗室](how-to-manage-classroom-labs.md)一文中的[發佈範本](#publish-the-template)小節。

        ![發佈範本](../media/tutorial-setup-classroom-lab/publish-template.png)
11. 您會看到範本的**發佈進度**。 此程序最多可能需要一小時。 

    ![發佈範本 - 進度](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. 範本成功發佈後，您會看到下列頁面。 選取 [完成] 。

    ![發佈範本 - 成功](../media/tutorial-setup-classroom-lab/publish-success.png)
1. 您會看到實驗室的**儀表板**。 
    
    ![教室實驗室儀表板](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)

## <a name="set-up-a-template-after-creating-a-lab"></a>在建立實驗室之後設定範本 
您也可以在建立實驗室之後設定範本。   

### <a name="set-template-title-and-description"></a>設定範本標題和描述
1. 在 [範本] 區段中，針對範本選取 [編輯] (鉛筆圖示)。 
2. 在 [使用者檢視] 視窗中，輸入範本的**標題**。
3. 輸入範本的**描述**。
4. 選取 [ **儲存**]。

    ![教室實驗室描述](../media/how-to-manage-classroom-labs/lab-description.png)

### <a name="set-up-the-template-vm"></a>設定範本 VM
 您可連線到範本 VM，在其上安裝任何必要的軟體，再將它提供給您的學生使用。 

1. 請等候範本虛擬機器準備就緒。 準備就緒時，應會啟用 [啟動] 按鈕。 若要啟動 VM，請選取 [啟動]。

    ![啟動範本 VM](../media/tutorial-setup-classroom-lab/start-template-vm.png)
1. 若要連線到 VM，請選取 [連線]，然後依照指示執行。 

    ![連線至範本 VM](../media/tutorial-setup-classroom-lab/connect-template-vm.png)
1. 安裝學生使用實驗室所需的任何軟體 (例如，Visual Studio、Azure 儲存體總管等)。 
2. 中斷範本 VM 的連線 (關閉您的遠端桌面工作階段)。 
3. 選取 [停止] 以**停止** VM 範本。 

    ![停止範本 VM](../media/tutorial-setup-classroom-lab/stop-template-vm.png)


## <a name="publish-the-template"></a>發佈範本 
如果您未在建立實驗室時發行範本，也可以之後再發佈。 在發佈之前，請先連線至範本 VM，然後為其更新軟體。 當您發佈範本時，Azure 實驗室服務會使用範本在實驗室中建立 VM。 在此程序中建立的 VM 數目與實驗室中允許的使用者數量上限相同 (此上限可在實驗室的使用原則中設定)。 所有虛擬機器都有與範本相同的設定。 

1. 選取 [範本] 區段中的 [發佈]。 

    ![發佈範本 VM](../media/tutorial-setup-classroom-lab/public-access.png)
1. 在 [發佈範本] 訊息方塊上檢閱訊息，然後選取 [發佈]。 視要建立多少 VM (這與實驗室中允許的使用者數目相同) 而定，此程序可能需要一些時間。
    
    > [!IMPORTANT]
    > 範本發行後就無法取消發行。 但可以重新發佈範本。 
4. 切換到 [虛擬機器] 頁面，確認您看到虛擬機器處於 [未指派] 狀態。 這些虛擬機器尚未指派給任何學生。 

    ![虛擬機器](../media/tutorial-setup-classroom-lab/virtual-machines.png)
5. 請等候 VM 建立。 它們應處於 [已停止] 狀態。 您可以在此頁面上啟動學生 VM、連線到 VM、停止 VM，以及刪除 VM。 您可以在此頁面啟動 VM，或是讓學生啟動 VM。 

    ![已停止狀態的虛擬機器](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)


## <a name="republish-the-template"></a>重新發佈範本 
在發佈範本後，您仍可連線至 VM 範本、予以更新，然後重新發佈。 當您重新發佈範本 VM 時，所有使用者 VM 都會先中斷連線，再根據更新後的範本加以重新建立。 

1. 在實驗室的 [儀表板] 頁面上，選取 [範本] 區段中的 [重新發佈]。 

    ![[重新發佈] 按鈕](../media/how-to-create-manage-template/republish-button.png)
2. 在 [重新發佈範本] 訊息方塊上檢閱文字，然後選取 [重新發佈] 以便繼續。 否則，請選取 [取消]。 

    ![重新發佈範本訊息](../media/how-to-create-manage-template/republish-template-message.png)
3. 您會在 [範本] 區段的圖格上看到重新發佈的狀態。

    ![重新發佈的狀態](../media/how-to-create-manage-template/republish-status-publishing.png)
4. 範本在發佈後，狀態會設定為 [已發佈]。 

    ![重新發佈成功](../media/how-to-create-manage-template/republish-success.png)

## <a name="next-steps"></a>後續步驟
使用 Azure 實驗室服務以設定實驗室的入門：

- [設定教室實驗室](how-to-manage-classroom-labs.md)
- [設定實驗室](../tutorial-create-custom-lab.md)
