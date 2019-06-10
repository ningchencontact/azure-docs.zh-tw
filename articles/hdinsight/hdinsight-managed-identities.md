---
title: 在 Azure HDInsight 中的受管理身分識別
description: 概述在 Azure HDInsight 中的受管理身分識別的實作。
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: hrasheed
ms.openlocfilehash: 30631c4b71d1e8f3b0380a39bab49b900df32621
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427643"
---
# <a name="managed-identities-in-azure-hdinsight"></a>在 Azure HDInsight 中的受管理身分識別

受控身分識別是在 Azure Active Directory (Azure AD) 認證並由 Azure 管理中註冊的身分識別。 使用受管理的身分識別，您不需要在 Azure AD 中註冊服務主體，或維護認證，例如憑證。

受管理的身分識別可以用於 Azure HDInsight，以允許您的叢集，以存取 Azure AD 網域服務、 存取 Azure Key Vault，或存取 Azure Data Lake 儲存體 Gen2 中的檔案。

有兩種類型的受管理的身分識別： 指派給使用者和系統指派。 Azure HDInsight 會使用使用者指派給受控身分識別。 使用者指派的受控身分識別會建立為獨立的 Azure 資源，這樣就可以指派給一或多個 Azure 服務執行個體。 相反地，系統指派的受控身分識別是 Azure AD 中建立，並自動啟用特定的 Azure 服務執行個體上直接。 該系統指派的受控身分識別生命週期再繫結至已啟用服務執行個體的生命週期。

## <a name="hdinsight-managed-identity-implementation"></a>HDInsight 受控身分識別實作

在 Azure HDInsight 中管理的身分識別佈建叢集的每個節點上。 這些身分識別元件，不過，僅供 HDInsight 服務。 目前沒有任何支援的方法，以產生使用 HDInsight 叢集節點上安裝受管理的身分識別的存取權杖。 某些 Azure 服務管理的身分識別會實作具有端點可供您取得存取權杖來與您自己的其他 Azure 服務互動。

## <a name="create-a-managed-identity"></a>建立受控身分識別

可以使用下列方法之一來建立受控身分識別：

* [Azure 入口網站](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* [Azure Resource Manager](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)
* [Azure CLI](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)

設定受管理的身分識別的其餘步驟取決於要使用的案例。

## <a name="managed-identity-scenarios-in-azure-hdinsight"></a>在 Azure HDInsight 中的受管理的身分識別案例

受管理的身分識別是多個案例中使用 Azure HDInsight。 請參閱詳細的安裝和組態指示的相關文件：

* [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md#create-a-user-assigned-managed-identity)
* [企業安全性套件](domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-and-authorize-a-managed-identity)
* [Kafka 攜帶您自己的金鑰 (BYOK)](kafka/apache-kafka-byok.md#get-started-with-byok)

## <a name="next-steps"></a>後續步驟

* [什麼是適用於 Azure 資源的受控識別？](../active-directory/managed-identities-azure-resources/overview.md)
