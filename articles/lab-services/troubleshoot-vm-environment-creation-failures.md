---
title: 疑難排解 VM 和環境建立失敗 Azure DevTest Labs |Microsoft Docs
description: 了解如何針對虛擬機器 (VM) 和 Azure DevTest Labs 中的環境建立失敗進行疑難排解。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2019
ms.author: spelluru
ms.openlocfilehash: 985f28e9a29ebd20abb1db17e6597b2942c9b0c0
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2019
ms.locfileid: "59551123"
---
# <a name="troubleshoot-virtual-machine-vm-and-environment-creation-failures-in-azure-devtest-labs"></a>針對虛擬機器 (VM) 和 Azure DevTest Labs 中的環境建立失敗進行疑難排解
DevTest Labs 會提供警告的電腦名稱是否無效，或您即將違反實驗室原則。 有時候，您會看到紅色`X`旁您的實驗室 VM 或環境會通知您發生錯誤的狀態。  本文章提供您可用來尋找潛在問題，並希望避免未來發生問題的一些技巧。 

## <a name="portal-notifications"></a>入口網站通知
如果您使用 Azure 入口網站，查看的第一個位置是**通知面板**。  通知 面板中，按一下主要工具列上的可用**鈴鐺圖示**，會告訴您實驗室的 VM 或環境的建立期間是否已成功。  如果發生失敗，您會看到建立失敗相關聯的錯誤訊息。 通常，詳細資料進一步提供可協助您解決問題的資訊。 在下列範例中，虛擬機器建立失敗，因為核心不足。 詳細的訊息會告訴您如何修正此問題，並要求增加核心配額。

![Azure 入口網站通知](./media/troubleshoot-vm-environment-creation-failures/portal-notification.png)


## <a name="activity-logs"></a>活動記錄 
如果您要調查失敗的一段時間後嘗試建立您的 VM 或環境，請查看活動記錄檔。 本節將說明如何尋找 Vm 及環境的記錄檔。 

## <a name="activity-logs-for-virtual-machines"></a>虛擬機器的活動記錄

1. 在您的實驗室的首頁上，選取 啟動 VM**虛擬機器**頁面。
2. 在上**虛擬機器**頁面上，於**監視**的左側功能表中選取的區段**活動記錄檔**若要查看所有與 VM 相關聯的記錄檔。  
3. 在活動記錄檔項目中，選取失敗的作業。 一般而言，失敗的作業稱為`Write Virtualmachines`。   
4. 在右窗格中，切換至 [JSON] 索引標籤。您會看到記錄檔的 [JSON] 檢視中的詳細資料。

    ![活動記錄檔以取得 VM](./media/troubleshoot-vm-environment-creation-failures/vm-activity-log.png)
5. 瀏覽的 JSON 記錄檔，直到您找到`statusMessage`屬性。 它可讓您的主要的錯誤訊息和其他相關詳細資訊，如果適用的話。 下列 JSON 會是本文稍早所示的範例如加上引號的核心超過時發生。

    ```json
    "properties": { 
        "statusCode": "Conflict", 
        "statusMessage": "{\"status\":\"Failed\",\"error\":{\"code\":\"ResourceDeploymentFailure\",\"message\":\"The resource operation completed with terminal provisioning state 'Failed'.\",\"details\":[{\"code\":\"OperationNotAllowed\",\"message\":\"Operation results in exceeding quota limits of Core. Maximum allowed: 100, Current in use: 100, Additional requested: 8. Please read more about quota increase at http://aka.ms/corequotaincrease.\"}]}}", 
    }, 
    ```

## <a name="activity-log-for-an-environment"></a>適用於環境的活動記錄檔

若要查看環境建立的活動記錄檔，請遵循下列步驟：

1. 在您的實驗室的首頁上，選取**組態和原則**左側功能表上。
2. 在 **組態和原則**頁面上，選取**活動記錄**功能表上。 
3. 尋找記錄檔中的活動清單中失敗，並加以選取。 
4. 在右窗格中，切換至 [JSON] 索引標籤，並尋找**statusMessage**。 

    ![環境活動記錄檔](./media/troubleshoot-vm-environment-creation-failures/envirionment-activity-log.png)

## <a name="resource-manager-template-deployment-logs"></a>Resource Manager 範本部署記錄檔
如果您的環境或虛擬機器透過自動化建立，但是沒有一個要查看錯誤資訊的最後一個位置。 這是 Azure Resource Manager 範本部署記錄檔。 透過自動化建立實驗室資源時，通常會透過 Azure Resource Manager 範本部署完成。 請參閱[https://github.com/Azure/azure-devtestlab/tree/master/Samples ](https://github.com/Azure/azure-devtestlab/tree/master/Samples)建立 DevTest Labs 資源的範例 Azure Resource Manager 範本。 

若要查看實驗室範本部署記錄檔，請遵循下列步驟：

1. 啟動實驗室所在的資源群組的頁面。
2. 選取 **部署**下方的左側功能表上**設定**。 
3. 尋找部署失敗的狀態，並加以選取。 
4. 在 **部署**頁面上，選取**作業詳細資料**連結失敗的作業。 
5. 您會看到在失敗作業的相關詳細資料**作業詳細資料**視窗。 

## <a name="next-steps"></a>後續步驟
請參閱[針對構件失敗進行疑難排解](devtest-lab-troubleshoot-artifact-failure.md)