---
title: 如何管理 OPC 保存庫憑證管理服務-Azure |Microsoft Docs
description: 管理 OPC 保存庫根 CA 憑證和使用者權限。
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 6e53914b16bc126cdab7300d918dbb7b6b868728
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/22/2019
ms.locfileid: "69973801"
---
# <a name="how-to-manage-the-opc-ua-certificate-service"></a>如何管理 OPC UA 憑證服務

本文說明 Azure 中 OPC UA 憑證管理服務的管理工作、如何更新簽發者 CA 憑證、如何更新憑證撤銷清單 (CRL), 以及如何授與及撤銷使用者存取權。

## <a name="create-or-renew-the-root-ca-certificate"></a>建立或更新根 CA 憑證

若要建立根 CA 憑證, 在部署之後是強制執行的步驟。 如果沒有有效的簽發者 CA 憑證, 就無法簽署和發行應用程式憑證。<br>請參閱關於憑證存留[期](howto-opc-vault-secure-ca.md#certificates)的章節, 以合理且安全的存留期來管理您的憑證。 簽發者 CA 憑證應在其存留期的一半後更新, 但不晚于新簽署應用程式憑證的設定存留期會超過簽發者憑證的存留期。<br>
> [!IMPORTANT]
> 需要「系統管理員」角色, 才能建立或更新簽發者 CA 憑證。

1. 在中開啟您的`https://myResourceGroup-app.azurewebsites.net`憑證服務, 然後登入。
2. 瀏覽到 `Certificate Groups` 頁面。
3. 其中列出一個`Default`憑證群組。 按一下 `Edit`。
4. 在`Edit Certificate Group Details`中, 您可以修改 CA 和應用程式憑證的主體名稱和存留期。<br>只有在發行第一個 CA 憑證之前, 應該只設定一次主體和存留期。 作業期間的存留期變更可能會導致所發行憑證和 Crl 的存留時間不一致。
5. 輸入有效的主旨, `CN=My CA Root, O=MyCompany, OU=MyDepartment`例如。<br>
   > [!IMPORTANT]
   > 變更主體需要更新簽發者憑證, 否則服務將無法簽署應用程式憑證。 設定的主旨是針對作用中簽發者憑證的主旨檢查的健全狀態。 如果主體不相符, 憑證簽署會遭到拒絕。
6. 按一下 [ `Save` ] 按鈕。
7. 如果您在此時遇到「禁止」錯誤, 表示您的使用者認證沒有系統管理員許可權, 無法修改或建立新的根憑證。 根據預設, 部署服務的使用者具有服務的系統管理員和簽署角色, 而其他使用者則需要在 AzureAD 應用程式註冊時, 新增至 [核准者]、[寫入器] 或 [系統管理員] 角色。
8. 按一下 [ `Details` ] 按鈕。 `View Certificate Group Details`應該會顯示更新的資訊。
9. `Renew CA Certificate`按一下按鈕以發出第一個簽發者 CA 憑證, 或更新簽發者憑證。 按`Ok`下以繼續。
10. 幾秒鐘之後, `Certificate Details`就會顯示。 按`Issuer` 或`Crl`下載最新的 CA 憑證和 CRL, 以散發至您的 OPC UA 應用程式。
11. 現在, OPC UA 憑證管理服務已準備好發行 OPC UA 應用程式的憑證。

## <a name="renew-the-crl"></a>更新 CRL

續訂憑證撤銷清單 (CRL) 是一項更新, 應定期散發給應用程式。 支援 CRL 發佈點 X509 延伸模組的 OPC UA 裝置, 可以直接從微服務端點更新 CRL。 其他 OPC UA 裝置可能需要手動更新, 或在最佳情況下, 可以使用 GDS 伺服器推送延伸模組 (*) 來更新信任清單, 以使用憑證和 Crl。

在下列工作流程中, 已刪除狀態中的所有憑證要求都會在 Crl 中撤銷, 其對應至簽發的簽發者 CA 憑證。 CRL 的版本號碼會遞增1。 <br>
> [!NOTE]
> 所有發行的 Crl 在簽發者 CA 憑證到期前都是有效的, 因為 OPC UA 規格不需要適用于 CRL 的強制、確定性散發模型。

> [!IMPORTANT]
> 需要「系統管理員」角色, 才能更新簽發者 CRL。

1. 在中開啟您的`https://myResourceGroup.azurewebsites.net`憑證服務, 然後登入。
2. 瀏覽到 `Certificate Groups` 頁面。
3. 按一下 [ `Details` ] 按鈕。 `View Certificate Group Details`應該會顯示目前的憑證和 CRL 資訊。
4. 按一下此`Update CRL Revocation List(CRL)`按鈕, 針對 OPC 保存庫儲存體中所有作用中的簽發者憑證發出更新的 CRL。
5. 幾秒鐘之後, `Certificate Details`就會顯示。 按`Issuer` 或`Crl`下載最新的 CA 憑證和 CRL, 以散發至您的 OPC UA 應用程式。

## <a name="manage-user-roles"></a>管理使用者角色

OPC 保存庫微服務的使用者角色是在 Azure Active Directory 企業應用程式中進行管理。

如需角色定義的詳細描述, 請參閱[角色](howto-opc-vault-secure-ca.md#roles)一節。

根據預設, 租使用者中已驗證的使用者可以將服務以「讀取者」的身分登入。 較高的特殊許可權角色需要在 Azure 入口網站或使用 Powershell 進行手動管理。

### <a name="add-user"></a>新增使用者

1. 開啟位於`portal.azure.com`的 Azure 入口網站。
2. 瀏覽至 `Azure Active Directory`/`Enterprise applications`。
3. 根據預設`resourceGroupName-service`, 選擇 [OPC 保存庫] 微服務的註冊。
4. 瀏覽至 `Users and Groups` 。
5. 按一下 `Add User`。
6. 選取或邀請使用者指派給特定角色。
7. 選取使用者的角色。
8. 按下`Assign`按鈕。
9. 針對或`Administrator` `Approver`角色中的使用者, 繼續新增 Azure Key Vault 存取原則。

### <a name="remove-user"></a>移除使用者

1. 開啟位於`portal.azure.com`的 Azure 入口網站。
2. 瀏覽至 `Azure Active Directory`/`Enterprise applications`。
3. 根據預設`resourceGroupName-service`, 選擇 [OPC 保存庫] 微服務的註冊。
4. 瀏覽至 `Users and Groups` 。
5. 選取要移除角色的使用者。
6. 按下`Remove`按鈕。
7. 移除已移除的系統管理員和核准者, 同時從 Azure Key Vault 的原則。

### <a name="add-user-access-policy-to-azure-key-vault"></a>將使用者存取原則新增至 Azure Key Vault

**核准者**和系統**管理員**需要其他存取原則。

根據預設, 服務識別僅具有存取 Key Vault 的有限許可權, 以避免在沒有使用者模擬的情況下進行較高的作業或變更。 基本服務許可權`Get`為和`List` , 適用于秘密和憑證。 對於秘密, 只有一個例外, 服務可以`Delete`在使用者接受後, 從秘密存放區取得私密金鑰。 所有其他作業都需要使用者模擬許可權。<br>

#### <a name="for-an-approver-role-the-following-permissions-must-be-added-to-key-vault"></a>對於**核准者角色**, 必須將下列許可權新增至 Key Vault:

1. 開啟位於`portal.azure.com`的 Azure 入口網站。
2. 流覽至您在部署`resourceGroupName`期間使用的 OPC 保存庫。
3. 流覽至 [Key Vault `resourceGroupName-xxxxx`]。
4. 流覽至`Access Policies`。
5. 按一下 `Add new`。
6. 略過範本, 沒有符合需求的範本。
7. 按一下 [ `Select Principal`開啟], 然後選取要新增或邀請新使用者加入租使用者的使用者。
8. 檢查`Key permissions`: `Get`、和`List`最重要`Sign`的。
9. 檢查`Secret permissions`: `Get`、 `List`和。`Delete` `Set`
10. 檢查`Certificate permissions`: `Get`和。`List`
11. 按一下 `Ok`。
12. `Save`變更.

#### <a name="for-an-administrator-role-the-following-permissions-must-be-added-to-key-vault"></a>對於**系統管理員角色**, 必須將下列許可權新增至 Key Vault:

1. 開啟位於`portal.azure.com`的 Azure 入口網站。
2. 流覽至您在部署`resourceGroupName`期間使用的 OPC 保存庫。
3. 流覽至 [Key Vault `resourceGroupName-xxxxx`]。
4. 流覽至`Access Policies`。
5. 按一下 `Add new`。
6. 略過範本, 沒有符合需求的範本。
7. 按一下 [ `Select Principal`開啟], 然後選取要新增或邀請新使用者加入租使用者的使用者。
8. 檢查`Key permissions`: `Get`、和`List`最重要`Sign`的。
9. 檢查`Secret permissions`: `Get`、 `List`和。`Delete` `Set`
10. 檢查`Certificate permissions`: `Get`、 `List`、 `Update`和。 `Create` `Import`
11. 按一下 `Ok`。
12. `Save`變更.

### <a name="remove-user-access-policy-from-azure-key-vault"></a>從 Azure Key Vault 移除使用者存取原則

1. 開啟位於`portal.azure.com`的 Azure 入口網站。
2. 流覽至您在部署`resourceGroupName`期間使用的 OPC 保存庫。
3. 流覽至 [Key Vault `resourceGroupName-xxxxx`]。
4. 流覽至`Access Policies`。
5. 尋找要移除的使用者, 然後按一下`... / Delete`以刪除使用者存取權。

## <a name="next-steps"></a>後續步驟

既然您已瞭解如何管理 OPC 保存庫憑證和使用者, 以下是建議的後續步驟:

> [!div class="nextstepaction"]
> [安全的 OPC 裝置通訊](howto-opc-vault-secure.md)
