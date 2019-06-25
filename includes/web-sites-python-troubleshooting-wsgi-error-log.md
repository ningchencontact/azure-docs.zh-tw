---
title: 包含檔案
description: 包含檔案
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/11/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 020e59f029b09f3c7656f67039731e4141e68d31
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67174583"
---
會傳回 （例如，Python 會發生錯誤，在啟動您的應用程式時，如果只是一個簡單的錯誤頁面「 網頁無法顯示因為發生內部伺服器錯誤。"）。

若要擷取 Python 應用程式錯誤：

1. 在 Azure 入口網站的 Web 應用程式中，選取 [設定]  。
2. 在 [設定]  索引標籤中，選取 [應用程式設定]  。
3. 在 [應用程式設定]  下，輸入下列索引鍵/值組：
    * 索引鍵：WSGI_LOG
    * 值：D:\home\site\wwwroot\logs.txt （輸入您選擇的檔案名稱）

您現在應該會看到 wwwroot 資料夾的 logs.txt 檔案中出現錯誤。
