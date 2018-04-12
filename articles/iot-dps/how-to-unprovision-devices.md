---
title: 如何解除佈建使用 Azure IoT 中樞裝置佈建服務註冊的裝置 | Microsoft Docs
description: 如何在 Azure 入口網站中，將裝置佈建服務所註冊的裝置解除佈建
services: iot-dps
keywords: ''
author: JimacoMS
ms.author: v-jamebr
ms.date: 01/08/2018
ms.topic: article
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 1d057a4df43cf25e6817672d198207d9a50e462e
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/03/2018
---
# <a name="how-to-unprovision-devices-enrolled-by-your-provisioning-service"></a>如何將佈建服務所註冊的裝置解除佈建

您可能會發現，有時必須將已透過裝置佈建服務佈建的裝置解除佈建。 比方說，裝置可能會售出或移至不同的 IoT 中樞，或者可能遺失、遭竊或發生其他遭破壞的狀況。 

一般而言，解除佈建裝置包含兩個步驟：

1. 撤銷裝置對佈建服務的存取權限。 端看您想要暫時還是永久撤銷存取權，或者對 X.509 證明機制而言，在現有的註冊群組階層上，您可能會想要停用或刪除註冊項目。 
 
   - 若要了解如何使用入口網站撤銷裝置的存取權限，請參閱[撤銷裝置存取](how-to-revoke-device-access-portal.md)。
   - 若要了解如何以程式設計方式使用其中一種佈建服務 SDK 來撤銷裝置的存取權限，請參閱[使用服務 SDK 管理裝置註冊](how-to-manage-enrollments-sdks.md)。

2. 停用或刪除身分識別登錄中對於所佈建的 IoT 中樞登錄的裝置項目。 若要進一步了解，請參閱 Azure IoT 中樞文件的[管理裝置身分識別](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-devguide-identity-registry#disable-devices)。 

解除佈建裝置的確切步驟取決於其證明機制，以及其適用的註冊項目與您的佈建服務。

## <a name="individual-enrollments"></a>個別註冊
使用 TPM 證明或 X.509 證明搭配分葉憑證的裝置會透過個別的註冊項目進行佈建。 

若要解除佈建具有個別註冊的裝置： 
1. 對於使用 TPM 證明的裝置，刪除個別註冊項目以永久撤銷裝置對佈建服務的存取權，或停用該項目以暫時撤銷其存取權。 對於使用 X.509 證明的裝置，您可以刪除或停用項目。 不過請注意，如果您針對使用 X.509 證明的裝置刪除個別註冊，而且該裝置憑證鏈結中的單一憑證有已啟用的註冊群組存在，則該裝置可以重新註冊。 對於這類裝置而言，停用註冊項目可能更安全。 不論裝置的其中一個簽署憑證是否有已啟用的註冊群組存在，這麼做都能防止裝置重新註冊。
2. 在佈建裝置之 IoT 中樞的身分識別登錄中，停用或刪除該裝置。 


## <a name="enrollment-groups"></a>註冊群組
藉由 X.509 證明，也可透過註冊群組佈建裝置。 註冊群組是使用簽署憑證 (中繼或根 CA 憑證) 來加以設定，若裝置的憑證鏈結中具有該憑證，註冊群組會控制裝置對佈建服務的存取權限。 若要深入了解註冊群組以及 X.509 憑證和佈建服務，請參閱 [X.509 憑證](concepts-security.md#x509-certificates)。 

若要檢視已透過註冊群組佈建的裝置清單，您可以檢視註冊群組的詳細資料。 如此便能輕鬆了解每個裝置佈建到哪個 IoT 中樞。 若要檢視裝置清單： 

1. 登入 Azure 入口網站，然後在左側功能表上按一下 [所有資源]。
2. 按一下資源清單中的佈建服務。
3. 在佈建服務中，按一下 [管理註冊]，然後選取 [註冊群組] 索引標籤。
4. 按一下註冊群組將之開啟。

   ![在入口網站中檢視註冊群組項目](./media/how-to-unprovision-devices/view-enrollment-group.png)

對於註冊群組，有兩個案例要加以考慮：

- 若要解除佈建所有透過註冊群組佈建的裝置：
  1. 停用註冊群組，將其簽署憑證列入封鎖清單。 
  2. 請使用該註冊群組的佈建裝置清單，從個別的 IoT 中樞身分識別登錄中停用或刪除每個裝置。 
  3. 從個別的 IoT 中樞停用或刪除所有裝置之後，可以選擇是否要刪除註冊群組。 不過請注意，如果您刪除註冊群組，而憑證鏈結之中一個或多個更上層裝置的簽署憑證有已啟用的註冊群組，這些裝置可以重新註冊。 
- 若要從註冊群組解除佈建單一裝置：
  1. 為其分葉 (裝置) 憑證建立已停用的個別註冊。 如此會撤銷該裝置的佈建服務存取權限，但是仍允許鏈結之中具有註冊群組簽署憑證的其他裝置進行存取。 請勿刪除裝置已停用的個別註冊。 否則會使得裝置透過註冊群組重新註冊。 
  2. 使用該註冊群組的已佈建裝置清單來尋找佈建裝置的 IoT 中樞，再從該中樞的身分識別登錄停用或刪除裝置。 
  
  










