---
title: 包含檔案
description: 包含檔案
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/21/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ad4b244b58d741ad45463297df5bd358f3ae9918
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2019
ms.locfileid: "58449925"
---
您將 RBAC 角色指派給安全性主體之前，決定安全性主體應該有的存取的範圍。 最佳做法應規定，最好是一律將只有最少的可能範圍授與。

下列清單描述的您可以將存取範圍對 Azure 的 blob 和佇列資源，以最窄的範圍開始的層級：

- **個別的容器。** 在這個範圍內，安全性主體會有所有的 blob 容器，以及容器屬性和中繼資料的存取權。
- **個別的佇列。** 在這個範圍內，安全性主體可以存取佇列，以及佇列屬性和中繼資料中的訊息。
- **儲存體帳戶中。** 在這個範圍內，安全性主體擁有存取所有的容器和其 blob，或所有佇列和其訊息。
- **資源群組中。** 在這個範圍內，安全性主體會有所有的容器或佇列中所有的資源群組中的儲存體帳戶的存取權。
- **訂用帳戶。** 在這個範圍內，安全性主體會有存取權的所有容器或所有儲存體帳戶中的所有訂用帳戶中的資源群組中的佇列。
