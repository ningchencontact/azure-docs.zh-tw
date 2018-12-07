---
title: 在 Azure 入口網站中建立影片索引器帳戶
titlesuffix: Azure Media Services
description: 本文將說明如何在 Azure 入口網站中建立影片索引器帳戶。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 11/19/2018
ms.author: juliako
ms.openlocfilehash: 49e05047d58cc5b6bb5e0099c24a131a26dc8af1
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2018
ms.locfileid: "52291813"
---
# <a name="create-a-video-indexer-account-connected-to-azure"></a>建立連線到 Azure 的影片索引器帳戶

建立影片索引器帳戶時，您可以選擇免費試用帳戶 (您可取得特定的免費編製索引分鐘數) 或付費選項 (您不會受限於配額)。 使用免費試用時，影片索引器最多可為網站使用者提供 600 分鐘的免費編製索引，以及為 API 使用者提供 2400 分鐘的免費索引編製。 使用付費選項時，您建立的影片索引器帳戶會連線到您的 Azure 訂用帳戶和 Azure 媒體服務帳戶。 您需支付已編製索引的分鐘數，以及媒體帳戶相關費用。 

本文將說明如何建立連結到您 Azure 訂用帳戶和 Azure 媒體服務帳戶的影片索引器帳戶。 

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 

    如果您還沒有 Azure 訂用帳戶，請先註冊 [Azure 免費試用版](https://azure.microsoft.com/free/)。

* Azure Active Directory (AD) 網域。 

    如果您沒有 Azure AD 網域，請使用您的 Azure 訂用帳戶建立此網域。 如需詳細資訊，請參閱[管理 Azure Active Directory 中的自訂網域名稱](../../active-directory/users-groups-roles/domains-manage.md)

* Azure AD 網域的使用者和成員。 當您將影片索引器帳戶連線到 Azure 時，會用到此成員。

    此使用者應符合下列準則：

    * 具有公司或學校帳戶 (非個人帳戶) 的 Azure AD 使用者，個人帳戶是指 outlook.com、live.com 或 hotmail.com 等。
        
        ![所有 AAD 使用者](./media/create-account/all-aad-users.png)

    *  Azure 訂用帳戶中具有「擁有者」角色或同時有「參與者」和「使用者存取系統管理員」角色的成員。 使用者能夠以 2 個角色新增 2 次。 一次使用「參與者」角色，另一次使用「使用者存取系統管理員」角色。

        ![存取控制](./media/create-account/access-control-iam.png)

* 使用 Azure 入口網站註冊 EventGrid 資源提供者。

    在 [Azure 入口網站](https://portal.azure.com/) 中，前往 [訂用帳戶] > [訂用帳戶] > [資源提供者] > [Microsoft.EventGrid]。 如果不是處於「已註冊」狀態，請按一下 [註冊]。 這需要幾分鐘來完成註冊。 

    ![EventGrid](./media/create-account/event-grid.png)

## <a name="connect-to-azure"></a>連接到 Azure

1. 瀏覽至[影片索引器](https://www.videoindexer.ai/)網站並登入。

2. 按一下 [連線到 Azure] 按鈕：

    ![連線到 Azure](./media/create-account/connect-to-azure.png)

3. 當訂用帳戶清單出現時，選取您想要使用的訂用帳戶。 

    ![將影片索引器連線到 Azure](./media/create-account/connect-vi-to-azure-subscription.png)

4. 從支援的位置選取 Azure 區域：美國西部 2、北歐或東亞。
5. 在 [Azure 媒體服務帳戶] 下，選擇其中一個選項：

    * 若要建立新的媒體服務帳戶，請選取 [建立新的資源群組]。 提供資源群組的名稱。

        Azure 會在您的訂用帳戶中建立新帳戶，包括新的 Azure 儲存體帳戶。 新媒體服務帳戶的預設初始設定會有一個串流端點和 10 個 S3 保留單元。
    * 若要使用現有的媒體服務帳戶，請選取 [使用現有資源]。 從帳戶清單中，選取您的帳戶。

        媒體服務帳戶具有的區域必須與您的影片索引器帳戶相同。 若要達到最少的索引編製持續時間及低輸送量，請將媒體服務帳戶中的保留單元類型與數目設為 **10 個 S3 保留單元**。
    * 若要手動設定您的連線，請按一下 [切換為手動設定]。 
    
        如果基於某些原因而無法完成自動選項，或如果您的安裝和設定不同於一般情況，或您想要能完整檢視及控制設定，則您可以手動設定連線。 
        
        在 [將影片索引器連線到 Azure 訂用帳戶] 中，提供下列資訊。

        |設定|說明|
        |---|---|
        |影片索引器帳戶區域|影片索引器帳戶區域的名稱。 為了達到較佳的效能和較低的成本，強烈建議您指定 Azure 媒體服務資源和 Azure 儲存體帳戶所在區域的名稱。 |
        |Azure Active Directory (AAD) 租用戶|Azure AD 租用戶的名稱，例如 "contoso.onmicrosoft.com"。 租用戶資訊可從 Azure 入口網站擷取。 將游標放在右上角登入的使用者名稱上方。|
        |訂用帳戶識別碼|用來建立此連線的 Azure 訂用帳戶。 訂用帳戶識別碼可從 Azure 入口網站擷取。 按一下左側面板中的 [所有服務]，然後搜尋「訂用帳戶」。 選取 [訂用帳戶]，並從訂用帳戶清單中選擇需要的識別碼。|
        |Azure 媒體服務資源群組名稱|媒體服務帳戶所在的資源群組名稱。|
        |媒體服務資源名稱|Azure 媒體服務資源的名稱。|
        |應用程式識別碼|具有指定媒體服務帳戶權限的 Azure AD 應用程式識別碼。 如需詳細資訊，請參閱[使用服務主體驗證](../../media-services/previous/media-services-portal-get-started-with-aad.md#service-principal-authentication)。|
        |應用程式金鑰|如需詳細資訊，請參閱[使用服務主體驗證](../../media-services/previous/media-services-portal-get-started-with-aad.md#service-principal-authentication)。|

6. 完成之後，請選擇 [連線]。 完成此作業可能需要幾分鐘的時間。 

    連線至 Azure 之後，新的影片索引器帳戶會出現在帳戶清單中：

    ![新帳戶](./media/create-account/new-account.png)

7. 瀏覽至您的新帳戶： 

    ![影片索引器帳戶](./media/create-account/vi-account.png)

## <a name="considerations"></a>考量

以下是 Azure 媒體服務的相關考量：

* 如果您連線到新的媒體服務帳戶，您會在 Azure 訂用帳戶中看到新的資源群組、媒體服務帳戶和儲存體帳戶。
* 如果您連線到新的媒體服務帳戶，影片索引器會將媒體**保留單元**設為 10 個 S3 單元：

    ![媒體服務保留單元](./media/create-account/ams-reserved-units.png)

* 如果您連線到現有的媒體服務帳戶，影片索引器則不會變更現有的媒體**保留單元**設定。

    您可能需要根據所規劃的負載，調整媒體**保留單元**的類型和數量。 請記住，如果您的負載很高，卻沒有足夠的單元或速度，則影片處理會導致逾時錯誤。

* 如果您連線到新的媒體服務帳戶，影片索引器會自動啟動其中的預設**串流端點**：

    ![媒體服務串流端點](./media/create-account/ams-streaming-endpoint.png)

* 如果您連線到現有的媒體服務帳戶，則影片索引器不會變更預設的串流端點設定。 如有沒有正在執行的**串流端點**，您將無法從此媒體服務帳戶或影片索引器中觀看影片。

## <a name="next-steps"></a>後續步驟

若要以程式設計方式與連線到 Azure 的試用帳戶和/或影片索引器帳戶互動，請遵循[使用 API](video-indexer-use-apis.md) 中的指示。

您應使用連線至 Azure 時使用的相同 Azure AD 使用者。


