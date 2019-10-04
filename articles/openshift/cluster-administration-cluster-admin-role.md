---
title: Azure Red Hat OpenShift 叢集系統管理員角色 |Microsoft Docs
description: Azure Red Hat OpenShift 叢集系統管理員角色的指派和使用
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: 1c676b2671b73084a2b4ae8908acb83c23a59b7b
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/03/2019
ms.locfileid: "71936934"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Azure Red Hat OpenShift 客戶系統管理員角色

您是 Azure Red Hat OpenShift 叢集的叢集系統管理員。 您的帳戶已增加許可權，並可存取所有使用者建立的專案。

當您的帳戶已系結 osa-客戶-管理員授權角色時，它就可以自動管理專案。

> [!Note] 
> Osa-客戶-管理員叢集角色與叢集管理員叢集角色不同。


例如，您可以執行與一組動詞（`create`）相關聯的動作，以操作一組資源名稱（`templates`）。 若要查看這些角色及其動詞和資源集合的詳細資料，請執行下列命令：

`$ oc describe clusterrole/osa-customer-admin`

動詞名稱不一定全部直接對應到 `oc` 命令。 它們通常等同于您可以執行之 CLI 作業的類型。 

例如，具有 `list` 動詞表示您可以顯示資源名稱的所有物件清單（`oc get`）。 @No__t-0 動詞命令表示您可以在知道特定物件的名稱（`oc describe`）時，顯示其詳細資料。

## <a name="configure-the-customer-administrator-role"></a>設定客戶系統管理員角色

只有在叢集建立期間，您可以藉由提供旗標 `--customer-admin-group-id` 來設定客戶系統管理員角色。 若要瞭解如何設定 Azure Active Directory 和 Administrators 群組，請參閱[Azure Red Hat 的 Azure Active Directory 整合 OpenShift](howto-aad-app-configuration.md)。

## <a name="next-steps"></a>後續步驟

設定 osa-客戶-管理員角色：
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift 的 Azure Active Directory 整合](howto-aad-app-configuration.md)
