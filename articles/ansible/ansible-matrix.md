---
title: 適用於 Azure 的 Ansible 模組和版本對照表 |Microsoft Docs
description: Azure 的 Ansible 模組和版本對照表
keywords: ansible, 角色, 對照表, 版本, azure, devops
ms.topic: reference
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: 3f5bda36368ee1fe1f37527422c6072c3ffda177
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2019
ms.locfileid: "63763321"
---
# <a name="ansible-module-and-version-matrix"></a>Ansible 模組和版本對照表

Ansible 會包含在佈建和設定 Azure 資源模組以使用一套。 這些資源包括虛擬機器、 擴展集，網路服務和容器服務。 本文會列出各種的 Ansible 模組，適用於 Azure 和它們附的 Ansible 版本。

## <a name="ansible-modules-for-azure"></a>Azure 的 Ansible 模組

可以執行下列的模組，直接在遠端主機上或透過劇本。

這些模組可從 Ansible 的正式發行，以及從下列 Microsoft 劇本角色。

| Azure 的 Ansible 模組                   |  Ansible 2.4 |  Ansible 2.5 |  Ansible 2.6 | Ansible 2.7 | Ansible 2.8 | Ansible 角色 | 
|---------------------------------------------|--------------|--------------|-----------------------------|-------------------------------------|--------------|--------------| 
| **計算**                    |           |                          |                          |                            |           |           |
| azure_rm_availabilityset                    | 是          | 是                         | 是          | 是          | 是          | 是          |
| azure_rm_availabilityset_facts              | 是          | 是                         | 是          | 是          | 是          | 是          |
| azure_rm_deployment                         | 是          | 是                         | 是          | 是          | 是          | 是          |
| azure_rm_deployment_facts                   | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_functionapp                        | 是          | 是                         | 是          | 是          | 是          | 是          |
| azure_rm_functionapp_facts                  | 是          | 是                         | 是          | 是          | 是          | 是          |
| azure_rm_image                              | -            | 是                         | 是          | 是          | 是          | 是          |
| azure_rm_image_facts                        | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_resource                           | -            | -                           | 是          | 是          | 是          | 是          |
| azure_rm_resource_facts                     | -            | -                           | 是          | 是          | 是          | 是          |
| azure_rm_resourcegroup                      | 是          | 是                         | 是          | 是          | 是          | 是          |
| azure_rm_resourcegroup_facts                | 是          | 是                         | 是          | 是          | 是          | 是          |
| azure_rm_virtualmachine                     | 是          | 是                         | 是          | 是          | 是          | 是          |
| azure_rm_virtualmachine_facts               | -            | -                           | -            | 是          | 是          | 是          |
| azure_rm_virtualmachineextension           | 是          | 是                         | 是          | 是          | 是          | 是          |
| azure_rm_virtualmachineextension_facts      | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_virtualmachineimage_facts          | 是          | 是                         | 是          | 是          | 是          | 是          |
| azure_rm_virtualmachinescaleset            | 是          | 是                         | 是          | 是          | 是          | 是          |
| azure_rm_virtualmachinescaleset_facts      | 是          | 是                         | 是          | 是          | 是          | 是          |
| azure_rm_virtualmachinescalesetextension    | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_virtualmachinescalesetextension_facts | -            | -                        | -            | -            | 是          | 是          |
| azure_rm_virtualmachinescalesetinstance     | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_virtualmachinescalesetinstance_facts | -            | -                         | -            | -            | 是          | 是          |
| **網路功能**                              |              |                             |              |              |              |              |
| azure_rm_appgateway                         | -            | -                           | -            | 是          | 是          | 是          |
| azure_rm_appgwroute                         | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_appgwroute                         | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_appgwroute_facts                   | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_appgwroutetable                    | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_appgwroutetable_facts              | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_applicationsecuritygroup           | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_applicationsecuritygroup_facts     | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_cdnendpoint                        | -            | -                         | -          | -            | 是          | 是          |
| azure_rm_cdnendpoint_facts                  | -            | -                         | -          | -            | 是          | 是          |
| azure_rm_cdnprofile                         | -            | -                         | -          | -            | 是          | 是          |
| azure_rm_cdnprofile_facts                   | -            | -                         | -          | -            | 是          | 是          |
| azure_rm_dnsrecordset                       | 是          | 是                         | 是          | 是          | 是          | 是          |
| azure_rm_dnsrecordset_facts                 | 是          | 是                         | 是          | 是          | 是          | 是          |
| azure_rm_dnszone                            | 是          | 是                         | 是          | 是          | 是          | 是          |
| azure_rm_dnszone_facts                      | 是          | 是                         | 是          | 是          | 是          | 是          |
| azure_rm_loadbalancer                       | 是          | 是                         | 是          | 是          | 是          | 是          |
| azure_rm_loadbalancer_facts                 | 是          | 是                         | 是          | 是          | 是          | 是          |
| azure_rm_networkinterface                   | 是          | 是                         | 是          | 是          | 是          | 是          |
| azure_rm_networkinterface_facts             | 是          | 是                         | 是          | 是          | 是          | 是          |
| azure_rm_publicipaddress                    | 是          | 是                         | 是          | 是          | 是          | 是          |
| azure_rm_publicipaddress_facts              | 是          | 是                         | 是          | 是          | 是          | 是          |
| azure_rm_route                              | -            | -                           | -            | 是          | 是          | 是          |
| azure_rm_routetable                         | -            | -                           | -            | 是          | 是          | 是          |
| azure_rm_routetable_facts                   | -            | -                           | -            | 是          | 是          | 是          |
| azure_rm_securitygroup                      | 是          | 是                         | 是          | 是          | 是          | 是          |
| azure_rm_subnet                             | 是          | 是                         | 是          | 是          | 是          | 是          |
| azure_rm_subnet_facts                       | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_trafficmanagerendpoint             | -            | -                         | -          | 是          | 是          | 是          |
| azure_rm_trafficmanagerendpoint_facts       | -            | -                         | -          | 是          | 是          | 是          |
| azure_rm_trafficmanagerprofile              | -            | -                         | -          | 是          | 是          | 是          |
| azure_rm_trafficmanagerprofile_facts        | -            | -                         | -          | 是          | 是          | 是          |
| azure_rm_virtualnetwork                     | 是          | 是                         | 是          | 是          | 是          | 是          |
| azure_rm_virtualnetwork_facts               | 是          | 是                         | 是          | 是          | 是          | 是          |
| azure_rm_virtualnetworkpeering              | -            | -                         | -          | -            | 是          | 是          |
| **儲存體**                    |           |                          |                          |                            |           |           |
| azure_rm_manageddisk                        | 是          | 是                         | 是          | 是          | 是          | 是          |
| azure_rm_manageddisk_facts                  | 是          | 是                         | 是          | 是          | 是          | 是          |
| azure_rm_storageaccount                     | 是          | 是                         | 是          | 是          | 是          | 是          |
| azure_rm_storageaccount_facts               | 是          | 是                         | 是          | 是          | 是          | 是          |
| azure_rm_storageblob                        | 是          | 是                         | 是          | 是          | 是          | 是          |
| **Web**                    |           |                          |                          |                             |           |           |
| azure_rm_appserviceplan                     | -            | -                         | -          | 是          | 是          | 是          |
| azure_rm_appserviceplan_facts               | -            | -                         | -          | 是          | 是          | 是          |
| azure_rm_webapp                             | -            | -                         | -          | 是          | 是          | 是          |
| azure_rm_webapp_facts                       | -            | -                         | -          | 是          | 是          | 是          |
| azure_rm_webappslot                         | -            | -                         | -          | -            | 是          | 是          |
| **容器**                    |           |                          |                          |                            |           |           |
| azure_rm_acs                                | 是          | 是                         | 是          | 是          | 是          | 是          |
| azure_rm_aks                                | -            | -                           | 是          | 是          | 是          | 是          |
| azure_rm_aks_facts                          | -            | -                           | 是          | 是          | 是          | 是          |
| azure_rm_aksversion_facts                   | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_containerinstance                  | -            | 是                         | 是          | 是          | 是          | 是          |
| azure_rm_containerinstance_facts            | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_containerregistry                  | -            | 是                         | 是          | 是          | 是          | 是          |
| azure_rm_containerregistry_facts            | -            | -                           | -            | 是          | 是          | 是          |
| azure_rm_containerregistryreplication       | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_containerregistryreplication_facts | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_containerregistrywebhook           | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_containerregistrywebhook_facts     | -            | -                           | -            | -            | 是          | 是          |
| **資料庫**                    |           |                          |                          |                             |           |           |
| azure_rm_cosmosdbaccount                    | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_cosmosdbaccount_facts              | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_mariadbconfiguration               | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_mariadbconfiguration_facts         | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_mariadbdatabase                    | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_mariadbdatabase_facts              | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_mariadbfirewallrule                | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_mariadbfirewallrule_facts          | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_mariadbserver                      | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_mariadbserver_facts                | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_mysqlconfiguration                 | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_mysqlconfiguration_facts           | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_mysqldatabase                      | -            | 是                         | 是          | 是          | 是          | 是          |
| azure_rm_mysqldatabase_facts                | -            | -                           | -            | 是          | 是          | 是          |
| azure_rm_mysqlfirewallrule                  | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_mysqlfirewallrule_facts            | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_mysqlserver                        | -            | 是                         | 是          | 是          | 是          | 是          |
| azure_rm_mysqlserver_facts                  | -            | -                           | -            | 是          | 是          | 是          |
| azure_rm_postgresqlconfiguration            | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_postgresqlconfiguration_facts      | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_postgresqldatabase                 | -            | 是                         | 是          | 是          | 是          | 是          |
| azure_rm_postgresqldatabase_facts           | -            | -                           | -            | 是          | 是          | 是          |
| azure_rm_postgresqlfirewallrule             | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_postgresqlfirewallrule_facts       | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_postgresqlserver                   | -            | 是                         | 是          | 是          | 是          | 是          |
| azure_rm_postgresqlserver_facts             | -            | -                           | -            | 是          | 是          | 是          |
| azure_rm_rediscache                         | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_rediscache_facts                   | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_rediscachefirewallrule             | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_sqldatabase                        | -            | 是                         | 是          | 是          | 是          | 是          |
| azure_rm_sqldatabase_facts                  | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_sqlelasticpool                     | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_sqlelasticpool_facts               | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_sqlfirewallrule                    | -            | -                           | -            | 是          | 是          | 是          |
| azure_rm_sqlfirewallrule_facts              | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_sqlserver                          | -            | 是                         | 是          | 是          | 是          | 是          |
| azure_rm_sqlserver_facts                    | -            | 是                         | 是          | 是          | 是          | 是          |
| **分析**                    |           |                          |                          |                             |           |           |
| azure_rm_hdinsightcluster                   | -            | -                           | -            | -            | 是          | 是          |
| **整合**                    |           |                          |                          |                             |           |           |
| azure_rm_servicebus                         | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_servicebus_facts                   | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_servicebusqueue                    | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_servicebussaspolicy                | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_servicebustopic                    | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_servicebustopicsubscription        | -            | -                           | -            | -            | 是          | 是          |
| **安全性**                    |           |                          |                          |                             |           |           |
| azure_rm_keyvault                           | -            | 是                         | 是          | 是          | 是          | 是          |
| azure_rm_keyvault_facts                     | -            | -                           | -              | -          | 是          | 是          |
| azure_rm_keyvaultkey                        | -            | 是                         | 是          | 是          | 是          | 是          |
| azure_rm_keyvaultsecret                     | -            | 是                         | 是          | 是          | 是          | 是          |
| azure_rm_roleassignment                     | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_roleassignment_facts               | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_roledefinition                     | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_roledefinition_facts               | -            | -                           | -            | -            | 是          | 是          |
| **DevOps**               |           |                          |                          |                             |           |           |
| azure_rm_devtestlab                         | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_devtestlab_facts                   | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_devtestlabarmtemplate_facts        | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_devtestlabartifact_facts           | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_devtestlabartifactsource           | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_devtestlabartifactsource_facts     | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_devtestlabcustomimage              | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_devtestlabenvironment              | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_devtestlabpolicy                   | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_devtestlabschedule                 | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_devtestlabvirtualmachine           | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_devtestlabvirtualmachine_facts | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_devtestlabvirtualnetwork           | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_devtestlabvirtualnetwork_facts     | -            | -                           | -            | -            | 是          | 是          |
| **Azure 監視器**          |           |                          |                          |                             |           |           |
| azure_rm_autoscale                  | -            | -                         | -          | 是          | 是          | 是          |
| azure_rm_autoscale_facts            | -            | -                         | -          | 是          | 是          | 是          |
| azure_rm_loganalyticsworkspace              | -            | -                           | -            | -            | 是          | 是          |
| azure_rm_loganalyticsworkspace_facts        | -            | -                           | -            | -            | 是          | 是          |

## <a name="introduction-to-playbook-role-for-azure"></a>Azure 劇本角色簡介

[Azure_preview_module 劇本角色](https://galaxy.ansible.com/Azure/azure_preview_modules/)包含所有最新的 Azure 模組。 相較於官方 Ansible 版本，此角色會更加及時地推出更新和錯誤修正。 如果您使用 Ansible 佈建用途的 Azure 資源時，我們鼓勵您安裝`azure_preview_module`劇本角色。

`azure_preview_module`劇本角色每三週發行。

## <a name="next-steps"></a>後續步驟

如需劇本角色的詳細資訊，請參閱[建立可重複使用的劇本](https://docs.ansible.com/ansible/latest/playbooks_reuse.html)。 