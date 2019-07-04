---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/24/2019
ms.openlocfilehash: 4cdcec850f32d7e94f33eb28e5bf7839e511f347
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67174302"
---
填寫並提交[認知服務的視覺容器要求表單](https://aka.ms/VisionContainersPreview)要求存取至容器。 該表格需要有關您本身、您的公司，以及您將會使用該容器之使用者情節的資訊。 您提交表單之後，Azure 認知服務小組會檢閱，以確保您符合私人容器登錄的存取權的準則。

> [!IMPORTANT]
> 您必須使用 Microsoft 帳戶 (MSA) 或在表單中的 Azure Active Directory (Azure AD) 帳戶相關聯的電子郵件地址。

如果您的要求獲得核准，您會收到一封電子郵件，說明如何取得您的認證和存取私人容器登錄庫的指示。

## <a name="log-in-to-the-private-container-registry"></a>登入私人容器登錄

有數種方式可向私人容器登錄庫，適用於認知服務容器。 我們建議您透過使用命令列方法[Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/)。

使用[docker 登入](https://docs.docker.com/engine/reference/commandline/login/)命令，如下列範例中，顯示登入`containerpreview.azurecr.io`，這是私人容器登錄的認知服務容器。 將 *\<username\>* 取代為使用者名稱，並將 *\<password\>* 取代為您從 Azure 認知服務小組收到的認證所提供的密碼。

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

如果您保護您的認證在文字檔中，您可以串連至該文字檔的內容`docker login`命令。 使用`cat`命令，如下列範例所示。 取代 *\<passwordFile\>* 的路徑和文字檔案，其中包含密碼的名稱。 取代 *\<使用者名稱\>* 提供您的認證的使用者名稱。

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

