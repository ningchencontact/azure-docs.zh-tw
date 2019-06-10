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
ms.openlocfilehash: 3f5b23028094b545262e9c01640890f2c0b989ca
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/31/2019
ms.locfileid: "66431247"
---
# <a name="notification-hubs-security"></a>通知中樞的安全性

## <a name="overview"></a>概觀

本主題說明 Azure 通知中樞的安全性模型。

## <a name="shared-access-signature-security-sas"></a>共用存取簽章的安全性 (SAS)

通知中樞會實作實體層級的安全性配置，稱為 SAS (共用存取簽章)。 此配置讓傳訊實體最多可以在其授與該實體權限的描述中宣告 12 項規則。

每個規則都包含名稱、 索引鍵值 （共用密碼） 和一組權限，如所述[安全性宣告](#security-claims)。 建立通知中樞時，會自動建立兩個規則： 一個**接聽**權限 （由用戶端應用程式使用），一個用於**所有**權限 （由應用程式後端使用）。

從用戶端應用程式執行註冊管理時，若透過通知傳送的資訊不是敏感性資訊 (例如氣象更新)，常見存取通知中樞的方法是將只具接聽存取權限之規則的索引鍵值，授與用戶端應用程式，並將該規則之完整存取權限的索引鍵值，授與應用程式後端。

應用程式應該不在 Windows 市集用戶端應用程式中內嵌的金鑰值，改為擷取在啟動應用程式後端用戶端應用程式。

使用索引鍵**接聽**存取可讓用戶端應用程式註冊任何標記。 如果您的應用程式必須限制為特定的用戶端 （例如，當標記代表使用者識別碼） 的特定標記，您的應用程式後端必須執行該註冊。 如需詳細資訊，請參閱 <<c0> [ 註冊管理](notification-hubs-push-notification-registration-management.md)。 請注意，如此一來，用戶端應用程式將無法直接存取通知中樞。

## <a name="security-claims"></a>安全性宣告

與其他實體類似，通知中樞作業也可有三種安全性宣告：**接聽**，**傳送**，以及**管理**。

| 宣告   | 描述                                          | 允許的作業 |
| ------- | ---------------------------------------------------- | ------------------ |
| 接聽  | 建立/更新、讀取及刪除單一註冊 | 建立/更新註冊<br><br>讀取註冊<br><br>讀取控制代碼的所有註冊<br><br>刪除註冊 |
| 傳送    | 將訊息傳送到通知中樞                | 傳送訊息 |
| 管理  | 對通知中樞執行 CRUD (包含更新 PNS 認證及安全性金鑰) 並依標記讀取的註冊 |建立/更新/讀取/刪除通知中樞<br><br>依標記讀取註冊 |

通知中樞接受權杖產生的簽章的共用直接在通知中樞上設定的金鑰。

您不可能將通知傳送至一個以上的命名空間。 命名空間是通知中樞的邏輯容器，並不涉及傳送通知。
命名空間層級存取原則 （認證） 可用於命名空間層級的作業，例如： 列出通知中樞、 建立或刪除通知中樞，等等。只有中樞層級存取原則會讓您傳送通知。
