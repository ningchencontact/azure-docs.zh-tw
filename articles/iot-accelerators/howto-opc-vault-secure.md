---
title: 使用 OPC 保存庫保護 OPC UA 裝置的通訊-Azure |Microsoft Docs
description: 如何註冊 OPC UA 應用程式, 以及如何使用 OPC 保存庫為您的 OPC UA 裝置發行已簽署的應用程式憑證。
author: mregen
ms.author: mregen
ms.date: 8/16/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: ae10bb3488c21b6637163e333231e95a18c652bf
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/23/2019
ms.locfileid: "69996059"
---
# <a name="how-to-use-the-opc-vault-certificate-management-service"></a>如何使用 OPC 保存庫憑證管理服務

本文說明如何註冊應用程式, 以及如何為您的 OPC UA 裝置發行已簽署的應用程式憑證。

## <a name="prerequisites"></a>必要條件

### <a name="deploy-the-certificate-management-service"></a>部署憑證管理服務

首先, 服務必須部署到 Azure 雲端。
請尋找說明如何[部署 OPC 保存庫憑證管理服務](howto-opc-vault-deploy.md)的文章。

### <a name="create-the-issuer-ca-certificate"></a>建立簽發者 CA 憑證

如果您尚未這麼做, 請建立簽發者 CA 憑證。

尋找說明如何[建立及管理 OPC 保存庫之簽發者憑證](howto-opc-vault-manage.md)的文章。

## <a name="secure-opc-ua-applications"></a>保護 OPC UA 應用程式

### <a name="step-1-register-your-opc-ua-application"></a>步驟 1:註冊您的 OPC UA 應用程式 

> [!IMPORTANT]
> 需要「寫入者」角色才能註冊應用程式。

