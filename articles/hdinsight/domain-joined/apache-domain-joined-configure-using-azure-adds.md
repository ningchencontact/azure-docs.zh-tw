---
title: 使用 Azure Active Directory Domain Services 來設定企業安全性套件 - Azure HDInsight
description: 了解如何使用 Azure Active Directory Domain Services 來設定 HDInsight 企業安全性套件叢集。
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.custom: seodec18
ms.date: 04/23/2019
ms.openlocfilehash: 8699533cd64e6b1778c5e78b8c51eb1efe518c75
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67126222"
---
# <a name="configure-a-hdinsight-cluster-with-enterprise-security-package-by-using-azure-active-directory-domain-services"></a>使用 Azure Active Directory Domain Services 設定具有企業安全性套件的 HDInsight 叢集

企業安全性套件 (ESP) 叢集可在 Azure HDInsight 叢集上提供多使用者存取。 具有 ESP 的 HDInsight 叢集會與網域連線，讓網域使用者可以使用其網域認證來驗證叢集並執行巨量資料作業。

在本文中，您將了解如何使用 Azure Active Directory Domain Services (Azure AD-DS) 來設定具有 ESP 的 HDInsight 叢集。

> [!NOTE]  
> ESP 是正式推出在 HDInsight 3.6 和 4.0 的叢集類型：Apache Spark、 互動式、 Apache Hadoop 和 HBase。 Apache Kafka 叢集類型的 ESP 為預覽狀態。

## <a name="enable-azure-ad-ds"></a>啟用 Azure AD-DS

