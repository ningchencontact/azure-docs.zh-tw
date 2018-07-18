---
title: 在 Azure 中的 LUIS 應用程式上與其他參與者共同作業 | Microsoft Docs
description: 了解如何在 Language Understanding (LUIS) 應用程式上與其他參與者共同作業。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/05/2018
ms.author: v-geberr
ms.openlocfilehash: c0451f7621a3c18dbf365f3a03934924c030092f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2018
ms.locfileid: "35369926"
---
# <a name="collaborate-with-others-on-language-understanding-luis-apps"></a>在 Language Understanding (LUIS) 應用程式上與其他人共同作業  

您可以在 LUIS 應用程式上與其他人共同作業。 

## <a name="owner-and-collaborators"></a>擁有者和共同作業者
一個應用程式只有單一擁有者，但可以有許多共同作業者。 

## <a name="add-collaborator"></a>新增共同作業者

若要允許共同作業者編輯您的 LUIS 應用程式，請在 LUIS 應用程式的 [設定] 頁面上輸入共同作業者的電子郵件，然後按一下 [新增共同作業者]。

![新增共同作業者](./media/luis-how-to-collaborate/add-collaborator.png)

* 共同作業者可在您使用 LUIS 應用程式的同時登入及編輯該應用程式。 <!--If a collaborator edits the LUIS app, you see a notification at the top of the browser.-->
* 共同作業者無法新增其他共同作業者。

## <a name="set-application-as-public"></a>將應用程式設為公用
如需詳細資訊，請參閱[公用應用程式端點存取](luis-concept-security.md#public-app-endpoint-access)。

### <a name="transfer-of-ownership"></a>擁有權的轉讓
LUIS 目前並不支援擁有權的轉讓，但您可以匯出應用程式，而其他 LUIS 使用者則可以匯入應用程式。 兩個應用程式的 LUIS 分數可能會有些許差異。 
