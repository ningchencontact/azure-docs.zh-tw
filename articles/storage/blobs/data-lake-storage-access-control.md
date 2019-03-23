---
title: Azure Data Lake Storage Gen2 中的存取控制概觀 | Microsoft Docs
description: 了解 Azure Data Lake Storage Gen2 中的存取控制運作方式
services: storage
author: jamesbak
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: jamesbak
ms.openlocfilehash: a102216a6a2a7dec471678e14f7050cb4ef41d77
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370103"
---
# <a name="access-control-in-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 中的存取控制

Azure Data Lake Storage Gen2 可實作支援 Azure 角色型存取控制 (RBAC) 和 POSIX 型存取控制清單 (ACL) 的存取控制模型。 本文摘要說明 Data Lake Storage Gen2 存取控制模型的基本概念。 

## <a name="azure-role-based-access-control-rbac"></a>Azure 角色型存取控制 (RBAC)

Azure 角色型存取控制 (RBAC) 會使用角色指派將權限集有效地套用至 Azure 資源的使用者、群組和服務主體。 一般而言，這些 Azure 資源會限定於最上層資源 (*例如*，Azure 儲存體帳戶)。 就 Azure 儲存體和隨之採用的 Azure Data Lake Storage Gen2 而言，這項機制已擴充至檔案系統資源。

雖然使用 RBAC 角色指派是可控制使用者權限的強大機制，但此機制的精細程度仍遠不及 ACL。 RBAC 的最小細微性是檔案系統層級，而此層級在評估時的優先順序將會高於 ACL。 因此，如果您在檔案系統上指派 RBAC 權限，使用者或服務主體將會獲得該檔案系統中所有目錄和檔案的授權，無論 ACL 指派為何。

Azure 儲存體針對 Blob 儲存體提供三個內建的 RBAC 角色： 

- [儲存體 Blob 資料擁有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner-preview)
- [儲存體 Blob 資料參與者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor-preview)
- [儲存體 Blob 資料讀者](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader-preview)

如果使用者或服務主體透過其中一個內建的角色或自訂角色獲得 RBAC 資料權限，則在對要求進行授權時會先評估這些權限。 如果要求的作業已獲呼叫端的 RBAC 指派授權，則會立即解析授權，而不會執行其他 ACL 檢查。 或者，如果呼叫端沒有 RBAC 指派，或要求的作業不符合指派的權限，則會執行 ACL 來判斷呼叫端是否有權執行要求的作業。

對於儲存體 Blob 資料擁有者內建角色，有一項要點應特別注意。 如果呼叫端具有此 RBAC 指派，則使用者會被視為*超級使用者*，並且獲得所有變動作業的完整存取權，包括設定目錄或檔案的擁有者，以及為他們並非擁有者的目錄和檔案設定 ACL。 超級使用者存取權是唯一可變更資源擁有者的授權方式。

## <a name="shared-key-and-shared-access-signature-authentication"></a>共用金鑰和共用存取簽章驗證

Azure Data Lake Storage Gen2 支援以共用金鑰和共用存取簽章方法進行驗證。 這些驗證方法的特性之一，是沒有與呼叫端相關聯的身分識別，因此無法執行以使用者權限為基礎的授權。

使用共用金鑰時，呼叫者可有效取得「超級使用者」存取權，亦即對所有資源的所有作業具有完整存取權，包括設定擁有者和變更 ACL。

SAS 權杖會在其權杖中包含允許的權限。 SAS 權杖中包含的權限會有效地套用至所有授權決策，但不會再執行其他 ACL 檢查。

## <a name="access-control-lists-on-files-and-directories"></a>檔案和目錄的存取控制清單

存取控制清單 (ACL) 有兩種類型：存取 ACL 和預設 ACL。

* **存取 ACL**：存取 ACL 可控制對物件的存取。 檔案和目錄均有存取 ACL。

* **預設 ACL**：與目錄相關聯 ACL 的範本，用以判斷在該目錄下所建立任何子項目的存取 ACL。 檔案沒有預設 ACL。

