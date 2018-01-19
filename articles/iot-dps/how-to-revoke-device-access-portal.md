---
title: "如何管理 Azure IoT 中樞裝置佈建服務的裝置存取 | Microsoft Docs"
description: "如何在 Azure 入口網站中撤銷 DPS 服務的裝置存取"
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
ms.openlocfilehash: 6f1dad648b228163219c8f722eed3897f4ba4d22
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2018
---
# <a name="how-to-revoke-device-access-to-your-provisioning-service-in-the-azure-portal"></a>如何在 Azure 入口網站中撤銷佈建服務的裝置存取

對於受到廣泛使用的系統 (例如 IoT 解決方案) 來說，適當地管理裝置認證是非常重要的。 適用於這類系統的最佳作法是清楚地規劃如何撤銷裝置存取，以免裝置的認證 (SAS 權杖或 X.509 憑證) 遭到洩漏。 本文會說明如何在佈建步驟中撤銷裝置存取。

若要了解如何在佈建裝置後撤銷 IoT 中樞的裝置存取， 請參閱[停用裝置](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-identity-registry#disable-devices)。

> [!NOTE] 
> 針對您要撤銷其存取權的裝置，請注意這些裝置的重試原則。 例如，具有無限制重試原則的裝置，可能會持續嘗試對佈建服務進行註冊，如此會耗用服務資源且可能影響效能。

## <a name="blacklist-devices-with-an-individual-enrollment-entry"></a>將具有個人註冊項目的裝置列入黑名單

個別註冊適用於單一裝置，且可使用 X.509 憑證或 SAS 權杖 (適用於實際或虛擬 TPM) 作為證明機制。 (使用 SAS 權杖作為證明機制的裝置只可透過個別註冊進行佈建。)若要將具有個別註冊的裝置列入黑名單，您可以停用或刪除其註冊項目： 

- 若要將裝置暫時列入黑名單，您可以停用其註冊項目。 

    1. 登入 Azure 入口網站，然後在左側功能表中按一下 [所有資源]。
    2. 在資源清單中，按一下要在其中將裝置列入黑名單的佈建服務。
    3. 在佈建服務中，按一下 [管理註冊]，然後選取 [個別註冊] 索引標籤。
    4. 按一下您想要列入黑名單的裝置註冊項目，將其開啟。 
    5. 在註冊清單項目底部按一下 [停用]，然後按一下 [儲存]。  

        ![在入口網站中停用個別註冊](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png)
    
- 若要將裝置永久列入黑名單，您可以刪除其註冊項目。

    1. 登入 Azure 入口網站，然後在左側功能表中按一下 [所有資源]。
    2. 在資源清單中，按一下要在其中將裝置列入黑名單的佈建服務。
    3. 在佈建服務中，按一下 [管理註冊]，然後選取 [個別註冊] 索引標籤。
    4. 針對您想要列入黑名單的裝置註冊項目，核取該項目旁的方塊。 
    5. 在視窗的頂端按一下 [刪除]，然後按一下 [是] 以確認您要移除該註冊。 

        ![在入口網站中刪除個別註冊項目](./media/how-to-revoke-device-access-portal/delete-individual-enrollment.png)
    
    6. 完成此動作之後，您會看到您的項目已從個別註冊清單中移除。  

## <a name="blacklist-an-x509-intermediate-or-root-ca-certificate-using-an-enrollment-group"></a>使用註冊群組將 X.509 中繼或根 CA 憑證列入黑名單

X.509 憑證通常會配置在信任的信任鏈結中。 如果憑證在鏈結中的任何階段上受到損害，信任就會中斷，而且必須將此憑證列入黑名單中，讓裝置佈建服務不會佈建任何該憑證所在鏈結中處於下游的裝置。 若要深入了解 X.509 憑證和其與佈建服務搭配使用的方式，請參閱 [X.509 憑證](./concepts-security.md#x509-certificates)。 

註冊群組是裝置的項目，這些裝置會共用由相同中繼或根 CA 所簽署之 X.509 憑證的通用證明機制。 註冊群組項目的設定中會包含與中繼或根 CA 相關聯的 X.509 憑證，以及任何設定值，例如對應項狀態與 IoT 中樞連線，在其信任鏈結中具有該憑證的裝置可共用這些項目。 若要將憑證列入黑名單，您可以停用或刪除其註冊群組：

- 若要將憑證暫時列入黑名單，您可以停用其註冊群組。 

    1. 登入 Azure 入口網站，然後在左側功能表中按一下 [所有資源]。
    2. 在資源清單中，按一下要在其中將簽署憑證列入黑名單的佈建服務。
    3. 在佈建服務中，按一下 [管理註冊]，然後選取 [註冊群組] 索引標籤。
    4. 按一下您想要列入黑名單的憑證註冊群組，將其開啟。
    5. 在註冊群組項目的左上方，按一下 [編輯群組]。
    6. 若要停用註冊項目，請在 [啟用項目] 的切換開關上選取 [停用]，然後按一下 [儲存]。  

        ![在入口網站中停用註冊群組項目](./media/how-to-revoke-device-access-portal/disable-enrollment-group.png)

    
- 若要將憑證永久列入黑名單，您可以刪除其註冊群組。

    1. 登入 Azure 入口網站，然後在左側功能表中按一下 [所有資源]。
    2. 在資源清單中，按一下要在其中將裝置列入黑名單的佈建服務。
    3. 在佈建服務中，按一下 [管理註冊]，然後選取 [註冊群組] 索引標籤。
    4. 針對您想要列入黑名單的憑證註冊群組，核取該群組旁的方塊。 
    5. 在視窗的頂端按一下 [刪除]，然後按一下 [是] 以確認您要移除該註冊群組。 

        ![在入口網站中刪除註冊群組項目](./media/how-to-revoke-device-access-portal/delete-enrollment-group.png)

    6. 完成此動作之後，您會看到您的項目已從註冊群組清單中移除。  

> [!NOTE]
> 如果您刪除了憑證的註冊群組，其信任鏈結中有該憑證的裝置可能仍然能夠進行註冊，前提是有裝置信任鏈結中較上游的根憑證或另一個中繼憑證的已啟用註冊群組。

## <a name="blacklist-specific-devices-in-an-enrollment-group"></a>將註冊群組中的特定裝置列入黑名單

實作 X.509 證明機制的裝置會使用裝置的信任鏈結和私密金鑰來進行驗證。 當裝置對裝置佈建服務進行連線和驗證時，服務會先尋找符合裝置認證的個人註冊，然後才搜尋註冊群組，以決定是否可佈建該裝置。 如果服務找到裝置的個別註冊已停用，服務會阻止裝置連線，即使裝置的信任鏈結中有中繼或根 CA 的已啟用註冊群組。 若要將註冊群組中的個別裝置列入黑名單，請遵循下列步驟：

1. 登入 Azure 入口網站，然後在左側功能表中按一下 [所有資源]。
2. 從資源清單中，按一下包含要列入黑名單之裝置註冊群組的佈建服務。
3. 在佈建服務中，按一下 [管理註冊]，然後選取 [個別註冊] 索引標籤。
4. 按一下位於頂端的 [新增] 按鈕。 
5. 選取 [X.509] 作為裝置的安全性機制，並上傳裝置憑證。 這是裝置上已安裝的終端實體憑證，用來產生驗證用的憑證。
6. 輸入裝置的 **IoT 中樞裝置識別碼**。 
7. 若要停用註冊項目，請在 [啟用項目] 的切換開關上選取 [停用]。 
8. 按一下 [檔案] 。 在成功建立註冊後，您應該會看到裝置出現在 [個別註冊] 索引標籤底下。 

    ![在入口網站中停用個別註冊項目](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png)




