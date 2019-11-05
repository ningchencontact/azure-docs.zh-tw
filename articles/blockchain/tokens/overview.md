---
title: 什麼是 Azure 區塊鏈權杖
description: Azure 區塊鏈 token 是一個平臺即服務（PaaS），可用於權杖發行和管理。
services: azure-blockchain
author: PatAltimore
ms.author: patricka
ms.date: 11/04/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
ms.openlocfilehash: d04c4202f89e58185465669c3da0f7d5e4d7b9bb
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73518641"
---
# <a name="what-is-azure-blockchain-tokens"></a>什麼是 Azure 區塊鏈 token？

[!INCLUDE [Preview note](./includes/preview.md)]

Azure 區塊鏈 token 是一個平臺即服務（PaaS），可在 Azure 中跨區塊鏈總帳進行標準化權杖發佈和管理。

使用 Azure 區塊鏈權杖，您可以使用預先建立的權杖範本，為您的區塊鏈解決方案建立標準化權杖。 您也可以使用服務來撰寫自己的權杖範本。 建立之後，請使用 Azure 區塊鏈 token 來連線併發行區塊鏈上的權杖。 一旦發出之後，您就可以管理跨多個區塊鏈網路的權杖。

## <a name="templates"></a>範本

使用 Azure 區塊鏈權杖來選取預先建立的權杖範本，或建立您自己的權杖範本。 Azure 區塊鏈 token 支援權杖範本複合性，可讓您根據支援的行為建立自己的權杖範本。 權杖範本可用於大部分的區塊鏈解決方案，因為它們會對應至最常使用的權杖。 您可以從範本開始進行個人化，並為您的解決方案部署權杖。

如需 Azure 區塊鏈 token 範本的詳細資訊，請參閱[Azure 區塊鏈 token 範本](templates.md)。

## <a name="management"></a>管理

Azure 區塊鏈 token 提供 Azure 入口網站的管理和 Api，以連線到現有的區塊鏈網路。 目前，您可以連接到[Azure 區塊鏈 Service](../service/overview.md)或其他乙太坊系列區塊鏈。

連線至一或多個區塊鏈網路之後，您可以使用 Azure 區塊鏈 token Api 來發行和管理權杖，以便在您的區塊鏈解決方案中使用。 使用 Api，您可以將權杖管理整合到您的商務應用程式和邏輯中。 例如，您可以使用 REST API 來管理權杖，而不是直接在區塊鏈上管理權杖。

## <a name="blockchains-and-accounts"></a>區塊鏈和帳戶

Azure 區塊鏈 token 提供 Azure 入口網站的管理和 Api，可在已連線的區塊鏈網路上建立新的群組和新的區塊鏈帳戶。 您可以直接在已連線的網路上建立新的帳戶，而 Azure 區塊鏈 token 會代表您管理您的帳戶私密金鑰。 使用群組，您可以將來自多個網路的不同區塊鏈帳戶群組在一起，並透過群組來管理存取控制。

如需 Azure 區塊鏈 token 帳戶管理的詳細資訊，請參閱[Azure 區塊鏈權杖帳戶管理](account-management.md)。

## <a name="token-taxonomy-framework"></a>Token 分類法架構

Azure 區塊鏈 Token 建基於以標準為主的基礎，名為權杖分類法架構（.TTF）。 .TTF 是從[權杖分類法方案](https://entethalliance.org/participate/token-taxonomy-initiative/)（TTI）權杖工作群組建立的一組交付專案。 TTI 工作群組會定義權杖的商務分類法，以及可跨所有主要總帳（包括乙太坊、仲裁、Corda 和 Hyperledger Fabric）套用的行為。 工作群組的目標是要建立架構，以標準化商務觀點的權杖使用，以促進簡化和將大眾化權杖型開發。 藉由讓產業定義這些權杖及其在商務層級的行為，會從操作權杖的商務邏輯中抽象化權杖的詳細執行。

## <a name="next-steps"></a>後續步驟

深入瞭解可用的[Azure 區塊鏈 token 範本](templates.md)。
