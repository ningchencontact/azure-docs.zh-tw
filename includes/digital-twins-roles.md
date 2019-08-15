---
title: 包含檔案
description: 包含檔案
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 08/12/2019
ms.author: v-adgera
ms.custom: include file
ms.openlocfilehash: 10161d6b1d944857aa45d5152c77e107a208d98d
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2019
ms.locfileid: "69012065"
---
下表描述 Azure Digital Twins 中可用的角色：

| **角色** | **描述** | **識別碼** |
| --- | --- | --- |
| 空間管理員 | 適用於指定空間及其下所有節點的建立、讀取、更新和刪除權限。 全域權限。 | 98e44ad7-28d4-4007-853b-b9968ad132d1 |
| 使用者管理員| 適用於使用者和使用者相關物件的建立、讀取、更新和刪除權限。 空間的讀取權限。 | dfaac54c-f583-4dd2-b45d-8d4bbc0aa1ac |
| 裝置管理員 | 適用於裝置和裝置相關物件的建立、讀取、更新和刪除權限。 空間的讀取權限。 | 3cdfde07-bc16-40d9-bed3-66d49a8f52ae |
| 金鑰管理員 | 適用於存取金鑰的建立、讀取、更新和刪除權限。 空間的讀取權限。 | 5a0b1afc-e118-4068-969f-b50efb8e5da6 |
| 權杖管理員 |  適用於存取金鑰的讀取和更新權限。 空間的讀取權限。 | 38a3bb21-5424-43b4-b0bf-78ee228840c3 |
| 使用者 |  適用於空間、感應器和使用者 (包括其對應的相關物件) 的讀取權限。 | b1ffdb77-c635-4e7e-ad25-948237d85b30 |
| 支援專家 |  存取金鑰以外各個項目的讀取權限。 | 6e46958b-dc62-4e7c-990c-c3da2e030969 |
| 裝置安裝人員 | 適用於裝置和感應器 (包括其對應的相關物件) 的讀取和更新權限。 空間的讀取權限。 | b16dd9fe-4efe-467b-8c8c-720e2ff8817c |
| 閘道裝置 | 感應器的建立權限。 適用於裝置和感應器 (包括其對應的相關物件) 的讀取權限。 | d4c69766-e9bd-4e61-bfc1-d8b6e686c7a8 |