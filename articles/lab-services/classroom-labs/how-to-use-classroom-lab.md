---
title: 如何存取 Azure 實驗室服務中的教室實驗室 | Microsoft Docs
description: 在本教學課程中，您會在由教師設定的教室實驗室中存取虛擬機器。
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: 8ea1afbfb90a55ec1c34f5c59cf38ea5417118ff
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/17/2019
ms.locfileid: "54390903"
---
# <a name="how-to-access-a-classroom-lab-in-azure-lab-services"></a>如何存取 Azure 實驗室服務中的教室實驗室
本文將說明如何存取教室實驗室、連線到實驗室中的 VM，以及停止 VM。 

## <a name="register-to-a-lab"></a>向實驗室註冊
1. 瀏覽至教師/授課者提供給您的**註冊 URL**。 
2. 使用學校帳戶登入服務，以完成註冊。 
3. 註冊之後，請確認您有看到可存取的實驗室虛擬機器。 
2. 等候虛擬機器就緒，然後**啟動** VM。 這個程序需要一些時間。  

    ![啟動 VM](../media/tutorial-connect-vm-in-classroom-lab/start-vm.png)


## <a name="view-all-the-classroom-labs"></a>檢視所有教室實驗室
向實驗室註冊之後，您可以採取下列步驟來檢視所有教室實驗室： 

1. 瀏覽至 [https://labs.azure.com](https://labs.azure.com)。 
2. 使用您用來向實驗室註冊的使用者帳戶登入服務。 
3. 確認您看到您有權存取的所有實驗室。 

    ![檢視所有連結](../media/how-to-use-classroom-lab/all-labs.png)

## <a name="connect-to-the-virtual-machine-in-a-classroom-lab"></a>連線到教室實驗室中的虛擬機器

1. 如果 VM 尚未啟動，請選取 [啟動]。
2. 在您要存取的實驗室虛擬機器圖格上，選取 [連線]。 

    ![檢視所有連結](../media/how-to-use-classroom-lab/connect-button.png)
3. 將 RDP 檔案 (適用於 Windows VM) 儲存至硬碟並加以開啟。 
4. 使用授課者/教師提供給您的**使用者名稱**和**密碼**來登入機器。 

## <a name="stop-the-virtual-machine-in-a-classroom-lab"></a>停止教室實驗室中的虛擬機器

若要停止您的 VM，請選取圖格上的 [停止]。 當 VM 停止時，在圖格上的 [啟動] 按鈕會啟用。 

## <a name="next-steps"></a>後續步驟
請參閱下列文章：

- [以管理員身分建立及管理實驗室帳戶](how-to-manage-lab-accounts.md)
- [以實驗室擁有者身分建立及管理實驗室](how-to-manage-classroom-labs.md)
- [以實驗室擁有者身分設定及發佈範本](how-to-create-manage-template.md)
- [以實驗室擁有者身分設定及控制實驗室的使用方式](how-to-configure-student-usage.md)
 