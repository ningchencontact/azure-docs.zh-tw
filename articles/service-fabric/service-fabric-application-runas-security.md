---
title: 在系統和本機安全性帳戶下執行 Azure Service Fabric 服務 | Microsoft Docs
description: 了解如何在系統和本機安全性帳戶下執行 Service Fabric 應用程式。  建立安全性主體並套用「執行身分」原則以安全地執行您的服務。
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/21/2018
ms.author: mfussell
ms.openlocfilehash: 3df5374911ee6381f25d08d23d565cdf8a7cd12f
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2018
---
# <a name="run-a-service-as-a-local-user-account-or-local-system-account"></a>以本機使用者帳戶或本機系統帳戶身分執行服務
藉由使用 Azure Service Fabric，您便可以保護在叢集中以不同使用者帳戶執行的應用程式。 根據預設，Service Fabric 應用程式會在用以執行 Fabric.exe 程序的帳戶之下執行。 Service Fabric 也提供以本機使用者帳戶或本機系統帳戶執行應用程式的功能，做法是在應用程式資訊清單內指定 RunAs 原則。 支援的本機系統帳戶類型為 **LocalUser**、**NetworkService**、**LocalService** 和 **LocalSystem**。  如果您在 Windows 獨立叢集上執行 Service Fabric，便可以在 [Active Directory 網域帳戶](service-fabric-run-service-as-ad-user-or-group.md)或[群組受控服務帳戶](service-fabric-run-service-as-gmsa.md)底下執行服務。

您也可以定義和建立使用者群組，以便將一或多個使用者新增至每個要一起管理的群組。 當不同的服務進入點有多個使用者，而且他們需要具備可在群組層級取得的某些常見權限時，這會很有用。

> [!NOTE] 
> 如果您將 RunAs 原則套用到服務，而服務資訊清單宣告具有 HTTP 通訊協定的端點資源，您就必須指定 **SecurityAccessPolicy**。  如需詳細資訊，請參閱[為 HTTP 和 HTTPS 端點指派安全性存取原則](service-fabric-assign-policy-to-endpoint.md)。 
>

## <a name="create-local-user-groups"></a>建立本機使用者群組
您可以定義和建立使用者群組，然後將一或多個使用者新增至群組。 當不同的服務進入點有多個使用者，而他們需要具備可在群組層級取得的某些通用權限時，這會很有用。 下列範例顯示名為 **LocalAdminGroup** 且具有系統管理員權限的本機群組。 在此應用程式資訊清單範例中，會使 Customer1 和 Customer2 這兩個使用者成為此本機群組的成員：

```xml
<Principals>
 <Groups>
   <Group Name="LocalAdminGroup">
     <Membership>
       <SystemGroup Name="Administrators"/>
     </Membership>
   </Group>
 </Groups>
  <Users>
     <User Name="Customer1">
        <MemberOf>
           <Group NameRef="LocalAdminGroup" />
        </MemberOf>
     </User>
    <User Name="Customer2">
      <MemberOf>
        <Group NameRef="LocalAdminGroup" />
      </MemberOf>
    </User>
  </Users>
</Principals>
```

## <a name="create-local-users"></a>建立本機使用者
您可以建立一個本機使用者，以便用來協助保護應用程式內的服務。 在應用程式資訊清單的主體區段中指定 **LocalUser** 帳戶類型時，Service Fabric 會在部署應用程式所在的電腦上建立本機使用者帳戶。 根據預設，這些帳戶的名稱不會與應用程式資訊清單中所指定的名稱相同 (例如，以下應用程式資訊清單範例中的 Customer3)。 相反地，它們會動態產生並具有隨機密碼。

```xml
<Principals>
  <Users>
     <User Name="Customer3" AccountType="LocalUser" />
  </Users>
</Principals>
```

如果應用程式要求使用者帳戶和密碼在所有電腦上都必須相同 (例如，為了啟用 NTLM 驗證)，叢集資訊清單就必須將 **NTLMAuthenticationEnabled** 設定為 true。 叢集資訊清單也必須指定用來在所有電腦上產生相同密碼的 **NTLMAuthenticationPasswordSecret**。

```xml
<Section Name="Hosting">
      <Parameter Name="EndpointProviderEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationPassworkSecret" Value="******" IsEncrypted="true"/>
 </Section>
```

## <a name="assign-policies-to-the-service-code-packages"></a>將原則指派給服務程式碼封裝
**ServiceManifestImport** 的 **RunAsPolicy** 區段會指定主體區段中應用來執行程式碼封裝的帳戶。 它也會將服務資訊清單中的程式碼封裝關聯至主體區段中的使用者帳戶。 您可以為安裝或主要進入點指定此原則，或指定 `All` 以將其套用到兩者。 下列範例顯示套用不同的原則：

```xml
<Policies>
<RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup"/>
<RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main"/>
</Policies>
```

如果未指定 **EntryPointType**，則預設會設定為 `EntryPointType=”Main”`。 當您想要以系統帳戶執行某些高權限設定作業時，指定 **SetupEntryPoint** 特別有用。 如需詳細資訊，請參閱[以本機使用者或系統帳戶身分執行服務啟動指令碼](service-fabric-run-script-at-service-startup.md)。 實際的服務程式碼可利用較低權限的帳戶來執行。

## <a name="apply-a-default-policy-to-all-service-code-packages"></a>將預設原則套用到所有的服務程式碼封裝
您使用 **DefaultRunAsPolicy** 區段來針對未定義特定 **RunAsPolicy** 的所有程式碼套件，指定預設的使用者帳戶。 如果在應用程式所使用的服務資訊清單中指定的大部分程式碼封裝必須以相同的使用者身分執行，則應用程式可以只使用該使用者帳戶定義預設的 RunAs 原則。 下列範例指定某個程式碼封裝未指定 **RunAsPolicy** 時，該程式碼封裝應該以主體區段中指定的 **MyDefaultAccount** 執行。

```xml
<Policies>
  <DefaultRunAsPolicy UserRef="MyDefaultAccount"/>
</Policies>
```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>後續步驟
* [了解應用程式模型](service-fabric-application-model.md)
* [在服務資訊清單中指定資源](service-fabric-service-manifest-resources.md)
* [部署應用程式](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
