---
title: Service Fabric CLI 指令碼範例 - 更新叢集上的應用程式
description: Service Fabric CLI 指令碼範例 - 以新版本來更新應用程式。 此範例也會以新版本來升級部署的應用程式。
services: service-fabric
documentationcenter: ''
author: aljo-microsoft
manager: timlt
editor: ''
tags: ''
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 12/06/2017
ms.author: aljo
ms.custom: ''
ms.openlocfilehash: 7c6b4b6214f5fc2788827ae34aad4019dad1d8ae
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2019
ms.locfileid: "56806958"
---
# <a name="add-an-application-certificate-to-a-service-fabric-cluster"></a>將應用程式憑證新增到 Service Fabric 叢集

此範例指令碼會上傳現有應用程式的新版本，然後以新版本來升級部署的應用程式。

[!INCLUDE [links to azure cli and service fabric cli](../../../includes/service-fabric-sfctl.md)]

## <a name="sample-script"></a>範例指令碼

[!code-sh[main](../../../cli_scripts/service-fabric/upgrade-application/upgrade-application.sh "Upload and update an application on a Service Fabric cluster")]

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱 [Service Fabric CLI 文件](../service-fabric-cli.md)。

您可以在 [Service Fabric CLI 範例](../samples-cli.md)中找到適用於 Azure Service Fabric 的其他 Service Fabric CLI 範例。