> [!NOTE]  
> 只有租用戶的系統管理員具有啟用 Azure AD-DS 的權限。 如果叢集儲存體是 Azure Data Lake 儲存體 (ADLS) Gen1 或 Gen2，您必須停用多重要素驗證 (MFA) 只需要存取叢集使用基本的 Kerberos 驗證的使用者。 您可以使用[信任的 Ip](../../active-directory/authentication/howto-mfa-mfasettings.md#trusted-ips)或[條件式存取](../../active-directory/conditional-access/overview.md)MFA 停用特定的使用者只有當他們存取的 HDInsight 叢集 VNET IP 範圍。 如果您使用條件式存取請請確定 AD 中的服務端點啟用 HDInsight VNET 上。
>
> 如果叢集儲存體是 Azure Blob 儲存體 (WASB)，請勿停用 MFA。

啟用 Azure AD-DS 是必要條件，如此您才能建立具有 ESP 的 HDInsight 叢集。 如需詳細資訊，請參閱[使用 Azure 入口網站啟用 Azure Active Directory Domain Services](../../active-directory-domain-services/create-instance.md)。 

Azure AD-DS 啟用時，所有使用者和物件依預設都會開始從 Azure Active Directory (AAD) 同步處理至 Azure AD-DS。 同步作業的所需時間取決於 Azure AD 中的物件數目。 數十萬個物件的同步可能需要幾天的時間。 

您可以選擇同步處理只需要存取 HDInsight 叢集的群組。 這個僅同步特定群組的選項，稱為*特定範圍同步處理*。 請參閱[設定從 Azure AD 到受控網域的特定範圍同步處理](../../active-directory-domain-services/scoped-synchronization.md)，以取得指示。

在啟用安全 LDAP 時，請在憑證的主體名稱與主體替代名稱中放入網域名稱。 例如，如果您的網域名稱是 *contoso100.onmicrosoft.com*，請確定相同的名稱存在於您的憑證主體名稱與主體替代名稱中。 如需詳細資訊，請參閱[針對 Azure AD-DS 受控網域設定安全的 LDAP](../../active-directory-domain-services/configure-ldaps.md)。 以下是建立自我簽署憑證並讓主體名稱與 DnsName (主體替代名稱) 中都有網域名稱 (*contoso100.onmicrosoft.com*) 的範例：

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject contoso100.onmicrosoft.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.onmicrosoft.com, contoso100.onmicrosoft.com
```

## <a name="check-azure-ad-ds-health-status"></a>檢查 Azure AD-DS 健康狀態
選取 [管理]  類別下方的 [健康情況]  ，以檢視您的 Azure Active Directory Domain Services 的健康狀態。 請確定 Azure AD-DS 的狀態是綠色的 (執行中)，且同步處理已完成。

![Azure Active Directory Domain Services 健康情況](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

## <a name="create-and-authorize-a-managed-identity"></a>建立和授權受控識別

**使用者指派的受控識別**可用來簡化和保護網域服務作業。 當您將 HDInsight 網域服務參與者角色指派給受控識別時，該角色將可讀取、建立、修改和刪除網域服務作業。 特定的網域服務作業，例如建立 Ou 和服務主體所需的 HDInsight 企業安全性套件。 受控識別可建立在任何訂用帳戶中。 如需詳細資訊在一般情況下管理身分識別，請參閱[管理 Azure 資源的身分識別](../../active-directory/managed-identities-azure-resources/overview.md)。 如需有關如何管理 Azure HDInsight 中的身分識別工作的詳細資訊，請參閱 <<c0> [ 管理 Azure HDInsight 中的身分識別](../hdinsight-managed-identities.md)。

若要設定 ESP 叢集，請建立使用者指派的受控識別 (如果您還沒有的話)。 請參閱[使用 Azure 入口網站對使用者指派的受控識別建立、列出、刪除或指派角色](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)，以取得指示。 接著，請在 Azure AD-DS 存取控制中，將 **HDInsight 網域服務參與者**角色指派給受控識別 (必須具有 AAD-DS 管理員權限才能執行此角色指派)。

![Azure Active Directory Domain Services 存取控制](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

指派 **HDInsight 網域服務參與者**角色，可確保此身分識別具有適當的 (代表) 存取權可對 AAD-DS 網域執行特定的網域服務作業，例如建立 OU、刪除 OU 等。

在建立受控識別並為其指定正確的角色後，AAD-DS 管理員可設定能夠使用此受控識別的人員。 若要設定受控識別的使用者，管理員應在入口網站中選取受控識別，然後按一下 [概觀]  下方的 [存取控制 (IAM)]  。 然後，在右側將**受控識別操作員**角色指派給要建立 ESP HDInsight 叢集的使用者或群組。 比方說，AAD DS 系統管理員可以指派此角色，才能**MarketingTeam**群組**sjmsi**受控身分識別，如下圖所示。 這可確保組織中適當人員才有存取權，可使用此受控識別來建立 ESP 叢集。

![HDInsight 受控識別操作員角色指派](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-managed-identity-operator-role-assignment.png)

## <a name="networking-considerations"></a>網路考量

> [!NOTE]  
> Azure AD-DS 必須部署在 Azure Resource Manager (ARM) 架構的 vNET 中。 Azure AD-DS 不支援傳統虛擬網路。 如需詳細資訊，請參閱[使用 Azure 入口網站啟用 Azure Active Directory Domain Services](../../active-directory-domain-services/active-directory-ds-getting-started-network.md)。

啟用 Azure AD-DS 之後，本機網域名稱服務 (DNS) 伺服器會在 AD 虛擬機器 (VM) 上執行。 將 Azure AD-DS 虛擬網路 (VNET) 設定為使用這些自訂的 DNS 伺服器。 若要找出正確的 IP 位址，請選取 [管理]  類別下的 [屬性]  ，然後查看 [虛擬網路上的 IP 位址]  下方列出的 IP 位址。

![找出本機 DNS 伺服器的 IP 位址](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-dns.png)

選取 [設定]  類別下的 [DNS 伺服器]  ，將 Azure AD-DS VNET 中的 DNS 伺服器設定變更為使用這些自訂的 IP。 接著，按一下 [自訂]  旁的選項按鈕，在文字方塊中輸入第一個 IP 位址，然後按一下 [儲存]  。 使用相同的步驟新增其他 IP 位址。

![更新 VNET DNS 設定](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-vnet-configuration.png)

將 Azure AD-DS 執行個體和 HDInsight 叢集放在相同的 Azure 虛擬網路中比較容易。 若您計畫使用不同的 VNET，您必須將那些虛擬網路設定為同儕節點，以便 HDI VM 可以看見網域控制站。 如需詳細資訊，請參閱[虛擬網路對等互連](../../virtual-network/virtual-network-peering-overview.md)。 

VNET 對等互連後，請將 HDInsight VNET 設定為使用自訂的 DNS 伺服器，並輸入 Azure AD-DS 的私人 IP 作為 DNS 伺服器位址。 當兩個 VNET 使用相同的 DNS 伺服器時，您的自訂網域名稱將解析為正確的 IP，並且可從 HDInsight 存取。 例如，如果您的網域名稱是 "contoso.com"，則在完成此步驟後偵測 "contoso.com" 時，應會解析正確的為 Azure AD-DS IP。 

![為對等互連的 VNET 設定自訂 DNS 伺服器](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-peered-vnet-configuration.png)

如果您在您的 HDInsight 子網路中使用網路安全性群組 (NSG) 規則，您應該允許輸入和輸出流量[所需的 IP](../hdinsight-extend-hadoop-virtual-network.md)。 

**若要測試**您的網路是否已正確設定，請將 Windows VM 加入至 HDInsight VNET/子網路，並 Ping 網域名稱 (它應更會解析為 IP)，接著執行 **ldp.exe** 以存取 Azure AD-DS 網域。 接著，**將此 Windows VM 加入至網域**，以確認用戶端與伺服器之間的所有必要 RPC 呼叫都會成功。 您也可以使用 **nslookup** 來確認對您儲存體帳戶或您可能使用之任何外部 DB (例如，外部 Hive 中繼存放區或 Ranger DB) 的網路存取。
您應該確認所有[必要連接埠](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772723(v=ws.10)#communication-to-domain-controllers) \(英文\) 都已在 AAD-DS 子網路網路安全性群組規則中列為允許清單 (若 AAD-DS 已由 NSG 保護)。 如果此 Windows VM 網域成功加入網域，您就可以繼續進行下一個步驟並建立 ESP 叢集。

## <a name="create-a-hdinsight-cluster-with-esp"></a>建立具有 ESP 的 HDInsight 叢集

正確設定上述步驟之後，下一個步驟是在已啟用 ESP 的情況下建立 HDInsight 叢集。 當您建立 HDInsight 叢集時，您可以在 [自訂]  索引標籤中啟用「企業安全性套件」。若偏好使用 Azure Resource Manager 範本來進行部署，請使用入口網站體驗一次，並在最後一個 [摘要] 頁面下載預先填寫的範本，以供未來重複使用。

> [!NOTE]  
> ESP 叢集名稱的前六個字元在您的環境中必須是唯一的。 例如，如果您在不同的 VNET 中有多個 ESP 叢集，您應該選擇能確保叢集名稱前六個字元是唯一的命名慣例。

![Azure HDInsight 企業安全性套件網域驗證](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate.png)

在您啟用 ESP 後，系統會自動偵測及驗證與 Azure AD-DS 有關的常見錯誤設定。 修正這些錯誤之後，您可以繼續進行下一個步驟： 

![Azure HDInsight 企業安全性套件失敗的網域驗證](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate-failed.png)

建立具有 ESP 的 HDInsight 叢集時，必須提供下列參數：

- **叢集管理員使用者**：從已同步的 Azure AD-DS 中選擇您叢集的管理員。 此網域帳戶必須已同步，而且可在 Azure AD-DS 中使用。

- **叢集存取群組**：Azure AD-DS 中應該要有您要同步使用者並讓使用者能夠存取叢集的安全性群組。 例如，HiveUsers 群組。 如需詳細資訊，請參閱[在 Azure Active Directory 中建立群組和新增使用者](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。

- **LDAPS URL**：例如 ldaps://contoso.com:636。

下列螢幕擷取畫面顯示 Azure 入口網站中的成功設定：

![Azure HDInsight ESP Active Directory Domain Services 的設定](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png)上也提供本文中使用的原始碼。

您所建立的受控識別，可在建立新的叢集時從 [使用者指派的受控識別] 下拉式清單中選擇。

![Azure HDInsight ESP Active Directory Domain Services 的設定](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-identity-managed-identity.png)上也提供本文中使用的原始碼。

## <a name="next-steps"></a>後續步驟

* 如需了解如何設定 Hive 原則及執行 Hive 查詢，請參閱[為具有 ESP 的 HDInsight 叢集設定 Apache Hive 原則](apache-domain-joined-run-hive.md)。
* 如需了解如何使用 SSH 來連線到具有 ESP 的 HDInsight 叢集，請參閱[從 Linux、Unix 或 OS X 使用 SSH 搭配 Linux 型 HDInsight 上的 Apache Hadoop](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)。
