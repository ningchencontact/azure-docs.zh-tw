---
title: 適用於 Azure 的 Ansible 模組和版本對照表 |Microsoft Docs
description: Azure 的 Ansible 模組和版本對照表
keywords: ansible, 角色, 對照表, 版本, azure, devops
ms.topic: reference
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: b3396b2f4639cc7298b77810dbaafadd308d6b24
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2019
ms.locfileid: "65230988"
---
# <a name="ansible-module-and-version-matrix"></a>Ansible 模組和版本對照表

Ansible 會包含在佈建和設定 Azure 資源模組以使用一套。 這些資源包括虛擬機器、 擴展集，網路服務和容器服務。 本文會列出各種的 Ansible 模組，適用於 Azure 和它們附的 Ansible 版本。

## <a name="ansible-modules-for-azure"></a>Azure 的 Ansible 模組

可以執行下列的模組，直接在遠端主機上或透過劇本。

這些模組可從 Ansible 的正式發行，以及從下列 Microsoft 劇本角色。

| Azure 的 Ansible 模組                   |  Ansible 2.4 |  Ansible 2.5 |  Ansible 2.6 | Ansible 2.7 | Ansible 2.8 | Ansible 角色 | 
|---------------------------------------------|--------------|--------------|-----------------------------|-------------------------------------|--------------|--------------| 
| **計算**                    |           |                          |                          |                            |           |           |
| azure_rm_availabilityset                    | 有          | 是                         | 是          | 是          | 是          | 有          |
| azure_rm_availabilityset_facts              | 有          | 是                         | 是          | 是          | 是          | 有          |
| azure_rm_deployment                         | 有          | 是                         | 是          | 是          | 是          | 有          |
| azure_rm_deployment_facts                   | -            | -                           | -            | -            | 有          | 有          |
| azure_rm_functionapp                        | 有          | 是                         | 是          | 是          | 是          | 有          |
| azure_rm_functionapp_facts                  | 有          | 是                         | 是          | 是          | 是          | 有          |
| azure_rm_image                              | -            | 有                         | 是          | 是          | 是          | 有          |
| azure_rm_image_facts                        | -            | -                           | -            | -            | 有          | 有          |
| azure_rm_resource                           | -            | -                           | 有          | 是          | 是          | 有          |
| azure_rm_resource_facts                     | -            | -                           | 有          | 是          | 是          | 有          |
| azure_rm_resourcegroup                      | 有          | 是                         | 是          | 是          | 是          | 有          |
| azure_rm_resourcegroup_facts                | 有          | 是                         | 是          | 是          | 是          | 有          |
| azure_rm_virtualmachine                     | 有          | 是                         | 是          | 是          | 是          | 有          |
| azure_rm_virtualmachine_facts               | -            | -                           | -            | 有          | 是          | 有          |
| azure_rm_virtualmachineextension           | 有          | 是                         | 是          | 是          | 是          | 有          |
| azure_rm_virtualmachineextension_facts      | -            | -                           | -            | -            | 有          | 有          |
| azure_rm_virtualmachineimage_facts          | 有          | 是                         | 是          | 是          | 是          | 有          |
| azure_rm_virtualmachinescaleset            | 有          | 是                         | 是          | 是          | 是          | 有          |
| azure_rm_virtualmachinescaleset_facts      | 有          | 是                         | 是          | 是          | 是          | 有          |
| azure_rm_virtualmachinescalesetextension    | -            | -                           | -            | -            | 有          | 有          |
| azure_rm_virtualmachinescalesetextension_facts | -            | -                        | -            | -            | 有          | 有          |
| azure_rm_virtualmachinescalesetinstance     | -            | -                           | -            | -            | 有          | 有          |
| azure_rm_virtualmachinescalesetinstance_facts | -            | -                         | -            | -            | 有          | 有          |
| **網路功能**                              |              |                             |              |              |              |              |
| azure_rm_appgateway                         | -            | -                           | -            | 有          | 是          | 有          |
| azure_rm_appgwroute                         | -            | -                           | -            | -            | -          | 有          |
| azure_rm_appgwroute_facts                   | -            | -                           | -            | -            | -          | 有          |
| azure_rm_appgwroutetable                    | -            | -                           | -            | -            | -          | 有          |
| azure_rm_appgwroutetable_facts              | -            | -                           | -            | -            | -          | 有          |
| azure_rm_applicationsecuritygroup           | -            | -                           | -            | -            | 有          | 有          |
| azure_rm_applicationsecuritygroup_facts     | -            | -                           | -            | -            | 有          | 有          |
| azure_rm_cdnendpoint                        | -            | -                         | -          | -            | 有          | 有          |
| azure_rm_cdnendpoint_facts                  | -            | -                         | -          | -            | 有          | 有          |
| azure_rm_cdnprofile                         | -            | -                         | -          | -            | 有          | 有          |
| azure_rm_cdnprofile_facts                   | -            | -                         | -          | -            | 有          | 有          |
| azure_rm_dnsrecordset                       | 有          | 是                         | 是          | 是          | 是          | 有          |
| azure_rm_dnsrecordset_facts                 | 有          | 是                         | 是          | 是          | 是          | 有          |
| azure_rm_dnszone                            | 有          | 是                         | 是          | 是          | 是          | 有          |
| azure_rm_dnszone_facts                      | 有          | 是                         | 是          | 是          | 是          | 有          |
| azure_rm_loadbalancer                       | 有          | 是                         | 是          | 是          | 是          | 有          |
| azure_rm_loadbalancer_facts                 | 有          | 是                         | 是          | 是          | 是          | 有          |
| azure_rm_networkinterface                   | 有          | 是                         | 是          | 是          | 是          | 有          |
| azure_rm_networkinterface_facts             | 有          | 是                         | 是          | 是          | 是          | 有          |
| azure_rm_publicipaddress                    | 有          | 是                         | 是          | 是          | 是          | 有          |
| azure_rm_publicipaddress_facts              | 有          | 是                         | 是          | 是          | 是          | 有          |
| azure_rm_route                              | -            | -                           | -            | 有          | 是          | 有          |
| azure_rm_routetable                         | -            | -                           | -            | 有          | 是          | 有          |
| azure_rm_routetable_facts                   | -            | -                           | -            | 有          | 是          | 有          |
| azure_rm_securitygroup                      | 有          | 是                         | 是          | 是          | 是          | 有          |
| azure_rm_subnet                             | 有          | 是                         | 是          | 是          | 是          | 有          |
| azure_rm_subnet_facts                       | -            | -                           | -            | -            | 有          | 有          |
| azure_rm_trafficmanagerendpoint             | -            | -                         | -          | 有          | 是          | 有          |
| azure_rm_trafficmanagerendpoint_facts       | -            | -                         | -          | 有          | 是          | 有          |
| azure_rm_trafficmanagerprofile              | -            | -                         | -          | 有          | 是          | 有          |
| azure_rm_trafficmanagerprofile_facts        | -            | -                         | -          | 有          | 是          | 有          |
| azure_rm_virtualnetwork                     | 有          | 是                         | 是          | 是          | 是          | 有          |
| azure_rm_virtualnetwork_facts               | 有          | 是                         | 是          | 是          | 是          | 有          |
| azure_rm_virtualnetworkpeering              | -            | -                         | -          | -            | 有          | 有          |
| **儲存體**                    |           |                          |                          |                            |           |           |
| azure_rm_manageddisk                        | 有          | 是                         | 是          | 是          | 是          | 有          |
| azure_rm_manageddisk_facts                  | 有          | 是                         | 是          | 是          | 是          | 有          |
| azure_rm_storageaccount                     | 有          | 是                         | 是          | 是          | 是          | 有          |
| azure_rm_storageaccount_facts               | 有          | 是                         | 是          | 是          | 是          | 有          |
| azure_rm_storageblob                        | 有          | 是                         | 是          | 是          | 是          | 有          |
| **Web**                    |           |                          |                          |                             |           |           |
| azure_rm_appserviceplan                     | -            | -                         | -          | 有          | 是          | 有          |
| azure_rm_appserviceplan_facts               | -            | -                         | -          | 有          | 是          | 有          |
| azure_rm_webapp                             | -            | -                         | -          | 有          | 是          | 有          |
| azure_rm_webapp_facts                       | -            | -                         | -          | 有          | 是          | 有          |
| azure_rm_webappslot                         | -            | -                         | -          | -            | 有          | 有          |
| **容器**                    |           |                          |                          |                            |           |           |
| azure_rm_acs                                | 有          | 是                         | 是          | 是          | 是          | 有          |
| azure_rm_aks                                | -            | -                           | 有          | 是          | 是          | 有          |
| azure_rm_aks_facts                          | -            | -                           | 有          | 是          | 是          | 有          |
| azure_rm_aksversion_facts                   | -            | -                           | -            | -            | 有          | 有          |
| azure_rm_containerinstance                  | -            | 有                         | 是          | 是          | 是          | 有          |
| azure_rm_containerinstance_facts            | -            | -                           | -            | -            | 有          | 有          |
| azure_rm_containerregistry                  | -            | 有                         | 是          | 是          | 是          | 有          |
| azure_rm_containerregistry_facts            | -            | -                           | -            | 有          | 是          | 有          |
| azure_rm_containerregistryreplication       | -            | -                           | -            | -            | 有          | 有          |
| azure_rm_containerregistryreplication_facts | -            | -                           | -            | -            | 有          | 有          |
| azure_rm_containerregistrywebhook           | -            | -                           | -            | -            | 有          | 有          |
| azure_rm_containerregistrywebhook_facts     | -            | -                           | -            | -            | 有          | 有          |
| **資料庫**                    |           |                          |                          |                             |           |           |
| azure_rm_cosmosdbaccount                    | -            | -                           | -            | -            | 有          | 有          |
| azure_rm_cosmosdbaccount_facts              | -            | -                           | -            | -            | 有          | 有          |
| azure_rm_mariadbconfiguration               | -            | -                           | -            | -            | 有          | 有          |
| azure_rm_mariadbconfiguration_facts         | -            | -                           | -            | -            | 有          | 有          |
| azure_rm_mariadbdatabase                    | -            | -                           | -            | -            | 有          | 有          |
| azure_rm_mariadbdatabase_facts              | -            | -                           | -            | -            | 有          | 有          |
| azure_rm_mariadbfirewallrule                | -            | -                           | -            | -            | 有          | 有          |
| azure_rm_mariadbfirewallrule_facts          | -            | -                           | -            | -            | 有          | 有          |
| azure_rm_mariadbserver                      | -            | -                           | -            | -            | 有          | 有          |
| azure_rm_mariadbserver_facts                | -            | -                           | -            | -            | 有          | 有          |
| azure_rm_mysqlconfiguration                 | -            | -                           | -            | -            | 有          | 有          |
| azure_rm_mysqlconfiguration_facts           | -            | -                           | -            | -            | 有          | 有          |
| azure_rm_mysqldatabase                      | -            | 有                         | 是          | 是          | 是          | 有          |
| azure_rm_mysqldatabase_facts                | -            | -                           | -            | 有          | 是          | 有          |
| azure_rm_mysqlfirewallrule                  | -            | -                           | -            | -            | 有          | 有          |
| azure_rm_mysqlfirewallrule_facts            | -            | -                           | -            | -            | 有          | 有          |
| azure_rm_mysqlserver                        | -            | 有                         | 是          | 是          | 是          | 有          |
| azure_rm_mysqlserver_facts                  | -            | -                           | -            | 有          | 是          | 有          |
| azure_rm_postgresqlconfiguration            | -            | -                           | -            | -            | 有          | 有          |
| azure_rm_postgresqlconfiguration_facts      | -            | -                           | -            | -            | 有          | 有          |
| azure_rm_postgresqldatabase                 | -            | 有                         | 是          | 是          | 是          | 有          |
| azure_rm_postgresqldatabase_facts           | -            | -                           | -            | 有          | 是          | 有          |
| azure_rm_postgresqlfirewallrule             | -            | -                           | -            | -            | 有          | 有          |
| azure_rm_postgresqlfirewallrule_facts       | -            | -                           | -            | -            | 有          | 有          |
| azure_rm_postgresqlserver                   | -            | 有                         | 是          | 是          | 是          | 有          |
| azure_rm_postgresqlserver_facts             | -            | -                           | -            | 有          | 是          | 有          |
| azure_rm_rediscache                         | -            | -                           | -            | -            | 有          | 有          |
| azure_rm_rediscache_facts                   | -            | -                           | -            | -            | 有          | 有          |
| azure_rm_rediscachefirewallrule             | -            | -                           | -            | -            | 有          | 有          |
| azure_rm_sqldatabase                        | -            | 有                         | 是          | 是          | 是          | 有          |
| azure_rm_sqldatabase_facts                  | -            | -                           | -            | -            | 有          | 有          |
| azure_rm_sqlelasticpool                     | -            | -                           | -            | -            | 有          | 有          |
| azure_rm_sqlelasticpool_facts               | -            | -                           | -            | -            | 有          | 有          |
| azure_rm_sqlfirewallrule                    | -            | -                           | -            | 有          | 是          | 有          |
| azure_rm_sqlfirewallrule_facts              | -            | -                           | -            | -            | 有          | 有          |
| azure_rm_sqlserver                          | -            | 有                         | 是          | 是          | 是          | 有          |
| azure_rm_sqlserver_facts                    | -            | 有                         | 是          | 是          | 是          | 有          |
| **分析**                    |           |                          |                          |                             |           |           |
| azure_rm_hdinsightcluster                   | -            | -                           | -            | -            | 有          | 有          |
| **整合**                    |           |                          |                          |                             |           |           |
| azure_rm_servicebus                         | -            | -                           | -            | -            | 有          | 有          |
| azure_rm_servicebus_facts                   | -            | -                           | -            | -            | 有          | 有          |
| azure_rm_servicebusqueue                    | -            | -                           | -            | -            | 有          | 有          |
| azure_rm_servicebussaspolicy                | -            | -                           | -            | -            | 有          | 有          |
| azure_rm_servicebustopic                    | -            | -                           | -            | -            | 有          | 有          |
| azure_rm_servicebustopicsubscription        | -            | -                           | -            | -            | 有          | 有          |
| **安全性**                    |           |                          |                          |                             |           |           |
| azure_rm_keyvault                           | -            | 有                         | 是          | 是          | 是          | 有          |
| azure_rm_keyvault_facts                     | -            | -                           | -              | -          | 有          | 有          |
| azure_rm_keyvaultkey                        | -            | 有                         | 是          | 是          | 是          | 有          |
| azure_rm_keyvaultsecret                     | -            | 有                         | 是          | 是          | 是          | 有          |
| azure_rm_roleassignment                     | -            | -                           | -            | -            | 有          | 有          |
| azure_rm_roleassignment_facts               | -            | -                           | -            | -            | 有          | 有          |
| azure_rm_roledefinition                     | -            | -                           | -            | -            | 有          | 有          |
| azure_rm_roledefinition_facts               | -            | -                           | -            | -            | 有          | 有          |
| **DevOps**               |           |                          |                          |                             |           |           |
| azure_rm_devtestlab                         | -            | -                           | -            | -            | 有          | 有          |
| azure_rm_devtestlab_facts                   | -            | -                           | -            | -            | 有          | 有          |
| azure_rm_devtestlabarmtemplate_facts        | -            | -                           | -            | -            | 有          | 有          |
| azure_rm_devtestlabartifact_facts           | -            | -                           | -            | -            | 有          | 有          |
| azure_rm_devtestlabartifactsource           | -            | -                           | -            | -            | 有          | 有          |
| azure_rm_devtestlabartifactsource_facts     | -            | -                           | -            | -            | 有          | 有          |
| azure_rm_devtestlabcustomimage              | -            | -                           | -            | -            | 有          | 有          |
| azure_rm_devtestlabenvironment              | -            | -                           | -            | -            | 有          | 有          |
| azure_rm_devtestlabpolicy                   | -            | -                           | -            | -            | 有          | 有          |
| azure_rm_devtestlabschedule                 | -            | -                           | -            | -            | 有          | 有          |
| azure_rm_devtestlabvirtualmachine           | -            | -                           | -            | -            | 有          | 有          |
| azure_rm_devtestlabvirtualmachine_facts | -            | -                           | -            | -            | 有          | 有          |
| azure_rm_devtestlabvirtualnetwork           | -            | -                           | -            | -            | 有          | 有          |
| azure_rm_devtestlabvirtualnetwork_facts     | -            | -                           | -            | -            | 有          | 有          |
| **Azure 監視器**          |           |                          |                          |                             |           |           |
| azure_rm_autoscale                  | -            | -                         | -          | 有          | 是          | 有          |
| azure_rm_autoscale_facts            | -            | -                         | -          | 有          | 是          | 有          |
| azure_rm_loganalyticsworkspace              | -            | -                           | -            | -            | 有          | 有          |
| azure_rm_loganalyticsworkspace_facts        | -            | -                           | -            | -            | 有          | 有          |

## <a name="introduction-to-playbook-role-for-azure"></a>Azure 劇本角色簡介

[Azure_preview_module 劇本角色](https://galaxy.ansible.com/Azure/azure_preview_modules/)包含所有最新的 Azure 模組。 相較於官方 Ansible 版本，此角色會更加及時地推出更新和錯誤修正。 如果您使用 Ansible 佈建用途的 Azure 資源時，我們鼓勵您安裝`azure_preview_module`劇本角色。

`azure_preview_module`劇本角色每三週發行。

## <a name="next-steps"></a>後續步驟

如需劇本角色的詳細資訊，請參閱[建立可重複使用的劇本](https://docs.ansible.com/ansible/latest/playbooks_reuse.html)。 
