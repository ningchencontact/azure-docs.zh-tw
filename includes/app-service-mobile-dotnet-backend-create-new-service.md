---
title: 包含檔案
description: 包含檔案
services: app-service\mobile
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 06/20/2019
ms.author: crdun
ms.custom: include file
ms.openlocfilehash: 72a69359d412a7560472fbb73ec525ab5d4a4fce
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/21/2019
ms.locfileid: "67325790"
---
1. 登入 [Azure 入口網站]。

2. 按一下 [建立資源]  。

3. 在 [搜尋] 方塊中，輸入**Web 應用程式**。
    
4. 在 [結果] 清單中，選取**Web 應用程式**從 Marketplace。

5. 選取您**訂用帳戶**並**資源群組**(選取現有的資源群組_或_建立新的 （使用相同的名稱做為您的應用程式）)。

6. 選擇唯一**名稱**web 應用程式。

7. 選擇預設值**發佈**做為選項**程式碼**。

8. 在 **執行階段堆疊**，您必須選取下的一個版本**ASP.NET**或**節點**。 如果您要建置.NET 後端，請選取 ASP.NET 下的版本。 否則如果您的目標節點為基礎的應用程式，選取其中一個版本的節點。

9. 選取右側**作業系統**，Linux 或 Windows。 

10. 選取 **地區**您要部署此應用程式。 

11. 選取適當**App Service 方案**並按下**檢閱並建立**。 

12. 在 [資源群組]  下，選取現有的資源群組或  建立新的資源群組 (使用與應用程式相同的名稱)。

13. 按一下頁面底部的 [新增]  。 等候幾分鐘的時間來成功地部署服務，然後再繼續進行。 監看狀態更新入口網站標頭中的 [通知] \(鈴鐺) 圖示。

14. 完成部署之後，按一下**部署詳細資料**區段，然後按一下 資源類型上**microsoft.web/sites**。 它會將您巡覽至您剛才建立 App Service Web 應用程式。 

15. 按一下**組態**下方的刀鋒視窗**設定**然後在**應用程式設定**，按一下**新增應用程式設定**按鈕。

16. 在 **新增/編輯應用程式設定**頁面上，輸入**名稱**做為**MobileAppsManagement_EXTENSION_VERSION**且值為**最新**和按 確定。

所有會設定您要使用這個新建立的 App Service Web 應用程式，為行動裝置應用程式。

<!-- URLs. -->
[Azure 入口網站]: https://portal.azure.com/