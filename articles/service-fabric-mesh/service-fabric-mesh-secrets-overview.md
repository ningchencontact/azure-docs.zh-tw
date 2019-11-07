---
title: 儲存和使用 Azure Service Fabric Mesh 應用程式秘密 | Microsoft Docs
description: Service Fabric Mesh 支援以祕密作為 Azure 資源。 以下說明如何使用您的 Service Fabric 網狀應用程式來儲存和管理秘密。
services: service-fabric-mesh
keywords: 密碼
author: v-steg
ms.author: jeconnoc
ms.date: 10/25/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: jeconnoc
ms.openlocfilehash: 72188517c237b170b709c48f16d3c131985f95d1
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686230"
---
# <a name="service-fabric-mesh-application-secrets"></a>Service Fabric Mesh 應用程式秘密
Service Fabric Mesh 支援以祕密作為 Azure 資源。 Service Fabric Mesh 秘密可以是任何機密文字資訊，例如儲存體連接字串、密碼，或其他應安全地儲存和傳輸的值。

![Mesh 秘密概觀][sf-mesh-secrets-overview]

## <a name="mesh-secrets-resources"></a>Mesh 秘密資源
Mesh 應用程式秘密包含：
* **祕密**資源，也就是可儲存文字祕密的容器。 包含在**祕密**資源中的祕密會以安全的方式儲存和傳輸。
* 一或多個儲存在**祕密**資源容器中的**祕密/值**資源。 各個**祕密/值**資源可用版本號碼來區分。

## <a name="next-steps"></a>後續步驟 
若要深入了解 Service Fabric Mesh 秘密，請參閱：
- [管理 Service Fabric Mesh 應用程式秘密](service-fabric-mesh-howto-manage-secrets.md)
- [Azure Service Fabric 資源模型簡介](service-fabric-mesh-service-fabric-resources.md)

<!-- pics -->
[sf-mesh-secrets-overview]: ./media/service-fabric-mesh-secrets-overview/MeshAppSecretsOverview.png
