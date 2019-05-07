---
title: Azure Cosmos DB etcd API 簡介
description: 這篇文章提供 Azure Cosmos DB 中的 etcd API 的概觀和索引鍵的優點
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: 6c7fcb1429438ee024cb226b63cfcdcab05ed9f8
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65205794"
---
# <a name="introduction-to-the-azure-cosmos-db-etcd-api-preview"></a>Azure Cosmos DB etcd API （預覽） 簡介

Azure Cosmos DB 是 Microsoft 的全域分散式多模型資料庫服務，供任務關鍵性應用程式。 它提供周全全域散發、 彈性調整的輸送量和儲存體、 個位數毫秒延遲時間的第 99 個百分位數，並保證高可用性，全都享有領先業界的 SLA 的。

[Etcd](https://github.com/etcd-io/etcd)是分散式的索引鍵/值存放區。 在  [Kubernetes](https://kubernetes.io/)，etcd 用來儲存的 Kubernetes 叢集組態和狀態。 確保可用性、 可靠性和效能是 etcd 的整體叢集健全狀況、 延展性、 彈性的可用性和 Kubernetes 叢集的效能很重要的。 

Azure Cosmos DB 中的 etcd API 可讓您使用 Azure Cosmos DB 做為後端存放區[Azure Kubernetes](../aks/index.yml)。 Azure Cosmos DB 中的 etcd API 目前為預覽狀態。 Azure Cosmos DB 實作 etcd 有線通訊協定。 使用 Azure Cosmos DB 中的 etcd API，讓開發人員自動獲得高度可靠，[可用](high-availability.md)，[分散在世界各地](distribute-data-globally.md)Kubernetes。 此 API 可讓開發人員在完全受控的雲端原生 PaaS 服務可以擴充 Kubernetes 狀態管理。 

> [!NOTE]
> 不同於 Azure Cosmos DB 中的其他 Api，您無法佈建的 etcd API 帳戶，透過 Azure 入口網站、 CLI 或 Sdk。 您可以藉由部署 Resource Manager 範本; 佈建 etcd API 帳戶如需詳細步驟，請參閱 <<c0> [ 如何佈建使用 Azure Cosmos DB 的 Azure Kubernetes](bootstrap-kubernetes-cluster.md)文章。 Azure Cosmos DB etcd API 目前處於有限預覽狀態。 您可以[註冊預覽版](https://aka.ms/cosmosetcdapi-signup)，填寫註冊表單。

## <a name="wire-level-compatibility"></a>網路相容性層級

Azure Cosmos DB 實作 etcd 第 3 版，有線通訊協定，並可讓[主要節點](https://kubernetes.io/docs/concepts/overview/components/)API 伺服器，以使用 Azure Cosmos DB，它會在本機安裝的 etcd 環境中執行一樣。 Etcd API 支援的 TLS 相互驗證。 

下圖顯示的 Kubernetes 叢集的元件。 在叢集主機上，API 伺服器會使用 Azure Cosmos DB etcd API，而不是安裝在本機的 etcd。 

![Azure Cosmos DB 實作 etcd 有線-通訊協定](./media/etcd-api-introduction/etcd-api-wire-protocol.png)

## <a name="key-benefits"></a>主要權益

### <a name="no-etcd-operations-management"></a>沒有 etcd 作業管理

Azure Cosmos DB 受到完整管理的原生雲端服務，不需要設定和管理 etcd Kubernetes 開發人員。 Azure Cosmos DB 中的 API etcd 是可擴充、 高可用性、 容錯，，並提供高效能。 額外負荷的設定複寫，跨多個節點，執行輪流更新，安全性修補程式，以及監視 etcd 健全狀況由 Azure Cosmos DB 的方式來處理。

### <a name="global-distribution--high-availability"></a>全域散發和高可用性 

藉由使用 etcd API，Azure Cosmos DB 讀取資料保證 99.99%的可用性，並跨多個區域寫入在單一區域，並可享有達 99.999%的可用性。 

### <a name="elastic-scalability"></a>彈性延展性

Azure Cosmos DB 會提供彈性的延展性進行讀取和寫入要求跨不同區域。
Kubernetes 叢集中增加時，Azure Cosmos DB 中的 etcd API 帳戶彈性會調整而不需要停機。 在 Azure Cosmos DB 中，儲存 etcd 資料，而不 Kubernetes 的主要節點也可讓更有彈性調整的主要節點。 

### <a name="security--enterprise-readiness"></a>安全性與企業整備

當 etcd 資料會儲存在 Azure Cosmos DB 中時，Kubernetes 開發人員會自動獲得[內建的加密在靜止](database-encryption-at-rest.md)，[認證與合規性](compliance.md)，和[備份與還原功能](online-backup-and-restore.md)Azure Cosmos DB 所支援。 

## <a name="next-steps"></a>後續步驟

* [如何使用 Azure Kubernetes 使用 Azure Cosmos DB](bootstrap-kubernetes-cluster.md)
* [Azure Cosmos DB 的主要優點](introduction.md)
* [AKS 引擎快速入門指南](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md)