存取 ACL 和預設 ACL 有相同的結構。

> [!NOTE]
> 變更父代的預設 ACL 並不會影響現存子項目的存取 ACL 或預設 ACL。

## <a name="permissions"></a>權限

檔案系統物件的權限為 [讀取]、[寫入] 和 [執行]，這些權限可以用於下表所示的檔案和目錄：

|            |    檔案     |   目錄 |
|------------|-------------|----------|
| **讀取 (R)** | 可以讀取檔案的內容 | 需要 [讀取] 和 [執行] 才能列出目錄內容 |
| **寫入 (W)** | 可寫入或附加至檔案 | 需要 [寫入] 和 [執行] 才能在目錄中建立子項目 |
| **執行 (X)** | 不表示 Data Lake Storage Gen2 內容中的任何項目 | 需要周遊目錄的子項目 |

### <a name="short-forms-for-permissions"></a>權限的簡短形式

**RWX** 用來表示 [讀取 + 寫入 + 執行]。 有更壓縮的數字形式存在，其中 [讀取 = 4]、[寫入 = 2] 和 [執行 = 1]，其總和代表各種權限。 以下有一些範例。

| 數值形式 | 簡短形式 |      意義     |
|--------------|------------|------------------------|
| 7            | `RWX`        | 讀取 + 寫入 + 執行 |
| 5            | `R-X`        | 讀取 + 執行         |
| 4            | `R--`        | 讀取                   |
| 0            | `---`        | 沒有權限         |

### <a name="permissions-inheritance"></a>權限繼承

在 Data Lake Storage Gen2 所使用的 POSIX 樣式模型中，項目的權限會儲存在項目本身。 換句話說，如果是在子項目建立後才設定權限，則無法從父項目繼承項目的權限。 必須是在子項目建立之前即已設定父項目的預設權限，才會繼承權限。

## <a name="common-scenarios-related-to-permissions"></a>權限相關的常見案例

下表列出一些常見的案例，可協助您了解在 Data Lake Storage Gen2 帳戶上執行某些作業所需的權限。

|    作業             |    /    | Oregon/ | Portland/ | Data.txt     |
|--------------------------|---------|----------|-----------|--------------|
| Read Data.txt            |   `--X`   |   `--X`    |  `--X`      | `R--`          |
| Append to Data.txt       |   `--X`   |   `--X`    |  `--X`      | `RW-`          |
| Delete Data.txt          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Create Data.txt          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| List /                   |   `R-X`   |   `---`    |  `---`      | `---`          |
| List /Oregon/           |   `--X`   |   `R-X`    |  `---`      | `---`          |
| List /Oregon/Portland/  |   `--X`   |   `--X`    |  `R-X`      | `---`          |


> [!NOTE]
> 只要前面的兩個條件成立，刪除檔案時就不需要其寫入權限。
>
>

## <a name="users-and-identities"></a>使用者和身分識別

每個檔案和目錄都有這些身分識別的不同權限︰

- 擁有使用者
- 擁有群組
- 具名使用者
- 具名群組
- 具名服務主體
- 所有其他使用者

使用者和群組的身分識別皆為 Azure Active Directory (Azure AD) 身分識別。 因此，除非另有註明，否則 Data Lake Storage Gen2 中的*使用者*可能表示 Azure AD 使用者、服務主體或安全性群組。

### <a name="the-owning-user"></a>擁有使用者

建立項目的使用者會自動成為項目的擁有使用者。 擁有使用者可以︰

* 變更所擁有檔案的權限。
* 只要擁有使用者也是目標群組的成員，請變更所擁有檔案的擁有群組。

> [!NOTE]
> 擁有使用者「無法」變更檔案或目錄的擁有使用者。 只有超級使用者可以變更檔案或目錄的擁有使用者。

### <a name="the-owning-group"></a>擁有群組