1. 在中開啟您的`https://myResourceGroup-app.azurewebsites.net`憑證服務, 然後登入。
2. 瀏覽到 `Register New` 頁面。
1. 若為應用程式註冊, 使用者必須至少指派「寫入者」角色。
2. 輸入表單會遵循 OPC UA 世界中的命名慣例。 例如, 在下列圖片中, OPC UA 中的[OPC Ua Reference Server](https://github.com/OPCFoundation/UA-.NETStandard/tree/master/SampleApplications/Workshop/Reference)範例設定。會顯示 NetStandard 堆疊:

   ![UA 參考伺服器註冊](media/howto-opc-vault-secure/reference-server-registration.png "UA 參考伺服器註冊")

5. 按下`Register`按鈕, 在憑證服務應用程式資料庫中註冊應用程式。 工作流程會直接引導使用者進行下一個步驟, 以要求應用程式的簽署憑證。

### <a name="step-2-secure-your-application-with-a-ca-signed-application-certificate"></a>步驟 2:使用 CA 簽署的應用程式憑證來保護您的應用程式

您可以根據憑證簽署要求 (CSR) 發行已簽署的憑證, 或要求新的金鑰組 (也包括 PFX 或 PEM 格式的新私密金鑰) 來保護 OPC UA 應用程式。 請遵循您的 OPC UA 裝置檔, 以瞭解您的應用程式支援的方法。 一般情況下, 建議使用 CSR 方法, 因為它不需要透過網路傳送私用金鑰。

- 如果您的裝置需要發出新的金鑰組, 請遵循[Step3a](##step-3a-request-a-new-certificate-with-a-new-keypair)。
- 如果您的裝置支援發行憑證簽署要求 (CSR), 請遵循[Step3b](#step-3b-request-a-new-certificate-with-a-csr)。

### <a name="step-3a-request-a-new-certificate-with-a-new-keypair"></a>步驟 3a：使用新的金鑰組來要求新的憑證

1. 瀏覽到 `Applications` 頁面。
3. 選擇`New Request`列出的應用程式來啟動憑證要求工作流程。

   ![要求新憑證](media/howto-opc-vault-secure/request-new-certificate.png "要求新憑證")

3. 按 [要求新的金鑰組和憑證], 以使用您應用程式的公開金鑰來要求私密金鑰和新的已簽署憑證。

   ![產生新的金鑰]組(media/howto-opc-vault-secure/generate-new-key-pair.png "產生新的金鑰")組

4. 在表單中填入主旨、功能變數名稱, 然後選擇 [以密碼提供私密金鑰的 PEM 或 PFX]。 按下`Generate New Certificate`按鈕以建立憑證要求。

   ![核准憑證](media/howto-opc-vault-secure/approve-reject.png "核准憑證")

5. 核准步驟需要使用者具有「核准者」角色, 並具有 Azure Key Vault 的簽署許可權。 在一般工作流程中, 必須將核准者和要求者角色指派給不同的使用者。
6. 核准或拒絕憑證要求, 以開始或取消實際建立金鑰組和簽署作業。 新的金鑰組會建立並安全地儲存在 Azure Key Vault 中, 直到憑證要求者下載為止。 具有公開金鑰的產生憑證是由 CA 簽署。 這些作業可能需要幾秒鐘的時間才能完成。

   ![視圖金鑰]組(media/howto-opc-vault-secure/view-key-pair.png "視圖金鑰")組

7. 您可以從這裡下載所產生的私密金鑰 (PFX 或 PEM) 和憑證 (DER), 格式為 [二進位檔下載]。 您也可以使用 base64 編碼版本, 例如, 將憑證貼到命令列或文字輸入。 
8. 私密金鑰下載並安全地儲存之後, 即可使用`Delete Private Key`按鈕從服務中刪除。 具有公開金鑰的憑證仍然可供日後使用。
9. 由於使用 CA 簽署的憑證, 因此也應該在這裡下載 CA cert 和 CRL。
10. 現在它取決於 OPC UA 裝置如何套用新的金鑰組。 CA 憑證和 CRL 通常會複製到`trusted`資料夾, 而應用程式憑證的公開和私密金鑰則會套用`own`到憑證存放區中的資料夾。 某些裝置可能已支援「伺服器推送」以進行憑證更新。 請參閱 OPC UA 裝置的檔。

### <a name="step-3b-request-a-new-certificate-with-a-csr"></a>步驟 3b:要求具有 CSR 的新憑證 

1. 瀏覽到 `Applications` 頁面。
3. 選擇`New Request`列出的應用程式來啟動憑證要求工作流程。

   ![要求新憑證](media/howto-opc-vault-secure/request-new-certificate.png "要求新憑證")

3. 按 [要求具有簽署要求的新憑證], 為您的應用程式要求新的已簽署憑證。

   ![產生新的憑證](media/howto-opc-vault-secure/generate-new-certificate.png "產生新的憑證")

4. 選取本機檔案或在表單中貼上 base64 編碼的 CSR, 以上傳 CSR。 按下`Generate New Certificate`按鈕以建立憑證要求。

   ![核准 CSR](media/howto-opc-vault-secure/approve-reject-csr.png "核准 CSR")

5. 核准步驟需要使用者具有「核准者」角色, 並具有 Azure Key Vault 的簽署許可權。 核准或拒絕憑證要求, 以啟動或取消實際的簽署作業。 具有公開金鑰的產生憑證是由 CA 簽署。 此作業可能需要幾秒鐘的時間才能完成。

   ![查看憑證](media/howto-opc-vault-secure/view-cert-csr.png "查看憑證")

6. 產生的憑證 (DER) 可以從這裡下載為二進位檔案。 您也可以使用 base64 編碼版本, 例如, 將憑證貼到命令列或文字輸入。 
10. 憑證下載並安全地儲存之後, 即可使用`Delete Certificate`按鈕從服務中刪除。
11. 由於使用 CA 簽署的憑證, 因此也應該在這裡下載 CA cert 和 CRL。
12. 現在它取決於 OPC UA 裝置如何套用新憑證。 通常會將 CA 憑證和 CRL 複製到`trusted`資料夾, 而應用程式憑證會套用`own`至憑證存放區中的資料夾。 某些裝置可能已支援「伺服器推送」以進行憑證更新。 請參閱 OPC UA 裝置的檔。

### <a name="step-4-device-secured"></a>步驟 4：受保護的裝置

OPC UA 裝置現在已準備好與 CA 簽署憑證所保護的其他 OPC UA 裝置進行通訊, 而不需要進一步設定。 盡情享受！


## <a name="next-steps"></a>後續步驟

既然您已瞭解如何保護 OPC UA 裝置, 以下是建議的後續步驟:

> [!div class="nextstepaction"]
> [執行安全憑證管理服務](howto-opc-vault-secure-ca.md)