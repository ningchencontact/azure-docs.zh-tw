---
title: 建立叢集錯誤字典
description: 瞭解如何建立叢集錯誤字典。
ms.reviewer: hrasheed
author: karkrish
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: troubleshooting
ms.date: 11/19/2019
ms.author: v-todmc
ms.openlocfilehash: e537014f741196024c24dd6ee98af0d8af2e1b31
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75483059"
---
# <a name="hdinsight-cluster-creation-errors"></a>HDInsight：叢集建立錯誤

本文說明在建立叢集時所遇到之錯誤的解決方法。 

> [!NOTE]
> 下列清單中的前三個錯誤發生的原因是 HDInsight 產品使用 CsmDocument_2_0 類別時的驗證錯誤。



## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>錯誤碼： DeploymentDocument ' CsmDocument_2_0 ' 無法通過驗證

### <a name="error-script-action-location-cannot-be-accessed-uriscript-action-url"></a>**錯誤**：無法存取腳本動作位置 URI：\<腳本動作 URL\>

**錯誤訊息：「遠端伺服器傳回錯誤：（404）找不到」。**

### <a name="cause"></a>原因
在「建立叢集」要求中提供的腳本動作 URL 無法從 HDInsight 服務存取。 當我們嘗試存取腳本動作時，會收到「ErrorMessage」。

### <a name="resolution"></a>解析度 
  - 針對 HTTP/HTTPs URL，您可以嘗試從 incognito 瀏覽器視窗開啟 URL 來進行驗證。 
  - 針對 WASB URL，請確定腳本存在於 [要求] 中提供的儲存體帳戶中。 請確定此儲存體帳戶的儲存體金鑰是正確的。 
  - 針對 ADLS URL，請確定此腳本存在於儲存體帳戶中。

---

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>錯誤碼： DeploymentDocument ' CsmDocument_2_0 ' 無法通過驗證

### <a name="error-script-action-location-cannot-be-accessed-uri-script-action-url"></a>**錯誤**：無法存取腳本動作位置 URI： \<腳本動作 URL\>

**錯誤訊息：指定的腳本 URI \<腳本 URI\> 在 ADLS 中，但此叢集沒有 data lake storage 主體**

### <a name="cause"></a>原因
在建立叢集要求中提供的腳本動作 URL 無法從 HDInsight 服務存取。 當我們嘗試存取腳本動作時，會收到「ErrorMessage」。 

### <a name="resolution"></a>解析度

請確定對應的 Azure Data Lake 存放區 Gen 1 帳戶已新增至叢集。 用來存取 Azure Data Lake 存放區第1代帳戶的服務主體也會新增至叢集中。 

---

## <a name="error-code-deploymentdocument-csmdocument_2_0-failed-the-validation"></a>錯誤碼： DeploymentDocument ' CsmDocument_2_0 ' 無法通過驗證

### <a name="error-vm-size-customer-specified-vm-size-provided-in-the-request-is-invalid-or-not-supported-for-role-role-valid-values-are-valid-vm-size-for-role"></a>**錯誤**：要求中提供的 vm 大小 '\<客戶指定的 vm 大小\>' 無效，或不支援角色 '\<角色\>'。 有效的值為： \<角色\>的有效 VM 大小。

### <a name="cause"></a>原因
角色不允許客戶指定的 VM 大小。 這可能是因為 VM 大小值未如預期般運作，或不適合計算機角色。 

### <a name="resolution"></a>解析度
錯誤訊息會列出 VM 大小的有效值。 請選取其中一個有效的值，然後重試建立叢集要求。 

---

## <a name="error-codeinvalidvirtualnetworkid"></a>錯誤碼： InvalidVirtualNetworkId  

### <a name="error-the-virtualnetworkid-is-not-valid-virtualnetworkid-user_virtualnetworkid"></a>**錯誤**： VirtualNetworkId 無效。 VirtualNetworkId '\<USER_VIRTUALNETWORKID\>' *

### <a name="cause"></a>原因
在叢集建立期間指定的**VirtualNetworkId**值格式不正確。 

### <a name="resolution"></a>解析度
請確定**VirtualNetworkId**和子網的格式正確。 若要取得**VirtualnetworkId**值，請移至 [Azure 入口網站]，選取您的虛擬網路，然後選取功能表上的 [**屬性**]。 **ResourceID**屬性是**VirtualNetworkId**值。 

虛擬網路識別碼的範例如下： 

