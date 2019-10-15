---
title: 在 Azure Spring Cloud 中設定 Config Server | Microsoft Docs
description: 在此教學課程中，您將了解如何在 Azure 入口網站上，為您的 Azure Spring Cloud 設定 Spring Cloud Config Server
services: spring-cloud
ms.service: spring-cloud
ms.topic: tutorial
ms.reviewer: jeconnoc
ms.author: v-vasuke
author: v-vasuke
ms.date: 08/08/2019
ms.openlocfilehash: f959d216859ded54de52de474ee3150f91bcfff1
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038650"
---
# <a name="tutorial-set-up-a-spring-cloud-config-server-for-your-service"></a>教學課程：為您的服務設定 Spring Cloud Config Server

此此教學課程將為您示範如何將 Spring Cloud Config Server 連線至 Azure Spring Cloud 服務。

Spring Cloud Config 能夠在分散式系統中提供外部化設定的伺服器端和用戶端支援。 透過 Config Server，您就可以在所有環境中集中管理應用程式的外部屬性。 若要深入了解，請造訪 [Spring Cloud Config Server 參考](https://spring.io/projects/spring-cloud-config) \(英文\)。

## <a name="prerequisites"></a>必要條件
* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
* 已佈建且正在執行的 Azure Spring Cloud 服務。  完成[本快速入門](spring-cloud-quickstart-launch-app-cli.md)以佈建並啟動 Azure Spring Cloud 服務。


## <a name="restriction"></a>限制

當您使用 __Config Server__ 搭配 git 後端時，會有一些限制。 有些屬性將會自動插入至您的應用程式環境，以存取 __Config Server__ 和__服務探索__。 如果您也從 **Config Server** 檔案中設定這些屬性，可能會發生衝突和非預期的行為。  這些屬性包括： 

```yaml
eureka.client.service-url.defaultZone
eureka.client.tls.keystore
server.port
spring.cloud.config.tls.keystore
spring.application.name
```
> [!CAUTION]
> 我們強烈建議您__不要__將上述屬性放在您的 __Config Server__ 應用程式檔案中。

## <a name="create-your-config-server-files"></a>建立 Config Server 檔案

Azure Spring Cloud 支援 Azure DevOps、GitHub、GitLab 和 Bitbucket，以儲存您的 Config Server 檔案。  當您的存放庫準備就緒時，請使用下列指示建立組態檔，並將其儲存在該處。

此外，有些可設定的屬性僅適用於特定類型。 下列小節會列出每個存放庫類型的屬性。


### <a name="public-repository"></a>公用存放庫

使用公用存放庫時，可設定的屬性將會受到更多限制。

以下列出用來設定公用 `Git` 存放庫的所有可設定屬性。

> [!NOTE]
> 目前僅支援連字號 ("-") 分隔字組命名慣例。 例如，您使用 `default-label`，不是 `defaultLabel`。

| 屬性        | 必要 | 功能                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | `yes`    | 當作 Config Server 後端使用的 `Git` 存放庫的 `uri` 應該從 `http://`、`https://`、`git@` 或 `ssh://` 開始。 |
| `default-label` | `no`     | `Git` 存放庫的預設標籤應該是存放庫的 `branch name`、`tag name` 或 `commit-id`。 |
| `search-paths`  | `no`     | 用來搜尋 `Git` 存放庫子目錄的字串陣列。 |

------

### <a name="private-repository-with-ssh-authentication"></a>使用 SSH 驗證的私人存放庫

以下列出透過 `Ssh` 設定私人 `Git` 存放庫所使用的所有可設定屬性。

> [!NOTE]
> 目前僅支援連字號 ("-") 分隔字組命名慣例。 例如，您使用 `default-label`，不是 `defaultLabel`。

| 屬性                   | 必要 | 功能                                                      |
| :------------------------- | -------- | ------------------------------------------------------------ |
| `uri`                      | `yes`    | 當作 Config Server 後端使用的 `Git` 存放庫的 `uri` 應該從 `http://`、`https://`、`git@` 或 `ssh://` 開始。 |
| `default-label`            | `no`     | `Git` 存放庫的預設標籤應該是存放庫的 `branch name`、`tag name` 或 `commit-id`。 |
| `search-paths`             | `no`     | 用來搜尋 `Git` 存放庫子目錄的字串陣列。 |
| `private-key`              | `no`     | 當 `uri` 從 `git@` 或 `ssh://` 開始時，存取 `Git` 存放庫__所需__的 `Ssh` 私密金鑰。 |
| `host-key`                 | `no`     | Git 存放庫伺服器的主機金鑰不應包含 `host-key-algorithm` 所涵蓋的演算法前置詞。 |
| `host-key-algorithm`       | `no`     | 主機金鑰演算法應該是 `ssh-dss` 的其中一個。 `ssh-rsa`、`ecdsa-sha2-nistp256`、`ecdsa-sha2-nistp384` 和 `ecdsa-sha2-nistp521`。 只有在 `host-key` 存在時才需要。 |
| `strict-host-key-checking` | `no`     | 指出利用提供 `host-key` 時，Config Server 是否無法啟動，應為 `true` (預設值) 或 `false`。 |

-----

### <a name="private-repository-with-basic-authentication"></a>使用基本驗證的私人存放庫

以下列出透過基本驗證設定私人 Git 存放庫所使用的所有可設定屬性。

> [!NOTE]
> 目前僅支援連字號 ("-") 分隔字組命名慣例。 例如，您可以使用 `default-label` 但不是 `defaultLabel`。

| 屬性        | 必要 | 功能                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | `yes`    | 當作 Config Server 後端使用的 `Git` 存放庫的 `uri` 應該從 `http://`、`https://`、`git@` 或 `ssh://` 開始。 |
| `default-label` | `no`     | `Git` 存放庫的預設標籤應該是存放庫的 `branch name`、`tag name` 或 `commit-id`。 |
| `search-paths`  | `no`     | 用來搜尋 `Git` 存放庫子目錄的字串陣列。 |
| `username`      | `no`     | 用來存取 `Git` 存放庫伺服器的 `username`，__必要的__ `Git` 存放庫伺服器支援 `Http Basic Authentication`。 |
| `password`      | `no`     | 用來存取 `Git` 存放庫伺服器的密碼，__必要的__ `Git` 存放庫伺服器支援 `Http Basic Authentication`。 |

> [!NOTE]
> 某些 `Git` 存放庫伺服器 (例如 GitHub) 支援「個人權杖」或「存取權杖」作為 `HTTP Basic Authentication` 的密碼。 您也可以在這裡使用該類型的權杖作為密碼，而且「個人權杖」或「存取權杖」將不會過期。 但若是 Git 存放庫伺服器 (例如 BitBucket 和 Azure DevOps)，權杖將會在一或兩個小時後過期，讓該選項無法搭配 Azure Spring Cloud 使用。

### <a name="git-repositories-with-pattern"></a>使用模式的 Git 存放庫

以下列出透過模式設定 Git 存放庫所使用的所有可設定屬性。

> [!NOTE]
> 目前僅支援連字號 ("-") 分隔字組命名慣例。 例如，您可以使用 `default-label` 但不是 `defaultLabel`。

| 屬性                           | 必要         | 功能                                                      |
| :--------------------------------- | ---------------- | ------------------------------------------------------------ |
| `repos`                            | `no`             | 對應是由具有指定名稱的 `Git` 存放庫設定所組成。 |
| `repos."uri"`                      | `repos` 上的 `yes` | 當作 Config Server 後端使用的 `Git` 存放庫的 `uri` 應該從 `http://`、`https://`、`git@` 或 `ssh://` 開始。 |
| `repos."name"`                     | `repos` 上的 `yes` | 識別一個 `Git` 存放庫的名稱，只有在 `repos` 存在時，才__需要__。 例如，從上面的 `team-A`、`team-B`。 |
| `repos."pattern"`                  | `no`             | 用來比對應用程式名稱的字串陣列，針對每個模式，採用具有萬用字元的 `{application}/{profile}` 格式。 |
| `repos."default-label"`            | `no`             | `Git` 存放庫的預設標籤應該是存放庫的 `branch name`、`tag name` 或 `commit-id`。 |
| `repos."search-paths`"             | `no`             | 用來搜尋 `Git` 存放庫子目錄的字串陣列。 |
| `repos."username"`                 | `no`             | 用來存取 `Git` 存放庫伺服器的 `username`，__必要的__ `Git` 存放庫伺服器支援 `Http Basic Authentication`。 |
| `repos."password"`                 | `no`             | 用來存取 `Git` 存放庫伺服器的密碼，__必要的__ `Git` 存放庫伺服器支援 `Http Basic Authentication`。 |
| `repos."private-key"`              | `no`             | 當 `uri` 從 `git@` 或 `ssh://` 開始時，存取 `Git` 存放庫__所需__的 `Ssh` 私密金鑰。 |
| `repos."host-key"`                 | `no`             | Git 存放庫伺服器的主機金鑰不應包含 `host-key-algorithm` 所涵蓋的演算法前置詞。 |
| `repos."host-key-algorithm"`       | `no`             | 主機金鑰演算法應該是 `ssh-dss` 的其中一個。 `ssh-rsa`、`ecdsa-sha2-nistp256`、`ecdsa-sha2-nistp384` 和 `ecdsa-sha2-nistp521`。 只有在 `host-key` 存在時才__需要__。 |
| `repos."strict-host-key-checking"` | `no`             | 指出利用提供 `host-key` 時，Config Server 是否無法啟動，應為 `true` (預設值) 或 `false`。 |

### <a name="import-applicationyml-file-from-spring-cloud-config"></a>從 Spring Cloud Config 匯入 `application.yml` 檔案

您可以直接從 [Spring Cloud Config](https://spring.io/projects/spring-cloud-config) 網站匯入一些預設的 Config Server 設定。 您可以直接從 Azure 入口網站執行此動作，因此您不需要立即採取任何步驟，就可以準備您的 Config Server 檔案或存放庫。

## <a name="attaching-your-config-server-repository-to-azure-spring-cloud"></a>將 Config Server 存放庫連接至 Azure Spring Cloud

既然您已經將設定檔儲存在存放庫中，您需要將 Azure Spring Cloud 連線到該存放庫。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 瀏覽至您的 Azure Spring Cloud 的 [概觀]  頁面。

1. 移至左側功能表中 [Config Server]  索引標籤的 [設定]  標題底下。

### <a name="public-repository"></a>公用存放庫

如果您的存放庫是公用的，只要按一下 [公用]  按鈕並貼上 URL 即可。

### <a name="private-repository"></a>私人存放庫

Azure Spring Cloud 支援 SSH 驗證。 依照 Azure 入口網站上的指示，將公開金鑰新增至您的存放庫。 接著，請務必將您的私密金鑰包含在設定檔中。

按一下 [套用]  ，完成 Config Server 的設定。


## <a name="delete-your-app-configuration"></a>刪除應用程式設定

儲存組態檔之後，[刪除應用程式設定]  按鈕將會出現在 [設定]  索引標籤中。這會完全清除您現有的設定。 如果您想要將 Config Server 連線至另一個來源 (例如從 GitHub 移至 Azure DevOps)，您應該執行此動作。

## <a name="next-steps"></a>後續步驟

在此教學課程中，您了解如何啟用和設定 Config Server。 若要深入了解如何管理您的應用程式，請繼續進行手動調整應用程式的教學課程。

> [!div class="nextstepaction"]
> [了解如何手動調整您的 Azure Spring Cloud 應用程式](spring-cloud-tutorial-scale-manual.md)。

