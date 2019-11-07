---
title: 使用 Azure 事件方格來接收和回應金鑰保存庫通知
description: 了解如何整合 Key Vault 與 Azure 事件方格。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: tutorial
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: 3b24da4d988554da240baba2984df44ff4744aaf
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464097"
---
# <a name="how-to-receive-and-respond-to-key-vault-notifications-with-azure-event-grid-preview"></a>作法：使用 Azure 事件方格來接收和回應金鑰保存庫通知 (預覽)

Key Vault 與 Azure 事件方格的整合 (目前處於預覽狀態) 可讓使用者在金鑰保存庫中儲存的秘密變更狀態時收到通知。 如需此功能的概觀，請參閱[使用 Azure 事件方格監視 Key Vault](event-grid-overview.md)。

本指南將說明如何透過 Azure 事件方格接收 Key Vault 通知，以及如何使用 Azure 自動化回應狀態變更。

## <a name="prerequisites"></a>必要條件

- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- Azure 訂用帳戶中的金鑰保存庫。 您可以遵循[使用 Azure CLI 從 Azure Key Vault 設定和擷取秘密](quick-create-cli.md)中的步驟，快速建立新的金鑰保存庫。

## <a name="concepts"></a>概念

Azure Event Grid 是一項雲端事件服務。 在本指南中，您將訂閱金鑰保存庫的事件，並將事件路由傳送至 Azure 自動化。 當金鑰保存庫中的其中一個秘密即將過期時，事件方格會收到狀態變更通知並對端點進行 HTTP POST。 然後，Webhook 會觸發 PowerShell 指令碼的 Azure 自動化執行。

![image](media/image1.png)

## <a name="create-an-azure-automation-account"></a>建立 Azure 自動化帳戶

