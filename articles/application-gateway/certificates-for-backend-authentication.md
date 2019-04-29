---
title: 将 Azure 应用程序网关中的后端加入白名单所需的证书
description: 本文通过示例演示如何将 SSL 证书转换为身份验证证书和受信任的根证书，将 Azure 应用程序网关中的后端实例加入白名单时需要这些证书
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 3/14/2019
ms.author: absha
ms.openlocfilehash: 72ee9123ad959c0c7240d4f7a906adc1a4dd1a93
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60831515"
---
# <a name="create-certificates-for-whitelisting-backend-with-azure-application-gateway"></a>创建证书用于将 Azure 应用程序网关中的后端加入白名单

若要执行端到端 SSL，应用程序网关要求通过上传身份验证证书/受信任的根证书，将后端实例加入白名单。 V1 SKU 時驗證憑證所需而 v2 SKU，如果受信任的根憑證所需的允許清單的憑證

在本文中，您將了解：

> [!div class="checklist"]
>
> - 從後端憑證的驗證憑證匯出 （適用於 v1 SKU)
> - 從後端憑證匯出信任的根憑證 （適用於 v2 SKU)

## <a name="prerequisites"></a>必要條件

需要使用现有的后端证书来生成身份验证证书或受信任的根证书，用于将应用程序网关中的后端实例加入白名单。 后端证书可与 SSL 证书相同，为了提高安全性，两者也可以不同。 应用程序网关不会提供任何机制用于创建或购买 SSL 证书。 对于测试，可以创建自签名证书，但不应将其用于生产工作负荷。 

## <a name="export-authentication-certificate-for-v1-sku"></a>驗證憑證匯出 （適用於 v1 SKU)

必须使用身份验证证书将应用程序网关 v1 SKU 中的后端实例加入白名单。 身份验证证书是后端服务器证书的公钥，采用 Base-64 编码的 X.509(.CER) 格式。 此示例使用 SSL 证书作为后端证书，并导出其公钥用于身份验证。 另外，此示例使用 Windows 证书管理器工具导出所需的证书。 你可以选择使用其他任何便利的工具。

从 SSL 证书中导出公钥 .cer 文件（不是私钥）。 以下步骤可帮助你导出证书的 .cer 文件，其格式为 Base-64 编码的 X.509(.CER)：

1. 若要取得憑證的 .cer 檔案，請開啟 [管理使用者憑證]。 找到该证书（通常位于“证书 - 当前用户”>“个人”>“证书”中），并单击右键。 按一下 [所有工作]，然後按一下 [匯出]。 這會開啟 [憑證匯出精靈] 。 若您在 Current User\Personal\Certificates 下找不到憑證，您可能已意外開啟 [憑證 - 本機電腦]，而非 [憑證 - 目前使用者]。 若要使用 PowerShell 在目前使用者範圍開啟 [憑證管理員]，您必須在主控台視窗中輸入 *certmgr*。

   ![匯出](./media/certificates-for-backend-authentication/export.png)

2. 在精靈中，按 [下一步]。

   ![匯出憑證](./media/certificates-for-backend-authentication/exportwizard.png)

3. 選取 [否，不要匯出私密金鑰]，然後按 [下一步]。

   ![不要匯出私密金鑰](./media/certificates-for-backend-authentication/notprivatekey.png)

4. 在 [匯出檔案格式] 頁面上，選取 [Base-64 編碼 X.509 (.CER)]，然後按 [下一步]。

   ![Base-64 編碼](./media/certificates-for-backend-authentication/base64.png)

5. 針對 [要匯出的檔案]，[瀏覽] 至您要匯出憑證的位置。 針對 [檔案名稱] ，請為憑證檔案命名。 然後按 [下一步] 。

   ![瀏覽](./media/certificates-for-backend-authentication/browse.png)

6. 按一下 [完成]  以匯出憑證。

   ![完成](./media/certificates-for-backend-authentication/finish.png)

7. 已成功匯出您的憑證。

   ![成功](./media/certificates-for-backend-authentication/success.png)

   匯出的憑證如下所示：

   ![已匯出](./media/certificates-for-backend-authentication/exported.png)

8. 如果您使用「記事本」開啟匯出的憑證，您會看到類似於此範例的內容。 蓝色部分包含已上传到应用程序网关的信息。 如果您使用「記事本」開啟您的憑證，但並未顯示這樣的內容，這通常表示您未使用 Base-64 編碼 X.509 (.CER) 格式加以匯出。 此外，如果您想要使用不同的文字編輯器，請了解某些編輯器可能會在背景中導入非預期的格式。 這可能會在此憑證中的文字上傳至 Azure 時產生問題。

   ![使用記事本開啟](./media/certificates-for-backend-authentication/format.png)

## <a name="export-trusted-root-certificate-for-v2-sku"></a>匯出信任的根憑證 （適用於 v2 SKU)

受信任的根憑證，才能在 v2 中的應用程式閘道的白名單後端執行個體 SKU。 根憑證的 Base-64 編碼 X.509 (。從後端伺服器憑證的 CER) 格式的根憑證。 在此範例中，我們將使用後端憑證的 SSL 憑證、 匯出其公開金鑰，然後取得受信任的根憑證的 base64 編碼格式的公開金鑰匯出受信任的 CA 的根憑證。 

下列步驟可協助您匯出憑證的.cer 檔案：

1. 使用步驟 1-9 一節所述**匯出驗證憑證，從後端憑證 （適用於 v1 SKU)** 上述匯出從您的後端憑證的公開金鑰。

2. 匯出公開金鑰後, 開啟的檔案。

   ![開啟驗證憑證](./media/certificates-for-backend-authentication/openAuthcert.png)

   ![關於憑證](./media/certificates-for-backend-authentication/general.png)

3. 移至 憑證路徑檢視，以檢視憑證授權單位。

   ![憑證詳細資料](./media/certificates-for-backend-authentication/certdetails.png)

4. 選取根憑證，然後按一下**檢視憑證**。

   ![憑證路徑](./media/certificates-for-backend-authentication/rootcert.png)

   您應該能夠檢視根憑證的詳細資訊。

   ![憑證資訊](./media/certificates-for-backend-authentication/rootcertdetails.png)

5. 移至**詳細資料**檢視，然後按一下**複製到檔案...**

   ![複本的根憑證](./media/certificates-for-backend-authentication/rootcertcopytofile.png)

6. 此時，您已經從後端的憑證來擷取根憑證的詳細資料。 您會看到**憑證匯出精靈**。 現在使用步驟 2-9 一節所述**匯出驗證憑證，從後端憑證 （適用於 v1 SKU)** 上方匯出受信任的根憑證的 Base-64 編碼 X.509 (。CER) 格式。

## <a name="next-steps"></a>後續步驟

现已创建采用 Base-64 编码的 X.509(.CER) 格式身份验证证书/受信任的根证书。 可将此证书添加到应用程序网关，以将后端服务器加入白名单进行端到端的 SSL 加密。 参阅[如何配置端到端 SSL 加密](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)。