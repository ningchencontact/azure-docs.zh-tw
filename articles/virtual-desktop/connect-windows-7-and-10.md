---
title: 從 Windows 10 或 Windows 7-Azure 連線到 Windows 虛擬桌面的預覽
description: 如何從 Windows 10 或 Windows 7 連線至 Windows 虛擬桌面預覽。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: helohr
ms.openlocfilehash: 66b423ea27cc0f9112349964fcfd0d75f08c12bd
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620109"
---
# <a name="connect-from-windows-10-or-windows-7"></a>從 Windows 10 或 Windows 7 連線

> 適用於：Windows 7 和 Windows 10。

可下載的用戶端可存取 Windows 虛擬桌面預覽資源從執行 Windows 7 和 Windows 10 的裝置。

> [!IMPORTANT]
> 不用**RemoteApp 和桌面連線 (RADC)** 或是**遠端桌面連線 (MSTSC)** 存取 Windows 的虛擬桌面的資源，因為 Windows 虛擬桌面不支援任一用戶端。

## <a name="install-the-client"></a>安裝用戶端

[下載](https://go.microsoft.com/fwlink/?linkid=2068602) 並安裝到本機電腦的用戶端。 安裝需要系統管理員權限。

## <a name="subscribe-to-a-feed"></a>訂閱摘要

要取得可用的受管理資源的清單，藉由訂閱摘要提供您的管理員。訂閱提供的資源在本機電腦上。

若要訂閱摘要：

1. 啟動用戶端從所有的應用程式清單中，尋找**遠端桌面**。
1. 選取  **Subscribe**連接至服務並擷取您的資源的主頁面上。
1. **登入**與您的使用者帳戶出現提示時。

成功驗證之後，您現在應該會看到一份可供您的資源。

您可以透過兩種方法之一啟動資源。

- 從用戶端的主要頁面上，按兩下 要啟動它的資源。
- 像平常一樣從 [開始] 功能表中的其他應用程式，請啟動資源。
  - 您也可以搜尋的搜尋列中的應用程式。

一旦訂閱摘要，摘要內容會定期自動更新。 資源可能會新增、 變更或移除 根據您的系統管理員所做的變更。

## <a name="view-the-details-of-a-feed"></a>檢視摘要的詳細資料

訂閱後您可以藉由存取詳細資料窗格來檢視摘要的其他資訊。

1. 從用戶端的主要頁面上，選取省略符號 ( **...** ) 右邊的 摘要的名稱。
1. 從下拉式功能表中，選取**詳細資料**。
1. [詳細資料] 面板會顯示在右側的用戶端。

詳細資料面板包含摘要的有用資訊：

- 用來訂閱的使用者名稱與 URL
- 應用程式和桌上型電腦的數目
- 上次更新日期/時間
- 上次更新的狀態

如有需要您可以選取上啟動手動更新**立即更新**。

## <a name="unsubscribe-from-a-feed"></a>取消訂閱摘要

本節將教導您如何取消訂閱摘要。 您可以取消訂閱來訂閱一次使用不同的帳戶，或從系統移除您的資源。

1. 從用戶端的主要頁面上，選取省略符號 ( **...** ) 右邊的 摘要的名稱。
1. 從下拉式功能表中，選取**Unsubscribe**。
1. 檢閱並選取**繼續**從對話方塊。

## <a name="update-the-client"></a>更新用戶端

當用戶端新版本可用時，您會收到通知用戶端和 Windows 行動作業中心 」 中。 選取通知以開始更新程序。
