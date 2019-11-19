---
title: 適用于 Azure 的 Ansible 模組和版本對照表 |Microsoft Docs
description: Azure 的 Ansible 模組和版本對照表
keywords: ansible, 角色, 對照表, 版本, azure, devops
ms.topic: reference
ms.date: 10/14/2019
ms.openlocfilehash: 54e27c7570ba1cdbce7355740181d68a2f3efbac
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2019
ms.locfileid: "74155977"
---
# <a name="ansible-module-and-version-matrix"></a>Ansible 模組和版本對照表

Ansible 包含一組用來佈建和設定 Azure 資源的模組。 這些資源包括虛擬機器、擴展集、網路服務和容器服務。 本文列出適用于 Azure 的各種 Ansible 模組和其隨附的 Ansible 版本。

## <a name="ansible-modules-for-azure"></a>Azure 的 Ansible 模組

下列模組可以直接在遠端主機上執行，或透過操作手冊來執行。  

這些模組可從 Ansible 官方版本和下列 Microsoft 腳本角色取得。

> [!NOTE]
> 從 Ansible 2.9 開始，我們會將所有 * _facts 模組重新命名為 * _info，以符合 Ansible 命名慣例。 舊的和重新命名的模組會相互連結，而不會看到取代警告，所有模組都會如以往一樣正常執行。