在 POSIX ACL 中，每個使用者都與*主要群組*相關聯。 例如，使用者 "alice" 可能屬於 "finance" 群組。 Alice 也可能屬於多個群組，但一定有一個群組指定為其主要群組。 在 POSIX 中，當 Alice 會建立檔案時，該檔案的擁有群組會設定為她的主要群組，在此案例中為 "finance"。 除此之外，擁有群組的作用類似於指派給其他使用者/群組的權限。

#### <a name="assigning-the-owning-group-for-a-new-file-or-directory"></a>指派新檔案或目錄的擁有群組

* **案例 1**：根目錄 "/"。 在建立 Data Lake Storage Gen2 檔案系統時，即會建立此目錄。 在此案例中，擁有群組會設定為建立檔案系統的使用者 (如果該系統是使用 OAuth 建立的)。 如果檔案系統是使用共用金鑰、帳戶 SAS 或服務 SAS 建立的，則擁有者和擁有群組會設定為 **$superuser**。
* **案例 2** (其餘所有案例)：在建立新項目時，會從父目錄複製擁有群組。

#### <a name="changing-the-owning-group"></a>變更擁有群組

可以變更擁有群組的對象︰
* 任何超級使用者。
* 擁有使用者，如果擁有使用者也是目標群組的成員。

> [!NOTE]
> 擁有群組無法變更檔案或目錄的 ACL。  雖然在上述根目錄的案例 (**案例 1**) 中，擁有群組設定為建立帳戶的使用者，但單一使用者帳戶並無法透過擁有群組來提供權限。 您可以將此權限指派給有效的使用者群組 (如果適用的話)。

## <a name="access-check-algorithm"></a>存取檢查演算法

以下的虛擬程式碼代表 Data Lake Storage Gen2 帳戶的存取檢查演算法。

```
def access_check( user, desired_perms, path ) : 
  # access_check returns true if user has the desired permissions on the path, false otherwise
  # user is the identity that wants to perform an operation on path
  # desired_perms is a simple integer with values from 0 to 7 ( R=4, W=2, X=1). User desires these permissions
  # path is the file or directory
  # Note: the "sticky bit" is not illustrated in this algorithm
  
# Handle super users.
  if (is_superuser(user)) :
    return True

# Handle the owning user. Note that mask IS NOT used.
entry = get_acl_entry( path, OWNER )
if (user == entry.identity)
    return ( (desired_perms & entry.permissions) == desired_perms )

# Handle the named users. Note that mask IS used.
entries = get_acl_entries( path, NAMED_USER )
for entry in entries:
    if (user == entry.identity ) :
        mask = get_mask( path )
        return ( (desired_perms & entry.permissions & mask) == desired_perms)

# Handle named groups and owning group
member_count = 0
perms = 0
entries = get_acl_entries( path, NAMED_GROUP | OWNING_GROUP )
for entry in entries:
if (user_is_member_of_group(user, entry.identity)) :
    member_count += 1
    perms | =  entry.permissions
if (member_count>0) :
return ((desired_perms & perms & mask ) == desired_perms)

# Handle other
perms = get_perms_for_other(path)
mask = get_mask( path )
return ( (desired_perms & perms & mask ) == desired_perms)
```

### <a name="the-mask"></a>遮罩

如「存取檢查演算法」所說明，遮罩會限制具名使用者、擁有群組及具名群組的存取。  

> [!NOTE]
> 對於新的 Data Lake Storage Gen2 檔案系統，根目錄 ("/") 的存取 ACL 遮罩會預設為 750 (目錄) 和 640 (檔案)。 檔案不會接收 X 位元，因為它與僅限儲存的系統中包含的檔案無關。
>
> 遮罩可就個別的呼叫指定。 這可讓不同的取用系統 (例如叢集) 將不同的有效遮罩用於其檔案作業上。 指定於給定要求上的遮罩會完全覆寫預設遮罩。

### <a name="the-sticky-bit"></a>黏性位元

