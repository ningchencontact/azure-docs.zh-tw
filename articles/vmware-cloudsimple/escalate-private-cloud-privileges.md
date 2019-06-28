---
title: 提高私用雲端權限-Azure CloudSimple VMware 方案
description: 描述如何提升您在 vCenter 中的系統管理功能的私用雲端上的權限
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/05/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d11c88b91b13cca13120a9203e376fdc2c3d6d8d
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332487"
---
# <a name="escalate-private-cloud-vcenter-privileges-from-the-cloudsimple-portal"></a>提高私用雲端 vCenter 的權限從 CloudSimple 入口網站 

系統管理存取您的私用雲端的 vCenter，您可以暫時提升 CloudSimple 權限。  您可以使用提高的權限，來安裝 VMware 解決方案、 新增身分識別來源和管理使用者。

可以在 vCenter SSO 網域上建立新的使用者，並將其授與 vCenter 存取權。  當您建立新的使用者時，將它們加入 CloudSimple 內建群組來存取 vCenter。  如需詳細資訊，請參閱 < [CloudSimple 私用雲端的 VMware vCenter 的權限模型](https://docs.azure.cloudsimple.com/learn-private-cloud-permissions/)。

> [!CAUTION]
> 不進行任何組態變更管理元件。 已呈報的特殊權限狀態期間所採取的動作可能會造成不良影響您的系統，或可能會導致您的系統變得無法使用。

## <a name="sign-in-to-azure"></a>登入 Azure

在 [https://portal.azure.com](https://portal.azure.com) 登入 Azure 入口網站。

## <a name="escalate-privileges"></a>提升權限

1. 存取權[CloudSimple 入口網站](access-cloudsimple-portal.md)。

2. 開啟**資源**頁面上，選取您要提升權限的私用雲端。

3. 在 [摘要] 頁面底部附近**變更 vSphere 權限**，按一下**提報**。

    ![變更 vSphere 權限](media/escalate-private-cloud-privilege.png)

4. 選取 vSphere 使用者類型。  只有 **CloudOwner@cloudsimple.local** 可擴大本機使用者。

5. 從下拉式清單中選取提報時間間隔。 選擇最短的期間，可讓您完成工作。

6. 選取核取方塊，以確認您了解風險。

    ![提升權限對話方塊](media/escalate-private-cloud-privilege-dialog.png)

7. 按一下 [確定]  。

8. 呈報的程序可能需要幾分鐘的時間。 完成時，按一下 [確定]  。

提升權限開始，並會持續到所選間隔的結尾。  您可以執行系統管理工作來登入您的私人雲端的 vCenter。

> [!IMPORTANT]
> 只有一位使用者可以有提升了權限。  您可以提升另一位使用者的權限之前，必須取消提升使用者的權限。

## <a name="extend-privilege-escalation"></a>延伸權限提升

如果您需要更多時間才能完成您的工作，您可以擴充的權限擴大規模期間。  請選擇其他的提升可讓您完成管理工作的時間間隔。

1. 在 **資源** > **私用雲端**CloudSimple 入口網站中，選取您要擴充權限提升的私用雲端。

2. 附近的 [摘要] 索引標籤底部，按一下**擴充權限提升**。

    ![延伸權限提升](media/de-escalate-private-cloud-privilege.png)

3. 從下拉式清單中選取提報的時間間隔。 檢閱新的擴大結束時間。

4. 按一下 **儲存**擴充的間隔。

## <a name="de-escalate-privileges"></a>取消提高權限

系統管理工作完成後，您應該取消提升您的權限。  

1. 在 **資源** > **私用雲端**CloudSimple 入口網站中，選取您要取消提升權限的私用雲端。

2. 按一下 **取消擴大**。

3. 按一下 [確定]  。

> [!IMPORTANT]
> 若要避免發生任何錯誤，登出 vCenter 並取消需要提升權限之後，再次登入。

## <a name="next-steps"></a>後續步驟

* [將 vCenter 身分識別來源設定為使用 Active Directory](https://docs.azure.cloudsimple.com/set-vcenter-identity/)
* 安裝備份解決方案，以便[備份工作負載的虛擬機器](https://docs.azure.cloudsimple.com/backup-workloads-veeam/)