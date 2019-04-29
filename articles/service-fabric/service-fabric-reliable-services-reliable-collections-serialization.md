---
title: Azure Service Fabric 中的 Reliable Collection 物件序列化 | Microsoft Docs
description: Azure Service Fabric Reliable Collections 物件序列化
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: masnider,rajak
ms.assetid: 9d35374c-2d75-4856-b776-e59284641956
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/8/2017
ms.author: aljo
ms.openlocfilehash: ee19be45915b3ff1253ec721f4334fead19647b8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60723588"
---
# <a name="reliable-collection-object-serialization-in-azure-service-fabric"></a>Azure Service Fabric 中的 Reliable Collection 物件序列化
Reliable Collections 會複寫並保存其項目，以確保在電腦發生失敗和電力中斷時能持續保留這些項目。
不論是要複寫還是保存項目，Reliable Collections 都必須將它們序列化。

Reliable Collections 會從 Reliable State Manager 為指定的類型取得適當的序列化程式。
Reliable State Manager 包含內建的序列化程式，並允許為指定的類型註冊自訂序列化程式。

## <a name="built-in-serializers"></a>內建的序列化程式

Reliable State Manager 包含一些常見類型的內建序列化程式，以便透過預設方式有效率地將這些類型序列化。 針對其他類型，Reliable State Manager 會回復為使用 [DataContractSerializer](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractserializer(v=vs.110).aspx)。
內建的序列化程式較有效率，因為它們知道它們的類型無法變更，所以不需要包含類型的相關資訊 (例如其類型名稱)。

Reliable State Manager 具有下列類型的內建序列化程式： 
- Guid
- 布林
- byte
- sbyte
- byte[]
- char
- string
- decimal
- double
- float
- int
- uint
- long
- ulong
- short
- ushort

## <a name="custom-serialization"></a>自訂序列化

自訂序列化程式通常是用來提升效能，或是透過網路和在磁碟上加密資料。 自訂序列化程式通常比一般序列化程式更有效率，最主要的原因是因為它們不需要將類型的相關資訊序列化。 

[IReliableStateManager.TryAddStateSerializer<T>](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.ireliablestatemanager.tryaddstateserializer) 用于为给定类型 T 注册自定义串行化程序。此注册应在 StatefulServiceBase 构造内发生，以确保在开始恢复前，所有 Reliable Collections 都有权访问相关串行化程序来读取其保留的数据。

```csharp
public StatefulBackendService(StatefulServiceContext context)
  : base(context)
  {
    if (!this.StateManager.TryAddStateSerializer(new OrderKeySerializer()))
    {
      throw new InvalidOperationException("Failed to set OrderKey custom serializer");
    }
  }
```

> [!NOTE]
> 自訂序列化程式的優先順序高於內建的序列化程式。 例如，在為 int 註冊自訂序列化程式之後，系統就會使用它將整數序列化，而不會使用 int 的內建序列化程式。

### <a name="how-to-implement-a-custom-serializer"></a>如何實作自訂序列化程式

自訂序列化程式需要實作 [IStateSerializer<T>](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.istateserializer-1) 介面。

> [!NOTE]
> IStateSerializer<T> 包含會接受額外 T (稱為基底值) 的 Write 和 Read 多載。 此 API 適用於差異序列化。 目前並未公開差異序列化功能。 因此，將等到公開並啟用差異序列化之後，才會呼叫這兩個多載。

以下是一個名為 OrderKey 且包含四個屬性的範例自訂類型

```csharp
public class OrderKey : IComparable<OrderKey>, IEquatable<OrderKey>
{
    public byte Warehouse { get; set; }

    public short District { get; set; }

    public int Customer { get; set; }

    public long Order { get; set; }

    #region Object Overrides for GetHashCode, CompareTo and Equals
    #endregion
}
```

以下是 IStateSerializer 範例實作\<OrderKey >。
請注意，接受 baseValue 的 Read 和 Write 多載會呼叫其個別的多載以滿足向後相容性。

```csharp
public class OrderKeySerializer : IStateSerializer<OrderKey>
{
  OrderKey IStateSerializer<OrderKey>.Read(BinaryReader reader)
  {
      var value = new OrderKey();
      value.Warehouse = reader.ReadByte();
      value.District = reader.ReadInt16();
      value.Customer = reader.ReadInt32();
      value.Order = reader.ReadInt64();

      return value;
  }

  void IStateSerializer<OrderKey>.Write(OrderKey value, BinaryWriter writer)
  {
      writer.Write(value.Warehouse);
      writer.Write(value.District);
      writer.Write(value.Customer);
      writer.Write(value.Order);
  }
  
  // Read overload for differential de-serialization
  OrderKey IStateSerializer<OrderKey>.Read(OrderKey baseValue, BinaryReader reader)
  {
      return ((IStateSerializer<OrderKey>)this).Read(reader);
  }

  // Write overload for differential serialization
  void IStateSerializer<OrderKey>.Write(OrderKey baseValue, OrderKey newValue, BinaryWriter writer)
  {
      ((IStateSerializer<OrderKey>)this).Write(newValue, writer);
  }
}
```

## <a name="upgradability"></a>可升級性
在[应用程序滚动升级](service-fabric-application-upgrade.md)过程中，升级应用于部分节点，一次一个升级域。 在此過程中，某些升級網域會比您的應用程式版本新，而某些升級網域會比您的應用程式的版本舊。 在滚动更新期间，新版本的应用程序必须能够读取旧版本的数据，并且旧版本的应用程序必须能够读取新版本的数据。 如果数据格式不向前和向后兼容，则升级可能会失败（或更糟），甚至可能丢失数据。

如果您使用的是內建序列化程式，就不需要擔心相容性。
不過，如果您使用的是自訂序列化程式或 DataContractSerializer，資料就必須無限地往前和往後相容。
換句話說，每個還原序列化版本都必須能夠將該類型的任何版本序列化或還原序列化。

「資料合約」使用者應該依照妥善定義的版本控制規則來新增、移除及變更欄位。 「資料合約」也支援處理未知欄位、連結到序列化和還原序列化程序，以及處理類別繼承。 有关详细信息，请参阅[使用数据协定](https://msdn.microsoft.com/library/ms733127.aspx)。

自訂序列化程式使用者應該遵守所使用序列化程式的準則，以確保能夠往前和往後相容。
支援所有版本的常見方式是在開頭新增大小資訊，並且只新增選用的屬性。
如此一來，每個版本便可儘可能地讀取資料，然後跳過資料流的其餘部分。

## <a name="next-steps"></a>後續步驟
  * [序列化與升級](service-fabric-application-upgrade-data-serialization.md)
  * [Reliable Collections 的开发人员参考](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
  * [使用 Visual Studio 升级应用程序](service-fabric-application-upgrade-tutorial.md)逐步讲解了如何使用 Visual Studio 进行应用程序升级。
  * [使用 PowerShell 升級您的應用程式](service-fabric-application-upgrade-tutorial-powershell.md) 將引導您完成使用 PowerShell 進行應用程式升級的步驟。
  * 使用 [升級參數](service-fabric-application-upgrade-parameters.md)來控制您應用程式的升級方式。
  * 参考[高级主题](service-fabric-application-upgrade-advanced.md)，了解如何在升级应用程序时使用高级功能。
  * 参考 [Troubleshooting Application Upgrades](service-fabric-application-upgrade-troubleshooting.md)（对应用程序升级进行故障排除）中的步骤来解决应用程序升级时的常见问题。
