---
title: "如何管理裝置存取的 Azure IoT 中樞裝置佈建的服務 |Microsoft 文件"
description: "如何撤銷裝置能夠存取 DP 服務在 Azure 入口網站"
services: iot-dps
keywords: 
author: JimacoMS
ms.author: v-jamebr
ms.date: 12/22/2017
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 7985fba68ef2c6f651c64756f8c534928b573de5
ms.sourcegitcommit: 4256ebfe683b08fedd1a63937328931a5d35b157
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/23/2017
---
# <a name="how-to-revoke-device-access-to-your-provisioning-service-in-the-azure-portal"></a>如何撤銷裝置存取您在 Azure 入口網站中的佈建服務

適當的裝置認證管理，是很重要的系統，例如 IoT 解決方案非常重要的。 這類系統的最佳作法是有如何撤銷存取權的情況下，他們的認證，還是 SAS 權杖的 X.509 憑證可能會危害的裝置清除計劃。 此文章說明如何以撤銷存取裝置在佈建步驟。

若要了解已佈建裝置後撤銷 IoT 中樞的裝置存取。 請參閱[停用裝置](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-devguide-identity-registry#disable-devices)。

> [!NOTE] 
> 請注意的重試原則的撤銷存取權的裝置。 例如，具有無限的重試原則的裝置可能會持續嘗試註冊與佈建服務，使用服務資源，而且可能影響效能。

## <a name="blacklist-devices-with-an-individual-enrollment-entry"></a>黑名單裝置，以及個別的註冊項目

個別的註冊項目套用至單一裝置，而且可以使用 X.509 憑證或 SAS 權杖 （在實際或虛擬的 TPM) 作為證明機制。 （使用 SAS 權杖，如其證明機制可以只佈建透過個別註冊的裝置。）要列入封鎖清單具有個別的註冊的裝置，您可以停用或刪除其註冊項目： 

- 若要暫時列入封鎖清單裝置，您可以停用其註冊的項目。 

    1. 登入 Azure 入口網站，然後按一下 **所有資源**從左側功能表。
    2. 按一下您想要列入封鎖清單中的資源清單的從裝置的佈建服務。
    3. 在佈建服務中，按一下 [管理註冊]，然後選取 [個別註冊] 索引標籤。
    4. 按一下您想要列入封鎖清單，以開啟它的裝置的註冊項目。 
    5. 按一下**停用**註冊清單項目底部，然後按一下 **儲存**。  

        ![停用個別註冊入口網站中的項目](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png)
    
- 若要永久列入封鎖清單裝置，您可以刪除其註冊的項目。

    1. 登入 Azure 入口網站，然後按一下 **所有資源**從左側功能表。
    2. 按一下您想要列入封鎖清單中的資源清單的從裝置的佈建服務。
    3. 在佈建服務中，按一下 [管理註冊]，然後選取 [個別註冊] 索引標籤。
    4. 請檢查您想要列入封鎖清單的裝置的註冊項目旁邊的方塊。 
    5. 按一下**刪除**在視窗的頂端，然後按一下 **是**以確認您想要移除註冊。 

        ![刪除個別註冊入口網站中的項目](./media/how-to-revoke-device-access-portal/delete-individual-enrollment.png)
    
    6. 完成此動作之後，您會看到您從個別的註冊項目清單中移除的項目。  

## <a name="blacklist-an-x509-intermediate-or-root-ca-certificate-using-an-enrollment-group"></a>列入封鎖清單 X.509 中繼或根 CA 憑證使用註冊群組