透過 [Azure 入口網站](https://portal.azure.com)建立 Azure 自動化帳戶。

1.  移至 portal.azure.com 並登入您的訂用帳戶。

1.  在搜尋方塊中，輸入「自動化帳戶」。

1.  在搜尋列下拉式清單的 [服務] 區段底下，選取 [自動化帳戶]。

1.  按一下 [加入]。

    ![](media/image2.png)

1.  在 [新增自動化帳戶] 刀鋒視窗中填寫必要資訊，然後選取 [建立]。

## <a name="create-a-runbook"></a>建立 Runbook

Azure 自動化帳戶準備就緒之後，請建立 Runbook。

![](media/image3.png)

1.  選取您剛建立的自動化帳戶。

1.  在 [程序自動化] 區段下方選取 [Runbook]。

1.  按一下 [建立 Runbook]。

1.  為 Runbook 命名，然後選取 [PowerShell] 作為 Runbook 類型。

1.  按一下您所建立的 Runbook，然後選取 [編輯] 按鈕。

1.  輸入下列程式碼 (用於測試目的)，然後按一下 [發佈] 按鈕。 這會輸出所接收 POST 要求的結果。

```azurepowershell
param
(
[Parameter (Mandatory = $false)]
[object] $WebhookData
)

#If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData) {

#rotate secret:
#generate new secret version in key vault
#update db/service with generated secret

#Write-Output "WebhookData <$WebhookData>"
Write-Output $WebhookData.RequestBody
}
else
{
# Error
write-Error "No input data found." 
}
```

![](media/image4.png)

## <a name="create-a-webhook"></a>建立 Webhook

現在建立 Webhook，以觸發新建立的 Runbook。

1.  從您剛發佈 Runbook 的 [資源] 區段中選取 [Webhook]。

1.  按一下 [新增 Webhook]。

    ![](media/image5.png)

1.  選取 [建立新的 Webhook]。

1. 為 Webhook 命名、設定到期日，然後**複製 URL**。

    > [!IMPORTANT] 
    > 建立 URL 之後，您就無法加以檢視。 務必將複本儲存在您可存取的安全位置，以供本指南的其餘部分使用。

1. 按一下 [參數與回合設定]，然後選取 [確定]。 請勿輸入任何參數。 這會啟用 [建立] 按鈕。

1. 選取 [確定]，然後選取 [建立]。

    ![](media/image6.png)

## <a name="create-an-event-grid-subscription"></a>建立事件格線訂用帳戶

透過 [Azure 入口網站](https://portal.azure.com)建立事件方格訂用帳戶。

1.  使用下列連結來開啟 Azure 入口網站： https://portal.azure.com/?Microsoft_Azure_KeyVault_ShowEvents=true&Microsoft_Azure_EventGrid_publisherPreview=true

1.  移至您的金鑰保存庫，然後選取 [事件] 索引標籤。如果您看不到 [事件] 索引標籤，請確定您使用的是[預覽版的入口網站](https://ms.portal.azure.com/?Microsoft_Azure_KeyVault_ShowEvents=true&Microsoft_Azure_EventGrid_publisherPreview=true)。

    ![](media/image7.png)

1.  按一下 [+ 事件訂用帳戶] 按鈕。

1.  建立訂用帳戶的描述性名稱。

1.  選擇 [事件方格結構描述]。

1.  「主題資源」應該是您要監視其狀態變更的金鑰保存庫。

1.  針對 [篩選至事件類型]，全部保持已核取狀態 (「已選取 9 項」)。

1.  針對 [端點類型]，選取 [Webhook]。

1.  選取 [選取端點]。 在新的內容窗格中，將[建立 Webhook](#create-a-webhook) 步驟中的 Webhook URL 貼到 [訂閱者端點] 欄位中。

1.  在內容窗格中選取 [確認選取項目]。

1.  選取 [建立]。

    ![](media/image8.png)

## <a name="test-and-verify"></a>測試和驗證

確認您的事件方格訂用帳戶已設定屬性。  這項測試假設您已在[建立事件方格訂用帳戶](#create-an-event-grid-subscription)中訂閱「已建立新版的秘密」通知，而且您有必要權限可在金鑰保存庫中建立新版的秘密。

![](media/image9.png)

![](media/image10.png)

1.  移至 Azure 入口網站上您的金鑰保存庫

1.  建立新的祕密。 基於測試目的，請將到期日設定為後一天。

1.  瀏覽至金鑰保存庫中的 [事件] 索引標籤。

1.  選取您建立的事件方格訂用帳戶。

1.  在 [計量] 底下，查看是否已擷取事件。 預期會有兩個事件：SecretNewVersion 和 SecretNearExpiry。 這會驗證事件方格是否成功擷取到金鑰保存庫中秘密的狀態變更。

    ![](media/image11.png)

1.  移至您的「Azure 自動化」帳戶。

1.  選取 [Runbook] 索引標籤，然後選取您建立的 Runbook。

1.  選取 [Webhook] 索引標籤，然後確認「上次觸發」時間戳記是在您建立新秘密時的 60 秒內。  這可確認事件方格已使用金鑰保存庫中狀態變更的事件詳細資料對 Webhook 進行 POST，然後觸發 Webhook。

    ![](media/image12.png)

1. 返回您的 Runbook，然後選取 [概觀] 索引標籤。

1. 查看 [最近的作業] 清單。 您應會看到已建立一項作業，且狀態為 [完成]。  這會確認 Webhook 觸發了 Runbook 來開始執行其指令碼。

    ![](media/image13.png)

1. 選取最近的作業，然後查看從事件方格傳送到 Webhook 的 POST 要求。 檢查 JSON，並確定您金鑰保存庫和事件類型的參數均正確無誤。 如果 JSON 物件中的「事件類型」參數符合金鑰保存庫中發生的事件 (在此範例中是 Microsoft.KeyVault.SecretNearExpiry)，則測試成功。

## <a name="troubleshooting"></a>疑難排解

### <a name="unable-to-create-event-subscription"></a>無法建立事件訂用帳戶

在您的 Azure 訂用帳戶資源提供者中重新註冊事件方格和 Key Vault 提供者。 請參閱 [Azure 資源提供者和類型](../azure-resource-manager/resource-manager-supported-services.md)。

## <a name="next-steps"></a>後續步驟

恭喜！ 如果您已遵循上述所有步驟，您現在已準備好以程式設計方式回應金鑰保存庫中所儲存秘密的狀態變更。

如果您已使用輪詢型系統來尋找金鑰保存庫中秘密的狀態變更，請轉為使用此通知功能。 您也可以將 Runbook 中的測試指令碼取代為程式碼，在秘密即將到期時以程式設計方式進行更新。

深入了解：

- [Azure Key Vault 概觀](key-vault-overview.md)
- [Azure 事件方格概觀](../event-grid/overview.md)
- [使用 Azure 事件方格監視 Key Vault (預覽)](event-grid-overview.md)
- [Azure Key Vault 的 Azure 事件方格事件結構描述 (預覽)](../event-grid/event-schema-key-vault.md)
- [Azure 自動化概觀](../automation/index.yml)
