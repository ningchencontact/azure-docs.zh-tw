---
title: 通知中樞的安全性
description: 本主題說明 Azure 通知中樞的安全性。
services: notification-hubs
documentationcenter: .net
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 6506177c-e25c-4af7-8508-a3ddca9dc07c
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 05/31/2019
ms.author: jowargo
ms.openlocfilehash: 73a6d0eaab286dec9d02bb55eb75f0781bcffcc4
ms.sourcegitcommit: a3a40ad60b8ecd8dbaf7f756091a419b1fe3208e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2019
ms.locfileid: "69891578"
---
# <a name="notification-hubs-security"></a>通知中樞的安全性

## <a name="overview"></a>總覽

本主題說明 Azure 通知中樞的安全性模型。

## <a name="shared-access-signature-security-sas"></a>共用存取簽章的安全性 (SAS)

通知中樞會實作實體層級的安全性配置，稱為 SAS (共用存取簽章)。 每個規則都包含名稱、金鑰值 (共用密碼) 及一組許可權, 如[安全性宣告](#security-claims)中所述。 建立通知中樞時, 會自動建立兩個規則: 一個具有**接聽**許可權 (用戶端應用程式使用), 另一個具有**所有**許可權 (應用程式後端使用)。

從用戶端應用程式執行註冊管理時，若透過通知傳送的資訊不是敏感性資訊 (例如氣象更新)，常見存取通知中樞的方法是將只具接聽存取權限之規則的索引鍵值，授與用戶端應用程式，並將該規則之完整存取權限的索引鍵值，授與應用程式後端。

應用程式不應該在 Windows Store 用戶端應用程式中內嵌金鑰值, 而是在啟動時, 讓用戶端應用程式從應用程式後端取出它。

具有**接聽**存取權的金鑰可讓用戶端應用程式註冊任何標記。 如果您的應用程式必須將特定標籤的註冊限制為特定用戶端 (例如, 當標記代表使用者識別碼時), 您的應用程式後端必須執行註冊。 如需詳細資訊, 請參閱[註冊管理](notification-hubs-push-notification-registration-management.md)。 請注意，如此一來，用戶端應用程式將無法直接存取通知中樞。

## <a name="security-claims"></a>安全性宣告

與其他實體類似，通知中樞作業也可有三種安全性宣告：**接聽**、**傳送**及**管理**。

| 宣告   | 描述                                          | 允許的作業 |
| ------- | ---------------------------------------------------- | ------------------ |
| 待命  | 建立/更新、讀取及刪除單一註冊 | 建立/更新註冊<br><br>讀取註冊<br><br>讀取控制代碼的所有註冊<br><br>刪除註冊 |
| 傳送    | 將訊息傳送到通知中樞                | 傳送訊息 |
| 管理  | 對通知中樞執行 CRUD (包含更新 PNS 認證及安全性金鑰) 並依標記讀取的註冊 |建立/更新/讀取/刪除通知中樞<br><br>依標記讀取註冊 |

通知中樞接受在通知中樞上直接設定的共用金鑰所產生的簽章權杖。

您無法將通知傳送至多個命名空間。 命名空間是通知中樞的邏輯容器, 不涉及傳送通知。
命名空間層級的存取原則 (認證) 可用於命名空間層級的作業, 例如: 列出通知中樞、建立或刪除通知中樞等等。只有中樞層級的存取原則可讓您傳送通知。
