---
title: 變更叢集組態存取-Azure HDInsight
description: 深入了解存取機密的叢集設定欄位的變更。
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/12/2019
ms.openlocfilehash: 85a6737ee111975d4c7ecf078673280127bfd0fd
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2019
ms.locfileid: "59610196"
---
# <a name="changes-to-cluster-configuration-access"></a>變更叢集組態的存取權

最新版的 Azure HDInsight SDK 會帶來一些重要的變更，以支援更多更細緻以角色為基礎的存取以取得敏感資訊。 當這些組件變更時，有些**可能需要動作**如果您使用其中一個受影響的方法。

## <a name="sdk-for-net-500"></a>SDK for.NET 5.0.0

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet) 將會**不會再傳回敏感性參數**，例如儲存體金鑰 （核心站台） 或 HTTP 認證 （閘道）。
    - 若要擷取所有的設定，包括敏感性參數，使用`ConfigurationOperationsExtensions.List`從現在開始。  請注意，'讀者 」 角色的使用者將無法使用這個方法。 這可讓您更精確地控制哪些使用者可以存取叢集的機密資訊。 
    - 若要擷取只 HTTP 閘道的認證，請使用`ClusterOperationsExtensions.GetGatewaySettings`。 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet) 現在已被取代，並已被取代`ClusterOperationsExtensions.UpdateGatewaySettings`。 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet) 並[ `DisableHttp` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet)現在已被取代。 HTTP 是現在一律啟用，因此不再需要這些方法。