黏性位元是 POSIX 檔案系統的進階功能。 在 Data Lake Storage Gen2 的內容中，不太可能需要黏性位元。 總而言之，如果已在目錄上啟用黏性位元，子項目便只能由子項目的擁有使用者刪除或重新命名。

黏性位元不會顯示在 Azure 入口網站中。

## <a name="default-permissions-on-new-files-and-directories"></a>新檔案和目錄的預設權限

在現有目錄下建立新檔案或目錄時，父目錄的預設 ACL 可決定︰

- 子目錄的預設 ACL 與存取 ACL。
- 子檔案的存取 ACL (檔案沒有預設 ACL)。

### <a name="umask"></a>umask

在建立檔案或目錄時，可使用 umask 來修改子項目上的預設 ACL 設定方式。 umask 是父目錄上的一個 9 位元值，其中包含一個用於**擁有使用者**、**擁有群組**及**其他**的 RWX 值。

Azure Data Lake Storage Gen2 的 umask 是一個設定為 007 的常數值。 此值會轉譯成：

| umask 元件     | 數值形式 | 簡短形式 | 意義 |
|---------------------|--------------|------------|---------|
| umask.owning_user   |    0         |   `---`      | 針對擁有使用者，將父代的預設 ACL 複製到子系的存取 ACL | 
| umask.owning_group  |    0         |   `---`      | 針對擁有群組，將父代的預設 ACL 複製到子系的存取 ACL | 
| umask.other         |    7         |   `RWX`      | 針對其他，移除子系存取 ACL 上的所有權限 |

Azure Data Lake Storage Gen2 所使用的 umask 值實際上意謂著不論預設 ACL 的指示為何，依預設一律不會在新子系上傳輸用於**其他**的值。 

下列虛擬程式碼說明為子項目建立 ACL 時套用 umask 的方式。

```
def set_default_acls_for_new_child(parent, child):
    child.acls = []
    for entry in parent.acls :
        new_entry = None
        if (entry.type == OWNING_USER) :
            new_entry = entry.clone(perms = entry.perms & (~umask.owning_user))
        elif (entry.type == OWNING_GROUP) :
            new_entry = entry.clone(perms = entry.perms & (~umask.owning_group))
        elif (entry.type == OTHER) :
            new_entry = entry.clone(perms = entry.perms & (~umask.other))
        else :
            new_entry = entry.clone(perms = entry.perms )
        child_acls.add( new_entry )
```

## <a name="common-questions-about-acls-in-data-lake-storage-gen2"></a>Data Lake Storage Gen2 中 ACL 的相關常見問題

### <a name="do-i-have-to-enable-support-for-acls"></a>我必須啟用 ACL 的支援嗎？

沒有。 只要階層式命名空間 (HNS) 功能開啟，就會為 Data Lake Storage Gen2 帳戶啟用透過 ACL 的存取控制。

如果 HNS 關閉，Azure RBAC 授權規則仍適用。

### <a name="what-is-the-best-way-to-apply-acls"></a>套用 ACL 的最佳方式為何？

一律使用 Azure AD 安全性群組作為 ACL 中指派的主體。 避免直接指派個別使用者或服務主體。 使用此結構，可讓您直接新增和移除使用者或服務主體，而不需要將 ACL 重新套用至整個目錄結構。 相對地，您只需要從適當的 Azure AD 安全性群組中加以新增或移除即可。 請記住，ACL 是不可繼承的，因此若要重新套用 ACL，必須更新每個檔案和子目錄的 ACL。 

### <a name="which-permissions-are-required-to-recursively-delete-a-directory-and-its-contents"></a>若要以遞迴方式刪除目錄及其內容，需要哪些權限？

- 呼叫端具有「超級使用者」權限，

或

- 父目錄必須具有 [寫入 + 執行] 權限。
- 要刪除的目錄及其中的每個目錄，都需要 [讀取 + 寫入 + 執行] 權限。

> [!NOTE]
> 您不需要寫入權限即可刪除目錄中的檔案。 此外，決不可刪除根目錄 "/"。

