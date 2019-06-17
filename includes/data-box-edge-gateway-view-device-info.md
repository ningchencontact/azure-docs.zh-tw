---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/04/2019
ms.author: alkohli
ms.openlocfilehash: d5af557a62f4bd35c242d334c28a38c3d632f7cf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66161206"
---
1. [連線到 PowerShell 介面](#connect-to-the-powershell-interface)。
2. 使用`Get-HcsApplianceInfo`以取得您裝置的資訊。

    下列範例會示範這個指令程式的使用方式：

    ```
    [10.100.10.10]: PS>Get-HcsApplianceInfo
    
    Id                            : b2044bdb-56fd-4561-a90b-407b2a67bdfc
    FriendlyName                  : DBE-NBSVFQR94S6
    Name                          : DBE-NBSVFQR94S6
    SerialNumber                  : HCS-NBSVFQR94S6
    DeviceId                      : 40d7288d-cd28-481d-a1ea-87ba9e71ca6b
    Model                         : Virtual
    FriendlySoftwareVersion       : Data Box Gateway 1902
    HcsVersion                    : 1.4.771.324
    IsClustered                   : False
    IsVirtual                     : True
    LocalCapacityInMb             : 1964992
    SystemState                   : Initialized
    SystemStatus                  : Normal
    Type                          : DataBoxGateway
    CloudReadRateBytesPerSec      : 0
    CloudWriteRateBytesPerSec     : 0
    IsInitialPasswordSet          : True
    FriendlySoftwareVersionNumber : 1902
    UploadPolicy                  : All
    DataDiskResiliencySettingName : Simple
    ApplianceTypeFriendlyName     : Data Box Gateway
    IsRegistered                  : False
    ```

    以下是一些重要的裝置資訊的摘要資料表：
    
    | 參數                             | 描述                                                                                                                                                  |   |
    |--------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|---|
    | FriendlyName                   | 在裝置部署期間透過本機 web UI 設定的裝置易記名稱。 預設的易記名稱是裝置序號。  |   |
    | SerialNumber                   | 裝置序號是在工廠所指派的唯一號碼。                                                                             |   |
    | 模型                          | 您的資料方塊邊緣或方塊部署資料閘道的裝置的模型。 此模型是方塊部署資料閘道的虛擬和實體的資料方塊的邊緣。                   |   |
    | FriendlySoftwareVersion        | 對應至裝置軟體版本的易記字串。 執行預覽的系統，好易軟體版本會是資料方塊邊緣 1902年。 |   |
    | HcsVersion                     | 裝置上執行的 HCS 軟體版本。 比方說，對應至資料方塊邊緣 1902年的 HCS 軟體版本是 1.4.771.324。            |   |
    | LocalCapacityInMb              | 單位是 mb / 裝置的本機容量總計。                                                                                                        |   |
    | IsRegistered                   | 這個值表示是否您的裝置就會啟動服務。                                                                                         |   |


