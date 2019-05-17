---
title: 註冊 Azure NetApp Files | Microsoft Docs
description: 描述如何使用 Azure NetApp 檔案註冊。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: b-juche
ms.openlocfilehash: fbe0b82008d7b15332c4e2cd62c49c611f20fe89
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2019
ms.locfileid: "65794702"
---
# <a name="register-for-azure-netapp-files"></a>註冊 Azure NetApp Files

> [!IMPORTANT] 
> 再註冊 Azure NetApp 檔案資源提供者，您一定會收到一封電子郵件確認您已獲得服務的存取權的 Azure NetApp 檔案小組。 

在這篇文章，了解如何註冊 Azure NetApp 檔案，以便您可以開始使用服務。

## <a name="waitlist"></a>提交的等候清單要求存取服務

1. 用於存取 Azure NetApp 檔案服務透過等候清單要求提交[Azure NetApp 檔案等候清單提交頁面](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u)。 

    等候清單註冊並不保證立即服務存取。 

2. 與其他工作繼續之前，以等候來自 Azure NetApp 檔案團隊的官方的確認電子郵件。 

## <a name="resource-provider"></a>註冊 NetApp 資源提供者

若要使用服務，您必須註冊 Azure NetApp Files 的 Azure 資源提供者。

> [!NOTE] 
> 您將能夠成功註冊 NetApp 資源提供者，即使沒有被授與存取服務。 不過，存取授權，而任何的 Azure 入口網站或建立 NetApp 帳戶或任何其他 Azure NetApp 檔案資源的 API 要求將會遭到拒絕，發生下列錯誤：  
>
> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"NotFound","message":"{\r\n \"error\": {\r\n \"code\": \"InvalidResourceType\",\r\n \"message\": \"The resource type could not be found in the namespace 'Microsoft.NetApp' for api version '2017-08-15'.\"\r\n }\r\n}"}]}`


1. 在 Azure 入口網站中，按一下右上角的 Azure Cloud Shell 圖示：

      ![Azure Cloud Shell 圖示](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell.png)

2. 如果您的 Azure 帳戶中有多個訂用帳戶，請選取已列入 Azure NetApp Files 允許清單中的訂用帳戶：
    
        az account set --subscription <subscriptionId>

3. 在 Azure Cloud Shell 主控台中輸入下列命令，以驗證您的訂用帳戶已列入白清單中：
    
        az feature list | grep NetApp

   命令輸出顯示如下：
   
       "id": "/subscriptions/<SubID>/providers/Microsoft.Features/providers/Microsoft.NetApp/features/publicPreviewADC",  
       "name": "Microsoft.NetApp/publicPreviewADC" 
       
   `<SubID>` 是您的訂用帳戶識別碼。

    如果您看不到功能名稱`Microsoft.NetApp/publicPreviewADC`，您沒有服務的存取權。 在此步驟中停止。 遵循指示[提交存取服務的等候清單要求](#waitlist)要求服務存取，然後再繼續。 

4. 在 Azure Cloud Shell 中輸入下列命令，以註冊 Azure 資源提供者： 
    
        az provider register --namespace Microsoft.NetApp --wait

   `--wait` 參數會指示主控台等候註冊完成。 註冊程序可能需要一些時間才能完成。

5. 在 Azure Cloud Shell 中輸入下列命令，以驗證 Azure 資源提供者已註冊： 
    
        az provider show --namespace Microsoft.NetApp

   命令輸出顯示如下：
   
        {
        "id": "/subscriptions/<SubID>/providers/Microsoft.NetApp",
        "namespace": "Microsoft.NetApp", 
        "registrationState": "Registered", 
        "resourceTypes": […. 

   `<SubID>` 是您的訂用帳戶識別碼。  `state` 參數值會指出 `Registered`。

6. 從 Azure 入口網站中，按一下 [訂用帳戶] 刀鋒視窗。
7. 在 [訂用帳戶] 刀鋒視窗中，按一下您的訂用帳戶識別碼。 
8. 在訂用帳戶設定中按一下 [資源提供者]，以驗證 Microsoft.NetApp 提供者顯示為 [已註冊] 狀態： 

      ![已註冊的 Microsoft.NetApp](../media/azure-netapp-files/azure-netapp-files-registered-resource-providers.png)


## <a name="next-steps"></a>後續步驟

[建立 NetApp 帳戶](azure-netapp-files-create-netapp-account.md)