---
title: 包含檔案
description: 包含檔案
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/09/2019
ms.service: storage
ms.subservice: blobs
ms.topic: include
ms.reviewer: hux
ms.custom: include file
ms.openlocfilehash: 03674a51566ab89791725d1a51c7af12ed6949e5
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/12/2019
ms.locfileid: "68952911"
---
若要閱讀封存儲存體中的資料，您必須先將 blob 層變更為經常性存取或非經常性存取。 此程式稱為解除凍結, 可能需要數小時才能完成。 我們建議大型 blob 大小以獲得最佳的解除凍結效能。 同時解除凍結數個小型 blob 可能會增加額外時間。 目前有兩個解除凍結優先順序: 高 (預覽) 和標準, 可以透過[設定 Blob 層](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier)或[複製 blob](https://docs.microsoft.com/rest/api/storageservices/copy-blob)作業上的選擇性 [ *x 毫秒-解除凍結-優先順序*] 屬性來設定。

* **標準優先順序**:解除凍結要求會依照收到的連續處理, 最多可能需要15小時。
* **高優先順序 (預覽)** :解除凍結要求會優先于標準要求, 而且可能會在1小時內完成。 高優先順序可能需要超過1小時的時間, 視 blob 大小和目前的需求而定。 高優先順序的要求保證優先于標準優先順序要求。

> [!NOTE]
> [標準優先權] 是 [封存] 的預設解除凍結選項。 高優先順序是較快速的選項, 其成本高於標準的優先順序解除凍結, 而且通常保留供緊急資料還原情況使用。

在解除凍結期間，您可以查看 [封存狀態] blob 屬性，以確認是儲存層否已變更。 視目的地層而定，狀態會顯示為 "rehydrate-pending-to-hot" 或 "rehydrate-pending-to-cool"。 完成時，系統會移除封存狀態屬性，而 [存取層] blob 屬性會反映新的經常性存取或非經常性存取層。
