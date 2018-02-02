---
title: "如何參數化 Azure Service Fabric 中的設定檔 | Microsoft Docs"
description: "示範如何參數化 Service Fabric 中的設定檔"
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: mikhegn
ms.openlocfilehash: 1e7d59ecb231440711b8ed3dc0b27a2b105890c4
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2018
---
# <a name="how-to-parameterize-configuration-files-in-service-fabric"></a>如何參數化 Service Fabric 中的設定檔

本文會示範如何參數化 Service Fabric 中的設定檔。

## <a name="procedure-for-parameterizing-configuration-files"></a>參數化設定檔的程序

在此範例中，您會使用應用程式部署中的參數來覆寫設定值。

1. 開啟 Config\Settings.xml 檔案。
1. 新增下列 XML 以配置設定參數：

    ```xml
      <Section Name="MyConfigSection">
        <Parameter Name="CacheSize" Value="25" />
      </Section>
    ```

1. 儲存並關閉檔案。
1. 開啟 `ApplicationManifest.xml` 檔案。
1. 新增 `ConfigOverride` 元素，並參考設定套件、區段和參數。

      ```xml
        <ConfigOverrides>
          <ConfigOverride Name="Config">
              <Settings>
                <Section Name="MyConfigSection">
                    <Parameter Name="CacheSize" Value="[Stateless1_CacheSize]" />
                </Section>
              </Settings>
          </ConfigOverride>
        </ConfigOverrides>
      ```

1. 仍舊在 ApplicationManifest.xml 檔案中，然後指定 `Parameters` 元素中的參數

    ```xml
      <Parameters>
        <Parameter Name="Stateless1_CacheSize" />
      </Parameters>
    ```

1. 並定義 `DefaultValue`

    ```xml
      <Parameters>
        <Parameter Name="Stateless1_CacheSize" DefaultValue="80" />
      </Parameters>
    ```

> [!NOTE]
> 在您新增 ConfigOverride 的情況下，Service Fabric 一律會選擇應用程式資訊清單中所指定的應用程式參數或預設值。
>
>

使用 Cloud.xml 發行設定檔從 Visual Studio 發行您的應用程式時，您的服務會設定為使用連接埠 80。 如果部署應用程式未指定 MyWebAPI_PortNumber 參數，則服務會使用連接埠 8080。

## <a name="next-steps"></a>後續步驟
若要深入了解這篇文章所討論的某些核心概念，請參閱[管理多個環境發行項的應用程式](service-fabric-manage-multiple-environment-app-configuration.md)。

如需 Visual Studio 中其他可用的應用程式管理功能的相關資訊，請參閱 [在 Visual Studio 中管理 Service Fabric 應用程式](service-fabric-manage-application-in-visual-studio.md)。