### <a name="who-is-the-owner-of-a-file-or-directory"></a>誰是檔案或目錄的擁有者？

檔案或目錄的建立者會成為擁有者。 就根目錄而言，這會是檔案系統建立者的使用者身分識別。

### <a name="which-group-is-set-as-the-owning-group-of-a-file-or-directory-at-creation"></a>在建立檔案或目錄時，會將哪個群組設定為擁有群組？

擁有群組是從新檔案或目錄建立所在父目錄的擁有群組複製而來的。

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>我是檔案的擁有使用者，但沒有我需要的 RWX 權限。 該怎麼辦？

擁有使用者可以變更檔案的權限，以取得本身所需的任何 RWX 權限。

### <a name="why-do-i-sometimes-see-guids-in-acls"></a>為什麼我有時候會在 ACL 中看到 GUID？

如果項目代表使用者，而使用者已不存在於 Azure AD 中，則會顯示 GUID。 當使用者已離開公司，或已在 Azure AD 中刪除其帳戶時，通常會發生這種情形。 此外，服務主體和安全性群組並沒有使用者主體名稱 (UPN) 可資識別，因此會藉由其 OID 屬性 (GUID) 來顯示。

### <a name="how-do-i-set-acls-correctly-for-a-service-principal"></a>如何設定 Acl 正確的服務主體？

當您定義 Acl 的服務主體時，務必要使用物件識別碼 (OID) 的*服務主體*您所建立的應用程式註冊。 請務必請注意，已註冊的應用程式就會有不同的服務主體中的特定 Azure AD 租用戶。 已註冊的應用程式都有 OID，會顯示在 Azure 入口網站中，但*服務主體*有另一個 （差異） 的 OID。

若要取得服務主體，對應至應用程式註冊的 OID，您可以使用`az ad sp show`命令。 指定應用程式識別碼做為參數。 以下是範例取得對應至應用程式識別碼的應用程式註冊為服務主體的 OID = 18218b12-1895年-43e9-ad80-6e8fc1ea88ce。 在 Azure CLI 中執行下列命令：

`az ad sp show --id 18218b12-1895-43e9-ad80-6e8fc1ea88ce --query objectId
<<OID will be displayed>>`

當您有正確的 OID，服務主體時，請移至儲存體總管**Manage Access**頁面以新增 OID，並指派適當的權限的 OID。 請確定您選取**儲存**。

### <a name="does-data-lake-storage-gen2-support-inheritance-of-acls"></a>Data Lake Storage Gen2 是否支援 ACL 的繼承？

Azure RBAC 指派可以繼承。 指派會從訂用帳戶、資源群組和儲存體帳戶資源向下傳至檔案系統資源。

ACL 則不會繼承。 但預設 ACL 可以用來為父目錄下建立的子目錄和檔案設定 ACL。 

### <a name="where-can-i-learn-more-about-posix-access-control-model"></a>何處可以進一步了解 POSIX 存取控制模型？

* [Linux 上的 POSIX 存取控制清單](https://www.linux.com/news/posix-acls-linux)
* [HDFS 權限指南](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)
* [POSIX 常見問題集](https://www.opengroup.org/austin/papers/posix_faq.html)
* [POSIX 1003.1 2008](https://standards.ieee.org/findstds/standard/1003.1-2008.html)
* [POSIX 1003.1 2013](https://pubs.opengroup.org/onlinepubs/9699919799.2013edition/)
* [POSIX 1003.1 2016](https://pubs.opengroup.org/onlinepubs/9699919799.2016edition/)
* [Ubuntu 上的 POSIX ACL](https://help.ubuntu.com/community/FilePermissionsACLs)
* [Linux 上使用存取控制清單的 ACL](https://bencane.com/2012/05/27/acl-using-access-control-lists-on-linux/)

## <a name="see-also"></a>請參閱

* [Azure Data Lake Storage Gen2 概觀](../blobs/data-lake-storage-introduction.md)
