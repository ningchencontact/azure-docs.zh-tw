---
title: 教室實驗室用於訓練-Azure 實驗室服務 |Microsoft Docs
description: 了解如何針對訓練案例使用 Azure DevTest Labs。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: 57ff4e30-7e33-453f-9867-e19b3fdb9fe2
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2019
ms.author: spelluru
ms.openlocfilehash: 4d2ba11181977f1976b5ae933e8b93a92424fa96
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60695267"
---
# <a name="use-classroom-labs-for-trainings"></a>用於訓練的教室實驗室
您可以設定實驗室，以進行訓練。 教室實驗室的 Azure 實驗室服務可讓您建立實驗室以進行訓練，每位受訓者使用相同且隔離的環境進行訓練。 您可以新增原則，以確保訓練環境只會在受訓者需要時才提供給他們使用，且包含足夠的訓練所需資源，例如虛擬機器。 

![教室實驗室](../media/classroom-labs-scenarios/classroom.png)

教室實驗室符合下列需求，才能進行訓練，在任何虛擬環境： 

- 受訓者可以快速佈建其訓練環境
- 每台訓練用機器應該相同
- 受訓者無法看到其他受訓者所建立的 VM
- 確保受訓者無法取得超過訓練所需數量的 VM 以控制成本，並在受訓者不使用 VM 時將其關閉
- 輕易地與每位受訓者共用訓練實驗室
- 一再重複使用訓練實驗室

在本文中，您會了解各種 Azure 實驗室服務功能，可用來符合先前所述的訓練需求和詳細的步驟，您可以依照設定實驗室，以進行訓練。  

## <a name="create-the-lab-account-as-a-lab-account-administrator"></a>身為實驗室帳戶系統管理員建立實驗室帳戶
使用 Azure 實驗室服務的第一個步驟是在 Azure 入口網站中建立實驗室帳戶。 實驗室帳戶管理員建立實驗室帳戶之後，系統管理員會將使用者想要建立實驗室**實驗室建立者**角色。 訓練人員使用適用於要練習這些教學課程的學生的虛擬機器建立實驗室。 如需詳細資訊，請參閱 <<c0> [ 建立和管理實驗室帳戶](how-to-manage-lab-accounts.md)。

## <a name="create-and-manage-classroom-labs"></a>建立和管理教室實驗室
培訓講師，身為實驗室帳戶中的實驗室建立者角色的成員，可以建立一或多個實驗室的實驗室帳戶。 您可以建立並設定 VM 的範本與所有必要的軟體，以執行您的課程練習。 您挑選現成的映像，從可用的映像來建立教室實驗室，然後自訂它安裝為實驗室所需的軟體。 如需詳細資訊，請參閱 <<c0> [ 建立及管理教室實驗室](how-to-manage-classroom-labs.md)。

## <a name="configure-usage-settings-and-policies"></a>設定使用方式設定和原則
實驗室建立者可以新增或移除使用者至實驗室，取得將傳送到設定原則，例如設定每個使用者的個別配額更新適用於實驗室和更多的 Vm 數目的實驗室使用者的註冊連結。 如需詳細資訊，請參閱 <<c0> [ 設定使用方式設定和原則](how-to-configure-student-usage.md)。

## <a name="create-and-manage-schedules"></a>建立和管理排程
排程可讓您設定教室實驗室，使實驗室中的 VM 可在指定的時間自動啟動和關閉。 您可以定義一次性排程或週期性排程。 如需詳細資訊，請參閱 <<c0> [ 建立及管理排程的教室實驗室](how-to-create-schedules.md)。

## <a name="set-up-and-publish-a-template-vm"></a>設定及發佈範本 VM
實驗室中的範本是所有使用者的虛擬機器據以建立的基底虛擬機器映像。 設定 VM 的範本，以便設定完全什麼您想要提供給訓練出席者。 您可以提供要給實驗室使用者查看的範本名稱與描述。 然後，發佈範本以便讓範本 VM 的執行個體可供實驗室使用者使用。 當您發佈範本時，Azure 實驗室服務會使用範本在實驗室中建立 VM。 在此程序中建立的 VM 數目與實驗室中允許的使用者數量上限相同 (此上限可在實驗室的使用原則中設定)。 所有虛擬機器都有與範本相同的設定。 如需詳細資訊，請參閱 <<c0> [ 設定及發行範本的虛擬機器](how-to-create-manage-template.md)。 

## <a name="use-vms-in-the-classroom-lab"></a>使用 Vm 在教室實驗室
學生或訓練出席者至實驗室、 註冊並連接至 VM，以執行的課程練習。 如需詳細資訊，請參閱 <<c0> [ 如何存取教室實驗室](how-to-use-classroom-lab.md)。

## <a name="next-steps"></a>後續步驟
開始在教室實驗室中建立實驗室帳戶，依照文件中的指示：[教學課程：設定實驗室帳戶使用 Azure 實驗室服務](tutorial-setup-lab-account.md)。