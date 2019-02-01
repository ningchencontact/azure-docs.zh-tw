---
title: 取得語音裝置 SDK
titleSuffix: Azure Cognitive Services
description: 語音服務可與各種裝置和音訊來源搭配運作。 您現在可以利用相符的硬體和軟體，將語音應用程式提升至下一個層級。 在本文中，您將了解如何取得語音裝置 SDK 的存取權，並開始進行開發。
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 875240b779f05c1b13f71bf2133b9abfaa209239
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2019
ms.locfileid: "55223352"
---
# <a name="get-the-cognitive-services-speech-devices-sdk"></a>取得認知服務語音裝置 SDK

語音裝置 SDK 處於受限預覽狀態，要求您在程式中註冊。 目前，Microsoft 會偏好以大型公司作為存取這項產品的候選者。

## <a name="request-access"></a>要求存取

取得語音裝置 SDK 的存取權：

1. 移至 Microsoft 語音裝置 SDK [註冊表單](https://aka.ms/sdsdk-signup)。
1. 閱讀[授權合約](speech-devices-sdk-license.md)。
1. 如果您同意此授權合約的條款，請選取 [我同意]。
1. 回答表單中的問題。
1. 提交表單。
1. 如果您的電子郵件地址還不是 Azure Active Directory (Azure AD) 的一部分，您會在核准存取時收到類似下面範例的邀請電子郵件。 如果您的電子郵件地址已經在 Azure AD 中，您會在獲得核准存取時收到來自 Microsoft 語音小組的電子郵件訊息，而且可以跳到[下載語音裝置 SDK](#download-the-speech-devices-sdk)。

## <a name="approval-e-mail"></a>核准電子郵件

```
From: Microsoft Speech Team from Microsoft (via Microsoft) <invites@microsoft.com>
Subject: You're invited to the Microsoft organization
```

![電子郵件訊息](media/speech-devices-sdk/get-sdk-1.png)

## <a name="accept-access"></a>接受存取

完成下列步驟後，即可使用您在註冊時提供的電子郵件地址加入 Azure AD。 此流程可供您存取語音裝置 SDK 的[下載網站](https://shares.datatransfer.microsoft.com/)。

1. 在您收到的電子郵件訊息中，選取 [開始使用]。 如果您的組織已經是 Office 365 客戶，系統會提示您登入，而您可以跳到步驟 8。

2. 在開啟的瀏覽器視窗中，選取 [下一步]。

    ![驗證視窗](media/speech-devices-sdk/get-sdk-2.png)

3. 建立 Microsoft 帳戶 (如果您還沒有帳戶的話)。 輸入您用來接收邀請電子郵件的同一個電子郵件地址。

    ![建立 Microsoft 帳戶](media/speech-devices-sdk/get-sdk-3.png)

4. 選取 [下一步] 建立密碼。

5. 當系統提示驗證您的電子郵件時，請使用您邀請電子郵件上所提供的驗證碼。

7. 在對話方塊中貼上或輸入電子郵件訊息中的安全碼。 在此範例中，安全碼為 [8406]。 選取 [下一步] 。

    ![驗證電子郵件](media/speech-devices-sdk/get-sdk-6.png)

8. 當您在瀏覽器中看到 [存取面板應用程式] 時，您已確認您的電子郵件地址是 Azure AD 的一部分。 您現在可以存取語音裝置 SDK 下載網站。

## <a name="download-the-speech-devices-sdk"></a>下載語音裝置 SDK

前往[語音裝置 SDK 下載網站](https://shares.datatransfer.microsoft.com/)。 使用您稍早建立的 Microsoft 帳戶登入。

![SDK 下載網站](media/speech-devices-sdk/get-sdk-7.png)

下載語音裝置 SDK、相關聯的範例程式碼及參考資料：

1. 當瀏覽器出現提示時，下載並安裝 Aspera Connect 工具。

    ![下載 Aspera Connect](media/speech-devices-sdk/get-sdk-8.png)

1. 選取 [是]，將應用程式切換為 Aspera Connect。

    ![切換為 Aspera Connect](media/speech-devices-sdk/get-sdk-9.png)

1. 選取 [允許]，確認使用 Aspera Connect 下載檔案。

    ![使用 Aspera Connect 下載](media/speech-devices-sdk/get-sdk-10.png)

1. 下載檔案後，請關閉 Aspera Connect 的 [傳輸] 視窗。

    ![Aspera Connect 的 [傳輸] 視窗](media/speech-devices-sdk/get-sdk-11.png)

根據預設，檔案會下載到您的 [下載] 資料夾。 您現在可以登出此網站。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [開始使用語音裝置 SDK](speech-devices-sdk-qsg.md)
