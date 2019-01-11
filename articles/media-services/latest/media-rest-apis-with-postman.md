---
title: 設定 Postman 以進行 Azure 媒體服務 REST API 呼叫
description: 了解如何設定 Postman 以進行媒體服務 REST API 呼叫。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2018
ms.author: juliako
ms.openlocfilehash: 06ecdf31c2627eda5bf651667703ddcf39c9d307
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/19/2018
ms.locfileid: "53604094"
---
# <a name="configure-postman-for-media-services-rest-api-calls"></a>設定 Postman 以進行媒體服務 REST API 呼叫

本文說明如何設定 **Postman**，以便用來呼叫 Azure 媒體服務 (AMS) REST API。 本文說明如何將環境和集合檔案匯入 **Postman**。 集合包含呼叫 Azure 媒體服務 (AMS) REST API 之 HTTP 要求的分組定義。 環境檔案包含集合所使用的變數。

## <a name="prerequisites"></a>必要條件

- [建立媒體服務帳戶](create-account-cli-how-to.md)。 請務必記住資源群組名稱和「媒體服務」帳戶名稱。 
- 取得所需資訊來[存取 API](access-api-cli-how-to.md)
- 安裝 [Postman](https://www.getpostman.com/) \(英文\) REST 用戶端，來執行在某些 AMS REST 教學課程中所示範的 REST API。 

    我們使用的是 **Postman**，但任何 REST 工具都適用。 其他替代方式為：搭配 REST 外掛程式的 **Visual Studio Code**，或 **Telerik Fiddler**。 

## <a name="download-postman-files"></a>下載 Postman 檔案

複製包含 Postman 集合和環境檔案的 GitHub 存放庫。

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-rest-postman.git
 ```

## <a name="configure-postman"></a>設定 Postman

此區段會設定 Postman。

### <a name="configure-the-environment"></a>建立環境 

1. 開啟 **Postman**。
2. 選取畫面右側的 [管理環境] 選項。

    ![管理 env](./media/develop-with-postman/postman-import-env.png)
4. 按一下 [管理環境] 對話方塊中的 [匯入]。
2. 瀏覽至您在複製 `https://github.com/Azure-Samples/media-services-v3-rest-postman.git` 時下載的 `Azure Media Service v3 Environment.postman_environment.json` 檔案。
6. 新增 **Azure Media Service v3 Environment** 環境。

    > [!Note]
    > 使用您從前述的**存取媒體服務 API** 一節中取得的值來更新存取變數。

7. 按兩下選取的檔案，並輸入您依照[存取 API](#access-the-media-services-api) 步驟所取得的值。
8. 關閉對話方塊。
9. 從下拉式清單中選取 **Azure Media Service v3 Environment** 環境。

    ![選擇 env](./media/develop-with-postman/choose-env.png)
   
### <a name="configure-the-collection"></a>設定集合

1. 按一下 [匯入] 來匯入集合檔案。
1. 瀏覽至您在複製 `https://github.com/Azure-Samples/media-services-v3-rest-postman.git` 時下載的 `Media Services v3.postman_collection.json` 檔案
3. 選擇 **Media Services v3.postman_collection.json** 檔案。

    ![匯入檔案](./media/develop-with-postman/postman-import-collection.png)

## <a name="get-azure-ad-token"></a>取得 Azure AD 權杖 

在開始操作 AMS v3 資源之前，您需要先取得和設定服務主體驗證的 Azure AD 權杖。

1. 在 Postman 的左側視窗中，選取 [步驟 1:取得 AAD 驗證權杖]。
2. 然後，選取 [取得服務主體驗證的 Azure AD 權杖]。
3. 按 [傳送]。

    此時會傳送下列 **POST** 作業。

    ```
    https://login.microsoftonline.com/:tenantId/oauth2/token
    ```

4. 傳回的回應會包含權杖，並將 "AccessToken" 環境變數設定為權杖值。  

    ![取得 AAD 權杖](./media/develop-with-postman/postman-get-aad-auth-token.png)

## <a name="next-steps"></a>後續步驟

[使用 REST 串流檔案](stream-files-tutorial-with-rest.md)。  
