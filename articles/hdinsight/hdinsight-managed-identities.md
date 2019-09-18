---
title: Azure HDInsight 中的受控識別
description: 提供 Azure HDInsight 中受控識別的執行總覽。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: 02ea164a1fa29b494801623d418be73fc47d069c
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/17/2019
ms.locfileid: "71077070"
---
# <a name="managed-identities-in-azure-hdinsight"></a>Azure HDInsight 中的受控識別

受控識別是在 Azure Active Directory （Azure AD）中註冊的身分識別，其認證是由 Azure 管理。 使用受控識別時，您不需要在 Azure AD 中註冊服務主體，或維護憑證之類的認證。

受控識別可以在 Azure HDInsight 中用來允許您的叢集存取 Azure Data Lake Storage Gen2 中 Azure AD 網域服務、存取 Azure Key Vault 或存取檔案。

受控識別有兩種類型：使用者指派和系統指派。 Azure HDInsight 會使用使用者指派的受控識別。 使用者指派的受控識別會建立為獨立的 Azure 資源，您可以接著將它指派給一或多個 Azure 服務實例。 相反地，系統指派的受控識別會建立在 Azure AD 中，然後直接在特定 Azure 服務實例上啟用。 系統指派的受控識別的生命週期接著會系結至其啟用所在之服務實例的生命週期。

## <a name="hdinsight-managed-identity-implementation"></a>HDInsight 受控識別執行

在 Azure HDInsight 中，受管理的身分識別會布建在叢集的每個節點上。 不過，這些身分識別元件僅供 HDInsight 服務使用。 目前沒有任何支援的方法可讓您使用安裝在 HDInsight 叢集節點上的受控識別來產生存取權杖。 針對某些 Azure 服務，受控識別會使用端點來執行，您可以用來取得存取權杖，以便自行與其他 Azure 服務互動。

## <a name="create-a-managed-identity"></a>建立受控識別

您可以使用下列任何方法來建立受控識別：

* [Azure 入口網站](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* [Azure Resource Manager](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)
* [Azure CLI](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)

設定受控識別的其餘步驟取決於將使用它的案例。

## <a name="managed-identity-scenarios-in-azure-hdinsight"></a>Azure HDInsight 中的受控識別案例

受控識別會在多個案例的 Azure HDInsight 中使用。 如需詳細的安裝和設定指示，請參閱相關檔：

* [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md#create-a-user-assigned-managed-identity)
* [企業安全性套件](domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-and-authorize-a-managed-identity)
* [Kafka 攜帶您自己的金鑰（BYOK）](kafka/apache-kafka-byok.md#get-started-with-byok)

## <a name="next-steps"></a>後續步驟

* [什麼是適用於 Azure 資源的受控識別？](../active-directory/managed-identities-azure-resources/overview.md)
