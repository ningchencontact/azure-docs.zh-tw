---
title: 包含檔案
description: 包含檔案
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/28/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 518c57bc3327511b70deef143826f2a1b9df8639
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66115577"
---
您將 RBAC 角色指派給安全性主體之前，決定安全性主體應該有的存取的範圍。 最佳做法應規定，最好是一律將只有最少的可能範圍授與。

下列清單描述的您可以將存取範圍對 Azure 的 blob 和佇列資源，以最窄的範圍開始的層級：

- **個別的容器。** 在這個範圍中，角色指派會套用至所有的容器，以及容器屬性和中繼資料中的 blob。
- **個別的佇列。** 在這個範圍中，角色指派適用於訊息佇列，以及佇列屬性和中繼資料。
- **儲存體帳戶中。** 在這個範圍中，角色指派適用於所有的容器和其 blob，或所有佇列和其訊息。
- **資源群組中。** 在這個範圍中，角色指派會套用至所有的容器或佇列中所有的資源群組中的儲存體帳戶。
- **訂用帳戶。** 在這個範圍中，角色指派會套用至所有的容器或所有儲存體帳戶中的所有訂用帳戶中的資源群組中的佇列。

> [!IMPORTANT]
> 如果您的訂用帳戶包含 Azure DataBricks 命名空間，在訂用帳戶範圍指派的角色將會封鎖從 blob 和佇列資料的存取權授與。