"/subscriptions/c15fd9b8-e2b8-1d4e-aa85-2e668040233b/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet" 

---

## <a name="error-code-customizationfailederrorcode"></a>錯誤碼： CustomizationFailedErrorCode

### <a name="error-cluster-deployment-failed-due-to-an-error-in-the-custom-script-action-failed-actions-script_name-please-go-to-ambari-ui-to-further-debug-the-failure"></a>**錯誤**：叢集部署因自訂腳本動作發生錯誤而失敗。 失敗的動作： \<SCRIPT_NAME\>，請移至 Ambari UI 以進一步偵測失敗。

### <a name="cause"></a>原因
在建立叢集要求期間所提供的使用者自訂腳本，會在成功部署叢集之後執行。 這個錯誤碼表示在以名稱 \<SCRIPT_NAME\>執行此自訂腳本時，發生錯誤。   

### <a name="resolution"></a>解析度
因為這是使用者的自訂腳本，使用者應該針對問題進行疑難排解，並視需要重新執行腳本。 若要針對腳本失敗進行疑難排解，請檢查/var/lib/ambari-agent/* 資料夾中的記錄檔。 或者，開啟 Ambari UI 中的 [作業] 頁面，然後選取**run_customscriptionaction**操作以查看錯誤詳細資料。 

---

## <a name="error-codeinvaliddocumenterrorcode"></a>錯誤碼： InvalidDocumentErrorCode

### <a name="error-the-meta_store_type-metastore-schema-version-metastore_major_version-in-database-database_name-is-incompatible-with-cluster-version-cluster_version"></a>**錯誤**：資料庫 METASTORE_MAJOR_VERSION\> \<中的 \<META_STORE_TYPE\> 中繼存放區架構版本 \<DATABASE_NAME\> 與叢集版本 \<CLUSTER_VERSION 不相容\>

### <a name="cause"></a>原因
自訂中繼存放區與選取的 HDInsight 叢集版本不相容。 目前，HDInsight 4.0 叢集僅支援中繼存放區第3版。*x*和 HDInsight 3.6 不支援中繼存放區第3版。*x*或更新版本。 

### <a name="resolution"></a>解析度
請務必使用每個 HDInsight 叢集版本所支援的中繼存放區版本。 請注意，如果未指定自訂中繼存放區，HDInsight 會在內部建立中繼存放區。 不過，此中繼存放區會在叢集刪除時自動刪除。 

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>錯誤碼： FailedToConnectWithClusterErrorCode 

### <a name="error-unable-to-connect-to-cluster-management-endpoint-to-perform-scaling-operation-verify-that-network-security-rules-are-not-blocking-external-access-to-the-cluster-and-that-the-cluster-manager-ambari-ui-can-be-successfully-accessed"></a>**錯誤**：無法連線到叢集管理端點以執行調整作業。 確認網路安全性規則不會封鎖對叢集的外部存取，而且可以成功存取叢集管理員（Ambari） UI。

### <a name="cause"></a>原因
您的網路安全性群組（NSG）上有防火牆規則，其封鎖與重要 Azure 健康狀態和管理服務的叢集通訊。 

### <a name="resolution"></a>解析度
如果您打算使用 **網路安全性群組** 來控制網路流量，請在安裝 HDInsight 之前，採取下列動作： 
  - 識別您要用於 HDInsight 的 Azure 區域。 
  - 識別 HDInsight 所需的 IP 位址。 如需詳細資訊，請參閱 [HDInsight 管理 IP 位址](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses)。 
    - 建立或修改您打算安裝 HDInsight 之子網的網路安全性群組。 
    - **網路安全性群組：** 允許來自 IP 位址的埠 **443** 的 **輸入** 流量。 這可確保 HDInsight 管理服務可以從虛擬網路外部連線到叢集。 

---

## <a name="error-code-storagepermissionsblockedformsi"></a>錯誤碼： StoragePermissionsBlockedForMsi  

### <a name="error-the-managed-identity-does-not-have-permissions-on-the-storage-account-please-verify-that-storage-blob-data-owner-role-is-assigned-to-the-managed-identity-for-the-storage-account-storage-subscriptions-subscription-id-resourcegroups-resource-group-name-providersmicrosoftstoragestorageaccounts-storage-account-name-managed-identity-subscriptions-subscription-id-resourcegroups--resource-group-name-providersmicrosoftmanagedidentityuserassignedidentities-user-managed-identity-name"></a>**錯誤**：受控識別沒有儲存體帳戶的許可權。 請確認「儲存體 Blob 資料擁有者」角色已指派給儲存體帳戶的受控識別。 儲存體：/subscriptions/\<訂用帳戶識別碼\>/resourceGroups/\< 資源組名\>/providers/Microsoft.Storage/storageAccounts/\<儲存體帳戶名稱\>，受控識別：/subscriptions/\<訂閱識別碼\>/resourceGroups//\< 資源組名\>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/\<使用者受控識別名稱\>

### <a name="cause"></a>原因
未提供所需的許可權來**管理身分識別。** **使用者指派的受控識別**在 ADLS Gen2 儲存體帳戶上沒有 Blob 儲存體參與者角色。 

### <a name="resolution"></a>解析度

開啟 Azure 入口網站，移至您的儲存體帳戶，查看 [**存取控制（IAM）** ]，並確定儲存體 Blob 資料參與者或儲存體 Blob 資料擁有者角色具有 [已指派] 存取權給訂用帳戶的 **使用者指派受控識別**。 如需詳細資訊，請參閱在[Data Lake Storage Gen2 帳戶上設定受控識別的許可權](hdinsight-hadoop-use-data-lake-storage-gen2.md)。 

---

## <a name="error-code-invalidnetworksecuritygroupsecurityrules"></a>錯誤碼：出現 invalidnetworksecuritygroupsecurityrules  

### <a name="error-the-security-rules-in-the-network-security-group-subscriptionssubscriptionidresourcegroupsresource-group-name-defaultprovidersmicrosoftnetworknetworksecuritygroupsnetwork-security-group-name-configured-with-subnet-subscriptionssubscriptionidresourcegroupsresource-group-name-rg-westeurope-vnet-tomtom-defaultprovidersmicrosoftnetworkvirtualnetworksvirtual-network-namesubnetssubnet-name"></a>**錯誤**：網路安全性群組/Subscriptions/中的安全性規則\<SubscriptionID >\/resourceGroups/<Resource Group name> 預設/提供者/Microsoft. Network/networkSecurityGroups/\<網路安全性群組名稱\> 設定為子網/Subscriptions/\<SubscriptionID >\/resourceGroups/\<資源組名 >/\/虛擬網路名稱 >\/子網/\<子網名稱\> 
不允許必要的輸入和/或輸出連線能力。 如需詳細資訊，請造訪[規劃虛擬網路以取得 Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment)或聯絡支援。 * *

### <a name="cause"></a>原因
如果您使用網路安全性群組（Nsg）或使用者定義的路由（Udr）來控制 HDInsight 叢集的輸入流量，您必須確定您的叢集可以與重要的 Azure 健康狀態和管理服務進行通訊。

### <a name="resolution"></a>解析度
如果您打算使用 **網路安全性群組** 來控制網路流量，請在安裝 HDInsight 之前，採取下列動作： 
  - 識別您打算用於 HDInsight 的 Azure 區域，並為您的區域建立安全的 IP 位址清單：[健全狀況和管理服務：特定區域](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses#health-and-management-services-specific-regions)。
  - 識別 HDInsight 所需的 IP 位址。 如需詳細資訊，請參閱 [HDInsight 管理 IP 位址](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses)。 
  - 建立或修改您打算安裝 HDInsight 之子網的網路安全性群組。 **網路安全性群組**：允許埠 **443**上的輸入流量從 IP 位址 。 這可確保 HDInsight 管理服務可以從虛擬網路外部連線到叢集。
  
---

## <a name="error-code-cluster-setup-failed-to-install-components-on-one-or-more-hosts"></a>錯誤碼：叢集安裝程式無法在一或多部主機上安裝元件

###  <a name="errorcluster-setup-failed-to-install-components-on-one-or-more-hosts-please-retry-your-request"></a>**錯誤**：叢集安裝程式無法在一或多部主機上安裝元件。 請重試您的要求。 

### <a name="cause"></a>原因 
通常當發生暫時性問題或 Azure 中斷時，就會產生此錯誤。 

### <a name="resolution"></a>解析度

檢查[azure 狀態](https://status.azure.com/status)頁面中可能會影響叢集部署的任何潛在 Azure 中斷。 如果沒有任何中斷，請重試叢集部署。 

## <a name="next-steps"></a>後續步驟

若要深入瞭解叢集建立的錯誤疑難排解，請參閱[使用 Azure HDInsight 疑難排解叢集建立失敗](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-troubleshoot-cluster-creation-fails)。
