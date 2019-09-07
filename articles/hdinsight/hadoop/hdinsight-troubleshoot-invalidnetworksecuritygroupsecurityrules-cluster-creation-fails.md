---
title: 出現 invalidnetworksecuritygroupsecurityrules-叢集在 Azure HDInsight 中失敗
description: 叢集建立失敗，錯誤碼為出現 invalidnetworksecuritygroupsecurityrules
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/31/2019
ms.openlocfilehash: a534ae54b6cb9ce20d0a1bc970ef575f88a09827
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70735758"
---
# <a name="scenario-invalidnetworksecuritygroupsecurityrules---cluster-creation-fails-in-azure-hdinsight"></a>案例：出現 invalidnetworksecuritygroupsecurityrules-叢集建立在 Azure HDInsight 中失敗

本文說明與 Azure HDInsight 叢集互動時，問題的疑難排解步驟和可能的解決方法。

## <a name="issue"></a>問題

您會收到錯誤碼`InvalidNetworkSecurityGroupSecurityRules` ，其描述類似于「使用子網設定的網路安全性群組中的安全性規則不允許必要的輸入和/或輸出連線能力」。

## <a name="cause"></a>原因

您的叢集所設定的輸入[網路安全性群組](../../virtual-network/virtual-network-vnet-plan-design-arm.md)規則可能發生問題。

## <a name="resolution"></a>解析度

移至 [Azure 入口網站]，並找出與部署叢集的子網相關聯的 NSG。 在 [**輸入安全性規則**] 區段中，請確定規則允許在[此處](../hdinsight-plan-virtual-network-deployment.md#hdinsight-ip)所述的 IP 位址對埠443進行輸入存取。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過[Azure 社區支援](https://azure.microsoft.com/support/community/)取得 azure 專家的解答。

* [@AzureSupport](https://twitter.com/azuresupport)連接-官方 Microsoft Azure 帳戶，藉由將 Azure 社區連接至適當的資源來改善客戶體驗：解答、支援及專家。

* 如果您需要更多協助，您可以從[Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列選取 [**支援**]，或開啟 [說明 **+ 支援**] 中樞。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 您的 Microsoft Azure 訂用帳戶包含訂用帳戶管理和帳單支援的存取權，而技術支援則透過其中一項[Azure 支援方案](https://azure.microsoft.com/support/plans/)提供。
