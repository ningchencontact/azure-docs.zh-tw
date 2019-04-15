---
title: 疑難排解適用於 IE 的 Azure 存取面板延伸模組 |Microsoft Docs
description: 如何使用群組原則針對我的 app 入口網站部署 Internet Explorer 附加元件。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: celested
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 63dfece713657df4450f18b8a7ce212ce2c41687
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2019
ms.locfileid: "59565022"
---
# <a name="troubleshoot-the-access-panel-extension-for-internet-explorer"></a>疑難排解 Internet explorer 的存取面板延伸模組

這篇文章可協助您疑難排解下列問題：

* 使用 Internet Explorer 時無法透過「我的 app」入口網站存取您的 app。
* 即使您已經安裝軟體，還是看到「安裝軟體」訊息。

如果您是系統管理員，請參閱[如何部署 Internet explorer 使用群組原則的存取面板延伸模組](deploy-access-panel-browser-extension.md)。

## <a name="run-the-diagnostic-tool"></a>執行診斷工具

您可以下載並執行 「 存取面板 」 診斷工具來診斷存取面板延伸模組的安裝問題。 

若要下載並安裝診斷工具：

1. [選取此連結來下載診斷工具。](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)

2. 開啟檔案，並將內容解壓縮至您的電腦。
   
3. 若要執行此工具，以滑鼠右鍵按一下名為的檔案*AccessPanelExtensionDiagnosticTool.js* ，然後選取**以開啟** > **Microsoft Windows Based Script Host**.
   
    ![[開啟檔案] > [Microsoft Windows Based Script Host]](./media/manage-access-panel-browser-extension/open-access-panel-extension-diagnostic-tool.png)

4. 檢閱診斷結果會出現，並選取**是**以修正問題。 **檢查結果** 對話方塊隨即出現該怎麼辦的擴充功能無法運作的相關資訊。  

5. 讀取訊息，然後選取**確定**。

## <a name="check-that-the-access-panel-extension-is-enabled"></a>檢查存取面板延伸模組是否已啟用

若要確認您已啟用存取面板延伸模組，在 Internet Explorer:

1. 在 Internet Explorer 中，選取**齒輪圖示**右上角的視窗，然後選取**網際網路選項**。
   
2. 移至**程式**索引標籤，然後選取**管理附加元件**。
   
3. 選取 **存取面板延伸模組**中**Microsoft Corporation**區段，然後選取**啟用**。
   
4. 若要儲存變更，關閉所有 Internet Explorer 瀏覽器視窗中，您必須開啟。 變更在的下次開啟 Internet Explorer 會生效。

## <a name="enable-extensions-for-inprivate-browsing"></a>啟用 InPrivate 瀏覽的延伸模組

若要啟用延伸模組的 InPrivate 瀏覽：

1. 在 Internet Explorer 中，選取**齒輪圖示**右上角的視窗，然後選取**網際網路選項**。
   
2. 移至**隱私權**索引標籤，然後確認**InPrivate 瀏覽啟動時停用工具列和延伸模組**已清除核取方塊。
   
3.  若要儲存變更，關閉所有 Internet Explorer 瀏覽器視窗中，您必須開啟。 變更在的下次開啟 Internet Explorer 會生效。

## <a name="uninstall-the-access-panel-extension"></a>解除安裝存取面板延伸模組

若要解除安裝存取面板延伸模組，從您的電腦：

1. 在 [控制台] 中搜尋*解除安裝*。 

2. 在搜尋結果中，選取**解除安裝程式**。
   
    ![解除安裝程式的搜尋。](./media/manage-access-panel-browser-extension/uninstall-program-control-panel.png)

3. 從清單中，選取**存取面板延伸模組**，然後選取**解除安裝**。

    ![解除安裝存取面板延伸模組。](./media/manage-access-panel-browser-extension/uninstall-access-panel-extension.png)
   
4. 您可以嘗試再安裝一次延伸模組，看看是否已經解決問題。

如果您遇到問題，解除安裝擴充功能時，您也可以移除它使用[Microsoft Fix It](https://go.microsoft.com/?linkid=9779673)工具。

## <a name="related-articles"></a>相關文章
* [搭配 Azure Active Directory 的應用程式存取和單一登入](what-is-single-sign-on.md)
* [如何部署 Internet explorer 使用群組原則的存取面板延伸模組](deploy-access-panel-browser-extension.md)

