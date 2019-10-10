---
title: 部署概觀 - Avere vFXT for Azure
description: 部署 Avere vFXT for Azure 的概觀
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: rohogue
ms.openlocfilehash: 9684b230b8790e36cd7442c65481c0c71ce185d6
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255419"
---
# <a name="avere-vfxt-for-azure---deployment-overview"></a>Avere vFXT for Azure - 部署概觀

此文章提供讓 Avere vFXT for Azure 叢集運作所需之步驟的概觀。

從 Azure Marketplace 建立 vFXT 叢集前後，必須執行幾項工作。 確切了解從開始到結束的程序將有助於預估所需精力。 

## <a name="deployment-steps"></a>部署步驟

在[規劃您的系統](avere-vfxt-deploy-plan.md)之後， 您就可以開始建立您的 Avere vFXT 叢集。 

Azure Marketplace 中的 Azure Resource Manager 範本會收集所需的資訊，並自動部署整個叢集。 

在 vFXT 叢集開始運作之後，您將必須了解如何讓用戶端與它連線，以及 (若有必要 ) 如何將您的資料移動到新的 Blob 儲存體容器。  

以下是所有步驟的概觀。

1. 設定必要條件 

   建立 VM 訂閱 之前，您必須先為 Avere vFXT 專案建立新訂用帳戶、設定訂用帳戶擁有權、檢查配額並在需要時要求增加配額，以及接受使用 Avere vFXT 軟體的條款。 閱讀[準備建立 Avere vFXT](avere-vfxt-prereqs.md) 以取得詳細指示。

1. 建立 Avere vFXT 叢集 

   使用 Azure Marketplace 建立 Avere vFXT for Azure 叢集。 範本會收集所需的資訊，並執行指令碼以建立最終產品。

   建立叢集時須執行下列步驟，而這全部都可透過 Marketplace 範本來完成： 

   * 建立新的網路基礎結構和資源群組 (如有需要)
   * 建立*叢集控制器*  

     叢集控制器是一個簡單 VM，位於與 Avere vFXT 叢集相同的虛擬網路中，且具有建立和管理叢集所需的自訂軟體。 控制器會建立 vFXT 節點並構成叢集，而且它也提供命令列介面讓您在叢集生命週期內管理叢集。

     如果您在部署期間建立新的 vnet，您的控制器將會有公用 IP 位址。 這表示控制器可以做為從 vnet 外部連接到叢集的跳躍主機。

   * 建立叢集節點 VM

   * 設定叢集節點 Vm 以形成叢集

   * (選擇性) 建立新的 Blob 容器，並將其設定為叢集的後端儲存體

1. 設定叢集 

   連線到 Avere vFXT 設定介面 (Avere 控制台) 以自訂叢集設定。 選擇加入支援監視，並新增您的儲存體系統 (若您使用內部部署資料中心)。

   * [存取 vFXT 叢集](avere-vfxt-cluster-gui.md)
   * [啟用支援](avere-vfxt-enable-support.md)
   * [設定儲存體](avere-vfxt-add-storage.md) (若有需要)

1. 裝載用戶端

   依照[裝載 Avere vFXT 叢集](avere-vfxt-mount-clients.md)中的指導方針執行，以了解負載平衡方式與用戶端電腦應該如何裝載叢集。

1. 新增資料 (若有需要)

   因為 Avere vFXT 是可調整規模的多用戶端快取，將資料移動到新後端儲存體容器的最佳方式是使用多用戶端、多執行緒檔案複製策略。 閱讀[將資料移動到 vFXT 叢集](avere-vfxt-data-ingest.md)以取得詳細資料。

## <a name="next-steps"></a>後續步驟

繼續閱讀[準備建立 Avere vFXT](avere-vfxt-prereqs.md)以完成部署 Avere vFXT for Azure 的預備工作。 