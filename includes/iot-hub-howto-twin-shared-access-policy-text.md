---
title: 包含檔案
description: 包含檔案
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: a843821a7720ddcb17f8a369d012facd7ba81e35
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/09/2019
ms.locfileid: "68912314"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-twin-getstarted.md files-->

在本文中, 您會建立後端服務, 將所需的屬性新增至裝置對應項, 然後查詢身分識別登錄, 以尋找具有已據以更新之報告屬性的所有裝置。 您的服務需要**服務**連線許可權來修改裝置對應項的所需屬性, 而且需要登錄**讀取**許可權才能查詢身分識別登錄。 沒有預設的共用存取原則只包含這兩個許可權, 因此您必須建立一個。
