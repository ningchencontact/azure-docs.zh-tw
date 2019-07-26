---
title: 參數化 Azure Service Fabric 中的設定檔 | Microsoft Docs
description: 了解如何參數化 Service Fabric 中的設定檔。
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/09/2018
ms.author: mikhegn
ms.openlocfilehash: dad497978de7187177998524db3b2f2ee448c717
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2019
ms.locfileid: "68464773"
---
# <a name="how-to-parameterize-configuration-files-in-service-fabric"></a>如何參數化 Service Fabric 中的設定檔

本文會示範如何參數化 Service Fabric 中的設定檔。  如果您尚未熟悉管理多個環境之應用程式的核心概念，請參閱[管理多個環境的應用程式](service-fabric-manage-multiple-environment-app-configuration.md)。

## <a name="procedure-for-parameterizing-configuration-files"></a>參數化設定檔的程序

在此範例中，您會使用應用程式部署中的參數來覆寫設定值。

1. 在服務專案中開啟*MyService>\PackageRoot\Config\Settings.xml檔案。\<*
1. 新增下列 XML 來設定組態參數名稱和值 (例如快取大小等於 25)：

   ```xml
    <Section Name="MyConfigSection">
      <Parameter Name="CacheSize" Value="25" />
    </Section>
   ```

1. 儲存並關閉檔案。
1. 開啟 MyApplication *>\ApplicationPackageRoot\ApplicationManifest.xml檔案。\<*
1. 在 ApplicationManifest.xml 檔案中，於 `Parameters` 元素中宣告參數和預設值。  建議使參數名稱包含服務的名稱 (例如 "MyService")。

   ```xml
    <Parameters>
      <Parameter Name="MyService_CacheSize" DefaultValue="80" />
    </Parameters>
   ```
1. 在 ApplicationManifest 的`ConfigOverrides` `ConfigOverride`區段中, 新增和元素, 並參考設定套件、區段和參數。 `ServiceManifestImport`

   ```xml
    <ConfigOverrides>
      <ConfigOverride Name="Config">
          <Settings>
            <Section Name="MyConfigSection">
                <Parameter Name="CacheSize" Value="[MyService_CacheSize]" />
            </Section>
          </Settings>
      </ConfigOverride>
    </ConfigOverrides>
   ```

> [!NOTE]
> 在您新增 ConfigOverride 的情況下，Service Fabric 一律會選擇應用程式資訊清單中所指定的應用程式參數或預設值。
>
>

## <a name="next-steps"></a>後續步驟
如需 Visual Studio 中其他可用的應用程式管理功能的相關資訊，請參閱 [在 Visual Studio 中管理 Service Fabric 應用程式](service-fabric-manage-application-in-visual-studio.md)。
