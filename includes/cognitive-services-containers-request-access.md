---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/24/2019
ms.openlocfilehash: d5a6692aceedb6dc7fad7a434a433882cdbea015
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/09/2019
ms.locfileid: "55984884"
---
您必須先填寫完成並提交[認知服務視覺容器要求表單](https://aka.ms/VisionContainersPreview)，以要求存取容器。 該表格需要有關您本身、您的公司，以及您將會使用該容器之使用者情節的資訊。 提交之後，Azure 認知服務小組會檢閱該表單，以確保您符合存取私人容器登錄的準則。

> [!IMPORTANT]
> 您必須在表單中使用與 Microsoft 帳戶 (MSA) 或 Azure Active Directory (Azure AD) 帳戶相關聯的電子郵件地址。

如果您的要求獲得核准，您會收到一封電子郵件，其中具有說明如何取得認證並存取私人容器登錄的指示。

## <a name="log-in-to-the-private-container-registry"></a>登入私人容器登錄

有幾個方式可以向認知服務容器的私人容器登錄驗證，但從命令列進行的建議方法是使用 [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/) \(英文\)。

使用下列範例中所示的 [docker login](https://docs.docker.com/engine/reference/commandline/login/) 命令來登入 `containerpreview.azurecr.io` (認知服務容器的私人容器登錄)。 將 *\<username\>* 取代為使用者名稱，並將 *\<password\>* 取代為您從 Azure 認知服務小組收到的認證所提供的密碼。

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

如果您已經將您的認證保護在文字檔中，您可以將該文字檔的內容串連 (使用 `cat` 命令) 至 `docker login` 命令，如下列範例所示。 將 *\<passwordFile\>* 取代為包含密碼之文字檔的路徑和名稱，並將 *\<username\>* 取代為您認證中提供的使用者名稱。

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

