---
title: 疑難排解 Azure 的 Red Hat OpenShift |Microsoft Docs
description: 疑難排解及解決 Azure Red Hat OpenShift 的一般問題
services: container-service
author: tylermsft
ms.author: twhitney
manager: jeconnoc
ms.service: container-service
ms.topic: troubleshooting
ms.date: 05/08/2019
ms.openlocfilehash: 9ef32273c007e7319b7f6df99f3d904f805c31f2
ms.sourcegitcommit: f013c433b18de2788bf09b98926c7136b15d36f1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2019
ms.locfileid: "65550918"
---
# <a name="troubleshooting-for-azure-red-hat-openshift"></a>Azure 的 Red Hat OpenShift 的疑難排解

本文詳細說明在建立或管理 Microsoft Azure 的 Red Hat OpenShift 叢集時發生的一些常見問題。

## <a name="retrying-the-creation-of-a-failed-cluster"></a>重試失敗的叢集建立

如果建立 Azure 的 Red Hat OpenShift 叢集使用`az`CLI 命令失敗，重試建立就會一直失敗。
使用`az openshift delete`刪除失敗的叢集，然後建立全新的叢集。

## <a name="untrusted-azure-red-hat-openshift-server-certificate"></a>不受信任的 Azure Red Hat OpenShift 伺服器憑證

OpenShift 主控台會建立一個自我簽署憑證。 當瀏覽器中出現系統提示時，請略過警告並接受「不受信任」的憑證。

## <a name="hidden-azure-red-hat-openshift-cluster-resource-group"></a>隱藏的 Azure Red Hat OpenShift 叢集資源群組

目前， `Microsoft.ContainerService/openShiftManagedClusters` Azure CLI 會自動建立的資源 (`az openshift create`命令) 會隱藏在 Azure 入口網站中。 在 **資源群組**檢視中，核取**顯示隱藏的類型**若要檢視資源群組。

![在入口網站中的隱藏的類型核取方塊的螢幕擷取畫面](./media/aro-portal-hidden-type.png)

## <a name="creating-a-cluster-results-in-error-that-no-registered-resource-provider-found"></a>沒有已註冊的資源提供者找到的錯誤在建立叢集結果

如果在錯誤中建立的叢集結果`No registered resource provider found for location '<location>' and API version '2019-04-30' for type 'openShiftManagedClusters'. The supported api-versions are '2018-09-30-preview`，則您已預覽的一部分，而且現在需要[採購 Azure 虛擬機器保留執行個體](https://aka.ms/openshift/buy)使用正式推出的產品。 保留可減少您預先支付受完整管理的 Azure 服務費用。 請參閱[*什麼是 Azure 保留*](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)若要進一步了解保留項目，以及如何它們節省成本。

## <a name="next-steps"></a>後續步驟

- 請嘗試[Red Hat OpenShift 說明中心](https://help.openshift.com/)如需詳細的 OpenShift 疑難排解。

- 尋找解答[關於 Azure Red Hat OpenShift 的常見問題集](openshift-faq.md)。