---
title: "使用 VMware vCenter 標籤分組虛擬機器 | Microsoft Docs"
description: "說明如何在使用 Azure Migrate 服務進行評量之前先建立群組。"
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/12/2017
ms.author: raynew
ms.openlocfilehash: 967c2a1fc0e5144c6c7d5c8c4a81cec3d77ffdb5
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/12/2017
---
# <a name="group-vms-with-vcenter-tagging"></a>使用 VCenter 標籤分組虛擬機器

本文說明如何使用 VMware vCenter 中的標籤功能來建立機器群組，以進行 [Azure Migrate](migrate-overview.md) 評量。 您可以指定建立群組時想要使用的標籤類別。 

## <a name="set-up-tagging"></a>設定標籤

在 Azure Migrate 部署期間，內部部署的 Azure Migrate 虛擬機器會開始探索在 vCenter 伺服器管理之 ESXi 主機上執行的機器。 您必須先設定 vCenter 標籤，才能進行探索流程。

1. 請在 VMware vSphere Web 用戶端內，瀏覽至 vCenter 伺服器。
2. 按一下 [標籤]，即可檢閱目前的所有標籤。
3. 若要標籤虛擬機器，請選取 [相關物件] > [虛擬機器]，然後選取您要標籤的虛擬機器。
4. 在 [摘要] > [標籤] 中，按一下 [指派]。 
5. 按一下 [新增標籤]，並指定標籤的名稱與描述。
6. 若要建立標籤的類別，請在下拉式清單中選取 [新增類別]。
7. 請指定類別的名稱與描述，以及基數。 然後按一下 [確定] 。

    ![虛擬機器標籤](./media/how-to-tag-v-center/vm-tag.png)

## <a name="use-tagging-to-create-groups"></a>使用標籤功能來建立群組

1. 請按 [VMware 評量教學課程](tutorial-assessment-vmware.md#run-the-collector-to-discover-vms)的說明，設定內部部署機器的探索作業。
2. 在 [用來分組的標籤類別] 中，選取評量群組要以之為依據的 vCenter 標籤類別。 Azure Migrate 會自動為選取的類別建立群組。

    

## <a name="next-steps"></a>後續步驟

[設定 VMware 虛擬機器評量](tutorial-assessment-vmware.md)。