X.509 憑證通常會排列在信任的憑證鏈結中。 如果憑證鏈結中的任何階段遭到盜用，信任會中斷，而且此憑證必須列入黑名單中而若要避免裝置向任何包含該裝置佈建服務所佈建憑證的鏈結中的資料流。 若要了解有關 X.509 憑證和佈建服務的使用方式的詳細資訊，請參閱[X.509 憑證](./concepts-security.md#x509-certificates)。 

註冊群組是共用一般證明機制的 X.509 憑證簽署的相同的中繼或根 CA 的裝置項目。 註冊群組項目設定以中繼或根 CA，以及組態的任何值，例如兩個狀態與 IoT 中樞連線中其憑證, 共用憑證，該裝置之相關聯的 X.509 憑證鏈結。 要列入封鎖清單的憑證，您可以停用或刪除其註冊群組：

- 若要暫時列入封鎖清單的憑證，您可以停用其註冊群組。 

    1. 登入 Azure 入口網站，然後按一下 **所有資源**從左側功能表。
    2. 按一下您想要列入封鎖清單中的資源清單的簽署憑證的佈建服務。
    3. 在佈建服務中，按一下 [管理註冊]，然後選取 [註冊群組] 索引標籤。
    4. 按一下您想要列入封鎖清單，以開啟它的憑證註冊群組。
    5. 按一下**編輯群組**在左上方的註冊群組項目。
    6. 若要停用的註冊項目，請選取**停用**上**啟用輸入**切換，然後按一下 **儲存**。  

        ![停用註冊入口網站中的群組項目](./media/how-to-revoke-device-access-portal/disable-enrollment-group.png)

    
- 若要永久列入封鎖清單的憑證，您可以刪除其註冊群組。

    1. 登入 Azure 入口網站，然後按一下 **所有資源**從左側功能表。
    2. 按一下您想要列入封鎖清單中的資源清單的從裝置的佈建服務。
    3. 在佈建服務中，按一下 [管理註冊]，然後選取 [註冊群組] 索引標籤。
    4. 請檢查您想要列入封鎖清單的憑證的註冊群組旁邊的方塊。 
    5. 按一下**刪除**在視窗的頂端，然後按一下 **是**以確認您想要移除註冊群組。 

        ![刪除註冊入口網站中的群組項目](./media/how-to-revoke-device-access-portal/delete-enrollment-group.png)

    6. 完成此動作之後，您會看到您從註冊群組清單中移除的項目。  

> [!NOTE]
> 如果您要刪除的憑證註冊群組，其憑證鏈結中有該憑證的裝置仍能夠註冊如果根憑證或另一個中繼憑證其憑證在較高的已啟用的註冊群組鏈結。

## <a name="blacklist-specific-devices-in-an-enrollment-group"></a>列入封鎖清單之註冊群組中的特定裝置

實作 X.509 證明機制的裝置會使用來驗證裝置的憑證鏈結和私密金鑰。 當裝置連接，並向裝置佈建服務時，服務會先尋找相符的裝置認證搜尋來判斷是否可以佈建裝置的註冊群組之前個別註冊。 如果服務找到的裝置已停用個別註冊，它會阻止裝置連接，即使有中繼啟用的註冊群組或裝置的憑證鏈結中的根 CA。 若要註冊群組中的個別裝置列入封鎖清單，請遵循下列步驟：

1. 登入 Azure 入口網站，然後在左側功能表中按一下 [所有資源]。
2. 從資源的清單中，按一下包含您想要列入封鎖清單的裝置的註冊群組的佈建服務。
3. 在佈建服務中，按一下 [管理註冊]，然後選取 [個別註冊] 索引標籤。
4. 按一下位於頂端的 [新增] 按鈕。 
5. 選取**X.509**做為安全性機制進行裝置和裝置憑證上傳。 這是安裝在裝置上，用來產生用於驗證的憑證簽署的終端實體憑證。
6. 輸入**IoT 中樞裝置識別碼**裝置。 
7. 若要停用的註冊項目，請選取**停用**上**啟用輸入**切換。 
8. 按一下 [檔案] 。 在成功建立您的註冊，您應該會看到您的裝置會出現在**個別註冊** 索引標籤。 

    ![停用個別註冊入口網站中的項目](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png)




