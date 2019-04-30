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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61483587"
---
在将 RBAC 角色分配到某个安全主体之前，请确定该安全主体应该获取的访问范围。 最佳做法指出，最好是授予尽可能小的范围。

以下列表描述了可将 Azure Blob 和队列资源访问权限限定到哪些级别，从最小的范围开始：

- **個別的容器。** 在這個範圍中，角色指派會套用至所有的容器，以及容器屬性和中繼資料中的 blob。
- **個別的佇列。** 在這個範圍中，角色指派適用於訊息佇列，以及佇列屬性和中繼資料。
- **存储帐户。** 在這個範圍中，角色指派適用於所有的容器和其 blob，或所有佇列和其訊息。
- **资源组。** 在這個範圍中，角色指派會套用至所有的容器或佇列中所有的資源群組中的儲存體帳戶。
- **订阅。** 在這個範圍中，角色指派會套用至所有的容器或所有儲存體帳戶中的所有訂用帳戶中的資源群組中的佇列。

> [!IMPORTANT]
> 如果您的訂用帳戶包含 Azure DataBricks 命名空間，在訂用帳戶範圍指派的角色將會封鎖從 blob 和佇列資料的存取權授與。
