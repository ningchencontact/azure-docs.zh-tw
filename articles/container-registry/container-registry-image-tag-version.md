---
title: Azure Container Registry 中的標記和版本映像
description: 最佳做法標記和版本管理 Docker 容器映像
services: container-registry
author: stevelasker
ms.service: container-registry
ms.topic: article
ms.date: 07/10/2019
ms.author: steve.lasker
ms.openlocfilehash: bd00fd4f8dd247c766eb34849ecf9de603c5171b
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67800389"
---
# <a name="recommendations-for-tagging-and-versioning-container-images"></a>標記和版本控制的容器映像的建議

當部署的容器映像推送至容器登錄庫，然後再部署它們時，您會需要映像標記和版本控制策略。 這篇文章將討論兩種方法，以及每個容器的生命週期期間的什麼位置：

* **穩定標記**-表示主要或次要版本，例如，您重複使用，例如，標記*mycontainerimage:1.0*。
* **唯一的標記**-不同的標記，例如推送至登錄中，每個映像*mycontainerimage:abc123*。

## <a name="stable-tags"></a>穩定的標記

**建議**：使用穩定的標記來維護**基底映像**組建您的容器。 因為這些標記會繼續接收更新，而且可能會造成不一致，在生產環境中的，請避免部署具有穩定的標記。

*穩定標記*表示開發人員或建置系統，可以繼續提取特定的標記，以取得更新會繼續。 穩定並不表示內容已凍結。 相反地，穩定表示影像應該是穩定的該版本的意圖。 若要保持 「 穩定版 」，它可能會提供服務套用安全性修補程式或架構更新。

### <a name="example"></a>範例

Framework 小組提供 1.0 版。 他們知道他們會提供更新，包括次要更新。 若要指定主要和次要版本支援穩定的標記，它們會有兩組穩定的標記。

* `:1` -針對主要版本的穩定的標記。 `1` 表示 「 最新 」 或 「 最新 」 1.* 版本。
* `:1.0`-1.0 版，讓開發人員的 1.0 時，更新繫結，並不會向前復原至 1.1 在其發行的穩定標記。

小組也使用`:latest`標記，它會指向最新的穩定標記，無論是何種目前的主要版本。

當基底映像更新可供使用，或任何類型的服務架構，具有穩定的標記的映像的版本會更新為最新的摘要，表示該版本的最新的穩定版本。

在此情況下，會持續正在服務的主要和次要的標記。 從基本映像的情況下，這可讓提供服務的映像的映像擁有者。

## <a name="unique-tags"></a>唯一的標記

**建議**：使用唯一的標記，如**部署**，尤其是在多個節點無法調整規模的環境。 此外，您可能會想特意部署一致版本的元件。 如果您的容器重新啟動或協調器向外延展多個執行個體，您的主機不會不小心提取較新版本，與其他節點不一致。

唯一的標記，只是表示每個推送至登錄的映像都具有唯一的標記。 標記不會重複使用。 有數種模式，您可以遵循以產生唯一的標記，包括：

* **日期時間戳記**-這種方法是很常見，因為您可以清楚地告訴建置映像時。 但是，如何使它回到您的建置系統相互關聯嗎？ 您可以尋找在相同的時間已完成的組建嗎？ 哪個時區中？ 為校正與 UTC 的所有建置系統嗎？
* **Git 認可**– 這種方法正常運作，直到您啟動支援基底映像更新。 如果基底映像更新時，您的建置系統一開始會使用相同的 Git 認可為前一個組建。 不過，基底映像有新的內容。 一般而言，提供 Git 認可*半*-穩定的標記。
* **資訊清單的摘要**-推送至容器登錄庫的每個容器映像為相關使用資訊清單，識別唯一的 SHA-256 雜湊或摘要。 雖然唯一的摘要會是冗長，不易閱讀，且它與您的建置環境。
* **組建識別碼**-這個選項可能最可能是因為增量，並可讓您將回到特定的組建，來尋找所有成品和記錄檔相互關聯。 不過，資訊清單的摘要，例如，它可能很難閱讀。

  如果您的組織有數個建置系統，前面加上標記的組建系統名稱是此選項的一種變化： `<build-system>-<build-id>`。 例如，您無法區別 API 團隊的 Jenkins 組建系統中的組建，而 web 小組的 Azure 管線建置系統。

## <a name="next-steps"></a>後續步驟

如這篇文章中的概念的更詳細討論，請參閱部落格文章[Docker 標記：標記和版本設定的 docker 映像的最佳做法](https://stevelasker.blog/2018/03/01/docker-tagging-best-practices-for-tagging-and-versioning-docker-images/)。

若要協助提升效能和您的 Azure container registry 的符合成本效益的使用，請參閱[Azure Container Registry 的最佳作法](container-registry-best-practices.md)。

<!-- IMAGES -->


<!-- LINKS - Internal -->