| Azure 的 Ansible 模組                   |  Ansible 2.4 |  Ansible 2.5 |  Ansible 2.6 | Ansible 2.7 | Ansible 2。8 | Ansible 2。9 | Ansible 角色 | 
|---------------------------------------------|--------------|--------------|-----------------------------|-------------------------------------|--------------|--------------|--------------|  
| **計算**                    |           |                          |                          |                            |           |           |           |
| azure_rm_availabilityset                   | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_availabilityset_info              | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_batchaccount                       | -            | -                           | -            | -            | -            | yes          | yes          |
| azure_rm_deployment                         | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_deployment_info                   | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_functionapp                        | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_functionapp_info                  | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_gallery                            | -            | -                           | -            | -            | -            | yes          | yes          |
| azure_rm_gallery_info                       | -            | -                           | -            | -            | -            | yes          | yes          |
| azure_rm_galleryimage                       | -            | -                           | -            | -            | -            | yes          | yes          |
| azure_rm_galleryimage_info                  | -            | -                           | -            | -            | -            | yes          | yes          |
| azure_rm_galleryimageversion                | -            | -                           | -            | -            | -            | yes          | yes          |
| azure_rm_galleryimageversion_info           | -            | -                           | -            | -            | -            | yes          | yes          |
| azure_rm_image                              | -            | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_image_info                        | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_resource                           | -            | -                           | yes          | yes          | yes          | yes          | yes          |
| azure_rm_resource_info                     | -            | -                           | yes          | yes          | yes          | yes          | yes          |
| azure_rm_resourcegroup                      | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_resourcegroup_info                | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_snapshot                           | -            | -                           | -            | -            | -            | yes          | yes          |
| azure_rm_virtualmachine                     | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_virtualmachine_info               | -            | -                           | -            | yes          | yes          | yes          | yes          |
| azure_rm_virtualmachineextension            | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_virtualmachineextension_info      | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_virtualmachineimage_info          | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_virtualmachinescaleset             | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_virtualmachinescaleset_info       | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_virtualmachinescalesetextension    | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_virtualmachinescalesetextension_info | -            | -                        | -            | -            | yes          | yes          | yes          |
| azure_rm_virtualmachinescalesetinstance     | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_virtualmachinescalesetinstance_info | -            | -                         | -            | -            | yes          | yes          | yes          |
| **網路功能**                              |              |                             |              |              |              |              |              |
| azure_rm_appgateway                         | -            | -                           | -            | yes          | yes          | yes          | yes          |
| azure_rm_applicationsecuritygroup           | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_applicationsecuritygroup_info     | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_cdnendpoint                        | -            | -                         | -          | -            | yes          | yes          | yes          |
| azure_rm_cdnendpoint_info                  | -            | -                         | -          | -            | yes          | yes          | yes          |
| azure_rm_cdnprofile                         | -            | -                         | -          | -            | yes          | yes          | yes          |
| azure_rm_cdnprofile_info                   | -            | -                         | -          | -            | yes          | yes          | yes          |
| azure_rm_dnsrecordset                       | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_dnsrecordset_info                 | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_dnszone                            | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_dnszone_info                      | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_firewall                           | -            | -                           | -            | -            | -            | yes          | yes          |
| azure_rm_firewall_info                      | -            | -                           | -            | -            | -            | yes          | yes          |
| azure_rm_loadbalancer                       | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_loadbalancer_info                 | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_networkinterface                   | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_networkinterface_info             | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_publicipaddress                    | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_publicipaddress_info              | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_route                              | -            | -                           | -            | yes          | yes          | yes          | yes          |
| azure_rm_routetable                         | -            | -                           | -            | yes          | yes          | yes          | yes          |
| azure_rm_routetable_info                   | -            | -                           | -            | yes          | yes          | yes          | yes          |
| azure_rm_securitygroup                      | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_securitygroup_info                 | -            | -                           | -            | -            | -            | yes          | yes          |
| azure_rm_subnet                             | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_subnet_info                       | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_trafficmanagerendpoint             | -            | -                         | -          | yes          | yes          | yes          | yes          |
| azure_rm_trafficmanagerendpoint_info       | -            | -                         | -          | yes          | yes          | yes          | yes          |
| azure_rm_trafficmanagerprofile              | -            | -                         | -          | yes          | yes          | yes          | yes          |
| azure_rm_trafficmanagerprofile_info        | -            | -                         | -          | yes          | yes          | yes          | yes          |
| azure_rm_virtualnetwork                     | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_virtualnetwork_info               | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_virtualnetworkgateway              | -            | -                         | -          | -            | yes          | yes          | yes          |
| azure_rm_virtualnetworkpeering              | -            | -                         | -          | -            | yes          | yes          | yes          |
| azure_rm_virtualnetworkpeering_info         | -            | -                         | -          | -            | -            | yes          | yes          |
| **儲存體**                    |           |                          |                          |                            |           |           |         |
| azure_rm_manageddisk                        | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_manageddisk_info                  | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_storageaccount                     | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_storageaccount_info               | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_storageblob                        | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| **Web**                    |           |                          |                          |                             |           |           |           |
| azure_rm_appserviceplan                     | -            | -                         | -          | yes          | yes          | yes          | yes          |
| azure_rm_appserviceplan_info               | -            | -                         | -          | yes          | yes          | yes          | yes          |
| azure_rm_webapp                             | -            | -                         | -          | yes          | yes          | yes          | yes          |
| azure_rm_webapp_info                       | -            | -                         | -          | yes          | yes          | yes          | yes          |
| azure_rm_webappslot                         | -            | -                         | -          | -            | yes          | yes          | yes          |
| **容器**                    |           |                          |                          |                            |           |           |          |
| azure_rm_acs                                | yes          | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_aks                                | -            | -                           | yes          | yes          | yes          | yes          | yes          |
| azure_rm_aks_info                          | -            | -                           | yes          | yes          | yes          | yes          | yes          |
| azure_rm_aksversion_info                   | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_containerinstance                  | -            | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_containerinstance_info            | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_containerregistry                  | -            | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_containerregistry_info            | -            | -                           | -            | yes          | yes          | yes          | yes          |
| azure_rm_containerregistryreplication       | -            | -                           | -            | -            | -          | -          | yes          |
| azure_rm_containerregistryreplication_info  | -            | -                           | -            | -            | -          | -          | yes          |
| azure_rm_containerregistrywebhook           | -            | -                           | -            | -            | -          | -          | yes          |
| azure_rm_containerregistrywebhook_info      | -            | -                           | -            | -            | -          | -          | yes          |
| **資料庫**                    |           |                          |                          |                             |           |           |          |
| azure_rm_cosmosdbaccount                    | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_cosmosdbaccount_info              | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_mariadbconfiguration               | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_mariadbconfiguration_info         | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_mariadbdatabase                    | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_mariadbdatabase_info              | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_mariadbfirewallrule                | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_mariadbfirewallrule_info          | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_mariadbserver                      | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_mariadbserver_info                | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_mysqlconfiguration                 | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_mysqlconfiguration_info           | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_mysqldatabase                      | -            | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_mysqldatabase_info                | -            | -                           | -            | yes          | yes          | yes          | yes          |
| azure_rm_mysqlfirewallrule                  | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_mysqlfirewallrule_info            | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_mysqlserver                        | -            | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_mysqlserver_info                  | -            | -                           | -            | yes          | yes          | yes          | yes          |
| azure_rm_postgresqlconfiguration            | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_postgresqlconfiguration_info      | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_postgresqldatabase                 | -            | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_postgresqldatabase_info           | -            | -                           | -            | yes          | yes          | yes          | yes          |
| azure_rm_postgresqlfirewallrule             | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_postgresqlfirewallrule_info       | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_postgresqlserver                   | -            | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_postgresqlserver_info             | -            | -                           | -            | yes          | yes          | yes          | yes          |
| azure_rm_rediscache                         | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_rediscache_info                   | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_rediscachefirewallrule             | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_sqldatabase                        | -            | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_sqldatabase_info                  | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_sqlelasticpool                    | -            | -                           | -            | -            | -          | -          | yes          |
| azure_rm_sqlelasticpool_info               | -            | -                           | -            | -            | -          | -          | yes          |
| azure_rm_sqlfirewallrule                    | -            | -                           | -            | yes          | yes          | yes          | yes          |
| azure_rm_sqlfirewallrule_info              | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_sqlserver                          | -            | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_sqlserver_info                    | -            | yes                         | yes          | yes          | yes          | yes          | yes          |
| **分析**                    |           |                          |                          |                             |           |           |          |
| azure_rm_hdinsightcluster                   | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_hdinsightcluster_info              | -            | -                           | -            | -            | -            | yes          | yes          |
| **整合**                    |           |                          |                          |                             |           |           |          |
| azure_rm_servicebus                         | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_servicebus_info                   | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_servicebusqueue                    | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_servicebussaspolicy                | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_servicebustopic                    | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_servicebustopicsubscription        | -            | -                           | -            | -            | yes          | yes          | yes          |
| **安全性**                    |           |                          |                          |                             |           |           |           |
| azure_rm_keyvault                           | -            | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_keyvault_info                     | -            | -                           | -              | -          | yes          | yes          | yes          |
| azure_rm_keyvaultkey                        | -            | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_keyvaultkey_info                   | -            | -                           | -            | -            | -            | yes          | yes          |
| azure_rm_keyvaultsecret                     | -            | yes                         | yes          | yes          | yes          | yes          | yes          |
| azure_rm_roleassignment                     | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_roleassignment_info               | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_roledefinition                     | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_roledefinition_info               | -            | -                           | -            | -            | yes          | yes          | yes          |
| **DevOps**               |           |                          |                          |                             |           |           |                  |
| azure_rm_devtestlab                         | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_devtestlab_info                   | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_devtestlabarmtemplate_info        | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_devtestlabartifact_info           | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_devtestlabartifactsource           | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_devtestlabartifactsource_info     | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_devtestlabcustomimage              | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_devtestlabcustomimage_info         | -            | -                           | -            | -            | -            | yes          | yes          |
| azure_rm_devtestlabenvironment              | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_devtestlabenvironment_info         | -            | -                           | -            | -            | -            | yes          | yes          |
| azure_rm_devtestlabpolicy                   | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_devtestlabpolicy_info              | -            | -                           | -            | -            | -            | yes          | yes          |
| azure_rm_devtestlabschedule                 | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_devtestlabschedule_info            | -            | -                           | -            | -            | -            | yes          | yes          |
| azure_rm_devtestlabvirtualmachine           | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_devtestlabvirtualmachine_info | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_devtestlabvirtualnetwork           | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_devtestlabvirtualnetwork_info     | -            | -                           | -            | -            | yes          | yes          | yes          |
| **Azure 監視器**                           |              |                           |            |              |                 |           |              |
| azure_rm_autoscale                          | -            | -                         | -          | yes          | yes          | yes          | yes          |
| azure_rm_autoscale_info                    | -            | -                         | -          | yes          | yes          | yes          | yes          |
| azure_rm_loganalyticsworkspace              | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_loganalyticsworkspace_info        | -            | -                           | -            | -            | yes          | yes          | yes          |
| azure_rm_monitorlogprofile                  | -            | -                           | -            | -            | -            | yes          | yes          |
| **管理與治理**     |              |                           |            |            |            |            |              |
| azure_rm_automationaccount        | -            | -                         | -          | -          | -          | yes        | yes          |
| azure_rm_automationaccount_info   | -            | -                         | -          | -          | -          | yes        | yes          |
| azure_rm_lock                     | -            | -                         | -          | -          | -          | yes        | yes          |
| azure_rm_lock_info                | -            | -                         | -          | -          | -          | yes        | yes          |
| **物聯網**            |              |                           |            |            |            |            |              |
| azure_rm_iotdevice                | -            | -                         | -          | -          | -          | yes        | yes          |
| azure_rm_iotdevice_info           | -            | -                         | -          | -          | -          | yes        | yes          |
| azure_rm_iotdevicemodule          | -            | -                         | -          | -          | -          | yes        | yes          |
| azure_rm_iothub_info              | -            | -                         | -          | -          | -          | yes        | yes          |
| azure_rm_iothub_info              | -            | -                         | -          | -          | -          | yes        | yes          |
| azure_rm_iothubconsumergroup      | -            | -                         | -          | -          | -          | yes        | yes          |

## <a name="introduction-to-playbook-role-for-azure"></a>Azure 劇本角色簡介

[Azure_preview_module 腳本角色](https://galaxy.ansible.com/Azure/azure_preview_modules/)包含所有最新的 azure 模組。 相較於官方 Ansible 版本，此角色會更加及時地推出更新和錯誤修正。 如果您將 Ansible 用於 Azure 資源布建用途，建議您安裝 `azure_preview_module` 腳本角色。

`azure_preview_module` 腳本角色每三周發行一次。

## <a name="next-steps"></a>後續步驟

如需腳本角色的詳細資訊，請參閱[建立可重複使用](https://docs.ansible.com/ansible/latest/playbooks_reuse.html)的腳本。 