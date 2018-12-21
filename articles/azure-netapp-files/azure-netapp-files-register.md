---
title: 註冊 Azure NetApp Files | Microsoft Docs
description: 說明如何提交在 Azure NetApp Files 服務中進行註冊的要求。
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
ms.topic: get-started-article
ms.date: 11/21/2018
ms.author: b-juche
ms.openlocfilehash: ff28429ba81a97ca85364364a2a432e39aaad380
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2018
ms.locfileid: "53414286"
---
# <a name="register-for-azure-netapp-files"></a>註冊 Azure NetApp Files
在使用 Azure NetApp Files 之前，您必須先提交在 Azure NetApp Files 服務中進行註冊的要求。  註冊之後，您即可註冊使用服務。

## <a name="request-to-enroll-in-the-service"></a>要求在服務中註冊
您必須加入「公開預覽計畫」，並列入可存取 Microsoft.NetApp Azure 資源提供者的白名單中。 如需有關加入「公開預覽」方案的詳細資訊，請參閱 [Azure NetApp Files 公開預覽註冊頁面](http://aka.ms/anfsignup)。 


## <a name="register-the-netapp-resource-provider"></a>註冊 NetApp 資源提供者

若要使用服務，您必須註冊 Azure NetApp Files 的 Azure 資源提供者。 

1. 在 Azure 入口網站中，按一下右上角的 Azure Cloud Shell 圖示：

      ![Azure Cloud Shell 圖示](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell.png)

2. 如果您的 Azure 帳戶中有多個訂用帳戶，請選取已列入 Azure NetApp Files 白名單中的訂用帳戶：
    
        az account set --subscription <subscriptionId>

3. 在 Azure Cloud Shell 主控台中輸入下列命令，以驗證您的訂用帳戶已列入白清單中：
    
        az feature list | grep NetApp

   命令輸出顯示如下：
   
       "id": "/subscriptions/<SubID>/providers/Microsoft.Features/providers/Microsoft.NetApp/features/publicPreviewADC",  
       "name": "Microsoft.NetApp/publicPreviewADC" 
       
   `<SubID>` 是您的訂用帳戶識別碼。

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