---
title: Azure Red Hat OpenShift 叢集系統管理員角色 |Microsoft Docs
description: Azure Red Hat OpenShift 叢集系統管理員角色指派和使用方式
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: 6d7aacea5e34e21513452880448227a1ca5f9b67
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709945"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Azure Red Hat OpenShift 客戶系統管理員角色

作為 OpenShift 叢集的 ARO （Azure Red Hat OpenShift）叢集系統管理員，您的帳戶對所有使用者建立的專案具有較高的許可權和存取權。

當您的帳戶已系結 osa-客戶-管理員授權角色時，它就可以自動管理專案。

> [!Note] 
> osa-客戶-系統管理 clusterrole 與叢集管理員 clusterrole 不同


例如，您可以執行與一組動詞（`create`）相關聯的動作，以操作一組資源名稱（`templates`）。 若要查看這些角色及其動詞和資源集合的詳細資料，請執行下列命令：

`$ oc describe clusterrole/osa-customer-admin`

動詞名稱不一定會直接對應至 oc 命令，而是通常等同于您可以執行的 CLI 作業類型。 例如，具有 @no__t 0 動詞命令表示您可以顯示指定資源名稱的所有物件清單（`oc get`），而 @no__t 2 動詞命令則表示您可以在知道特定物件的名稱（`oc describe`）時顯示其詳細資料。

## <a name="how-to-configure-customer-administrator-role"></a>如何設定客戶系統管理員角色

客戶系統管理員角色只能在叢集建立期間，藉由提供旗標 `--customer-admin-group-id` 來設定。 如何設定 Azure Active Directory 和系統管理員群組遵循操作指南：[Azure Red Hat OpenShift 的 Azure Active Directory 整合](howto-aad-app-configuration.md)

## <a name="next-steps"></a>後續步驟

如何設定 osa-客戶-管理員角色：
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift 的 Azure Active Directory 整合](howto-aad-app-configuration.md)
