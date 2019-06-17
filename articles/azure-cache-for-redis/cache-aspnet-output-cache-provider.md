---
title: 適用於 Azure Redis 快取的 ASP.NET 輸出快取提供者
description: 了解如何使用「Azure Redis 快取」來快取 ASP.NET 頁面輸出
services: cache
documentationcenter: na
author: yegu-ms
manager: jhubbard
editor: tysonn
ms.assetid: 78469a66-0829-484f-8660-b2598ec60fbf
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache
ms.workload: tbd
ms.date: 04/22/2018
ms.author: yegu
ms.openlocfilehash: a93d21b07dc486f743694ee99f60018ed4ef517c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "64943863"
---
# <a name="aspnet-output-cache-provider-for-azure-cache-for-redis"></a>適用於 Azure Redis 快取的 ASP.NET 輸出快取提供者

Redis 輸出快取提供者為輸出快取資料的程序外儲存體機制。 此資料特別適用於完整 HTTP 回應 (頁面輸出快取)。 提供者插入 ASP.NET 4 中導入的新輸出快取提供者擴充點。

若要使用 Redis 輸出快取提供者，請先設定您的快取，然後使用「Redis 輸出快取提供者 NuGet 封裝」設定 ASP.NET 應用程式。 本主題提供為使用 Redis 輸出快取提供者而進行應用程式設定的相關指引。 如需有關建立及設定「Azure Redis 快取」執行個體的詳細資訊，請參閱[建立快取](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache)。

## <a name="store-aspnet-page-output-in-the-cache"></a>將 ASP.NET 頁面輸出儲存在快取中

若要在 Visual Studio 中使用「Azure Redis 快取工作階段狀態」NuGet 套件來設定用戶端應用程式，請從 [工具]  功能表中，依序按一下 [NuGet 套件管理員]  和 [套件管理器主控台]  。

從 `Package Manager Console` 視窗執行下列命令。

```
Install-Package Microsoft.Web.RedisOutputCacheProvider
```

「Redis 輸出快取提供者 NuGet 封裝」對「StackExchange.Redis.StrongName 封裝」有相依性。 如果 StackExchange.Redis.StrongName 封裝不在專案中，代表已經安裝。 如需「Redis 輸出快取提供者 NuGet 套件」的詳細資訊，請參閱 [RedisOutputCacheProvider (英文)](https://www.nuget.org/packages/Microsoft.Web.RedisOutputCacheProvider/) NuGet 頁面。

>[!NOTE]
>除了強式名稱的 StackExchange.Redis.StrongName 封裝外，另外還有非強式名稱版本的 StackExchange.Redis。 如果您的專案使用非強式名稱的 StackExchange.Redis 版本，您必須解除安裝;否則，則會發生命名衝突，專案中。 如需這些封裝的相關詳細資訊，請參閱 [設定 .NET 快取用戶端](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients)。

NuGet 封裝會下載和加入必要的組件參考，並將下列區段加入您的 web.config 檔案。 此區段包含讓您的 ASP.NET 應用程式使用 Redis 輸出快取提供者所需的設定。

```xml
<caching>
  <outputCache defaultProvider="MyRedisOutputCache">
    <providers>
      <add name="MyRedisOutputCache" type="Microsoft.Web.Redis.RedisOutputCacheProvider"
           host=""
           accessKey=""
           ssl="true" />
    </providers>
  </outputCache>
</caching>
```

以來自 Microsoft Azure 入口網站之快取刀鋒視窗的值來設定屬性，並視需要設定其他值。 如需關於存取快取屬性的指示，請參閱[設定 Azure Cache for Redis 設定](cache-configure.md#configure-azure-cache-for-redis-settings)。

| 屬性 | 類型 | 預設值 | 描述 |
| --------- | ---- | ------- | ----------- |
| *主機* | 字串 | "localhost" | Redis 伺服器 IP 位址或主機名稱 |
| *port* | 正整數 | 6379 (non-SSL)<br/>6380 (SSL) | Redis 伺服器連接埠 |
| *accessKey* | 字串 | "" | 已啟用 Redis 授權時，redis 伺服器密碼。 值為空字串，根據預設，這表示工作階段狀態提供者在連接到 Redis 伺服器時，不會使用任何密碼。 **您的 Redis 伺服器是否可公開存取的網路，例如 Azure Redis 快取中，務必啟用 Redis 授權，以改善安全性，並提供安全的密碼。** |
| *ssl* | boolean | **false** | 是否要透過 SSL 的 Redis 伺服器連接。 這個值是**false**預設因為 Redis 不支援現成的 SSL。 **如果您使用 Azure Redis 快取支援 SSL 內建的請務必將此設為 true，以改善安全性。**<br/><br/>預設會為新快取停用非 SSL 連接埠。 指定 **，則為 true**這項設定使用 SSL 連接埠。 如需啟用非 SSL 連接埠的相關詳細資訊，請參閱[設定快取](cache-configure.md)主題中的[存取連接埠](cache-configure.md#access-ports)一節。 |
| *databaseIdNumber* | 正整數 | 0 | *這個屬性可以指定僅透過 web.config 或 AppSettings。*<br/><br/>指定要使用哪個 Redis 資料庫。 |
| *connectionTimeoutInMilliseconds* | 正整數 | 由 StackExchange.Redis | 用來設定*ConnectTimeout*建立 StackExchange.Redis.ConnectionMultiplexer 時。 |
| *operationTimeoutInMilliseconds* | 正整數 | 由 StackExchange.Redis | 用來設定*SyncTimeout*建立 StackExchange.Redis.ConnectionMultiplexer 時。 |
| *connectionString* （有效的 StackExchange.Redis 連接字串） | 字串 | *n/a* | 任一參數參考，以便 AppSettings 或 web.config 中，或是有效的 StackExchange.Redis 連接字串。 這個屬性可以提供的值*主機*，*連接埠*， *accessKey*， *ssl*，和其他 StackExchange.Redis 屬性。 得更仔細看看*connectionString*，請參閱[設定 connectionString](#setting-connectionstring)中[屬性備忘稿](#attribute-notes)一節。 |
| *settingsClassName*<br/>*settingsMethodName* | 字串<br/>字串 | *n/a* | *這些屬性可以指定僅透過 web.config 或 AppSettings。*<br/><br/>您可以使用這些屬性來提供連接字串。 *settingsClassName*應該是組件限定的類別名稱，其中包含所指定的方法*settingsMethodName*。<br/><br/>所指定的方法*settingsMethodName*應該是公用、 靜態的且虛值 （未採用任何參數），以傳回型別**字串**。 這個方法會傳回實際的連接字串。 |
| *loggingClassName*<br/>*loggingMethodName* | 字串<br/>字串 | *n/a* | *這些屬性可以指定僅透過 web.config 或 AppSettings。*<br/><br/>藉由提供來自 StackExchange.Redis 的工作階段狀態/輸出快取的記錄檔，以及記錄檔偵錯應用程式中使用這些屬性。 *loggingClassName*應該是組件限定的類別名稱，其中包含所指定的方法*loggingMethodName*。<br/><br/>所指定的方法*loggingMethodName*應該是公用、 靜態的且虛值 （未採用任何參數），以傳回型別**System.IO.TextWriter**。 |
| *applicationName* | 字串 | 目前的處理序的模組名稱或"/" | *只有 SessionStateProvider*<br/>*這個屬性可以指定僅透過 web.config 或 AppSettings。*<br/><br/>使用 Redis 快取中的應用程式名稱前置詞。 客戶可能會針對不同用途使用相同的 Redis 快取。 為了確保工作階段金鑰，不會衝突，它可以在前面加上應用程式名稱。 |
| *throwOnError* | boolean | true | *只有 SessionStateProvider*<br/>*這個屬性可以指定僅透過 web.config 或 AppSettings。*<br/><br/>是否發生錯誤時擲回例外狀況。<br/><br/>如需詳細資訊*throwOnError*，請參閱[注意事項*throwOnError* ](#notes-on-throwonerror)中[屬性備忘稿](#attribute-notes)一節。 |>*Microsoft.Web.Redis.RedisSessionStateProvider.LastException*. |
| *retryTimeoutInMilliseconds* | 正整數 | 5000 | *只有 SessionStateProvider*<br/>*這個屬性可以指定僅透過 web.config 或 AppSettings。*<br/><br/>重試作業失敗時的時間長度。 如果這個值是小於*operationTimeoutInMilliseconds*，提供者將不會重試。<br/><br/>如需詳細資訊*retryTimeoutInMilliseconds*，請參閱[注意事項*retryTimeoutInMilliseconds* ](#notes-on-retrytimeoutinmilliseconds)中[屬性備忘稿](#attribute-notes)一節。 |
| *redisSerializerType* | 字串 | *n/a* | 指定可實作 Microsoft.Web.Redis 類別的組件限定的類型名稱。 ISerializer，包含序列化和還原序列化值的自訂邏輯。 如需詳細資訊，請參閱 <<c0> [ 關於*redisSerializerType* ](#about-redisserializertype)中[屬性備忘稿](#attribute-notes)一節。 |
|

## <a name="attribute-notes"></a>屬性附註

### <a name="setting-connectionstring"></a>設定*connectionString*

值*connectionString*當做索引鍵從 AppSettings，擷取實際的連接字串如果 AppSettings 中有這類的字串。 如果在 AppSettings 值找不到*connectionString*將使用做為索引鍵，以從 web.config 擷取實際的連接字串**ConnectionString**區段中，如果該區段存在。 如果連接字串不存在於 web.config 中 AppSettings **ConnectionString**一節的常值*connectionString*建立時使用的連接字串StackExchange.Redis.ConnectionMultiplexer。

下列範例說明如何*connectionString*用。

#### <a name="example-1"></a>範例 1

```xml
<connectionStrings>
    <add name="MyRedisConnectionString" connectionString="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</connectionStrings>
```

在  `web.config`，上述索引鍵做為參數值，而不是實際的值。

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "MyRedisConnectionString"/>
    </providers>
</sessionState>
```

#### <a name="example-2"></a>範例 2

```xml
<appSettings>
    <add key="MyRedisConnectionString" value="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</appSettings>
```

在  `web.config`，上述索引鍵做為參數值，而不是實際的值。

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "MyRedisConnectionString"/>
    </providers>
</sessionState>
```

#### <a name="example-3"></a>範例 3

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False"/>
    </providers>
</sessionState>
```

### <a name="notes-on-throwonerror"></a>注意事項*throwOnError*

目前，如果工作階段作業期間發生錯誤，工作階段狀態提供者會擲回例外狀況。 這會關閉應用程式。

具有已修改此行為，以支援現有的 ASP.NET 工作階段狀態提供者使用者的期望，同時也能夠處理的例外狀況，如有需要的方式。 預設行為仍然會擲回例外狀況發生錯誤時，配合其他 ASP.NET 工作階段狀態提供者;現有的程式碼應該運作之前一樣。

如果您設定*throwOnError*要**false**，而不是發生錯誤時，擲回例外狀況，它就會失敗以無訊息模式。 若要查看是否有錯誤，若是如此，探索例外狀況是什麼，請檢查靜態屬性*Microsoft.Web.Redis.RedisSessionStateProvider.LastException*。

### <a name="notes-on-retrytimeoutinmilliseconds"></a>注意事項*retryTimeoutInMilliseconds*

這會提供一些重試邏輯，以簡化的案例，其中某些工作階段作業應重試失敗之類的網路問題，因為還可讓您控制重試逾時，或選擇完全退出重試。

如果您設定*retryTimeoutInMilliseconds*轉換為數字，例如 2000 中，然後當工作階段作業失敗時，會重試為 2000年毫秒之前將其視為錯誤。 因此若要讓工作階段狀態提供者，要套用此重試邏輯，只要設定逾時。 第一個重試之後會發生 20 毫秒，網路問題發生時即足以應付大部分的情況。 在那之後，它會重試每隔一秒，直到逾時。之後逾時，它會重試一次，請確定，它將不會遭到截斷的逾時 （最多） 一秒。

如果您認為您需要重試 （例如，當您在與您的應用程式相同的電腦上執行的 Redis 伺服器），或如果您想要自行處理重試邏輯設定*retryTimeoutInMilliseconds*設為 0。

### <a name="about-redisserializertype"></a>About *redisSerializerType*

根據預設，將值儲存在 Redis 上序列化是由以二進位格式**BinaryFormatter**類別。 使用*redisSerializerType*若要指定實作的類別的組件限定的類型名稱**Microsoft.Web.Redis.ISerializer**和已序列化和還原序列化值的自訂邏輯。 例如，以下是使用 JSON.NET 的 Json 序列化程式類別：

```cs
namespace MyCompany.Redis
{
    public class JsonSerializer : ISerializer
    {
        private static JsonSerializerSettings _settings = new JsonSerializerSettings() { TypeNameHandling = TypeNameHandling.All };

        public byte[] Serialize(object data)
        {
            return Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(data, _settings));
        }

        public object Deserialize(byte[] data)
        {
            if (data == null)6t6
            {
                return null;
            }
            return JsonConvert.DeserializeObject(Encoding.UTF8.GetString(data), _settings);
        }
    }
}
```

假設此類別名稱的組件中定義**MyCompanyDll**，您可以設定參數*redisSerializerType*使用它：

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvider"
             name = "MySessionStateStore"
             redisSerializerType = "MyCompany.Redis.JsonSerializer,MyCompanyDll"
             ... />
    </providers>
</sessionState>
```

## <a name="output-cache-directive"></a>輸出快取指示詞

將 OutputCache 指示詞新增至每一個您要快取輸出的頁面。

```xml
<%@ OutputCache Duration="60" VaryByParam="*" %>
```

在上一個範例中，已快取的頁面資料會留在快取中 60 秒，而且會對每一個參數組合快取不同版本的頁面。 如需 OutputCache 指示詞的相關詳細資訊，請參閱 [@OutputCache](https://go.microsoft.com/fwlink/?linkid=320837)。

一旦執行這些步驟，您的應用程式將設定為使用 Redis 輸出快取提供者。

## <a name="next-steps"></a>後續步驟

請參閱[適用於 Azure Redis 快取的 ASP.NET 工作階段狀態提供者](cache-aspnet-session-state-provider.md)。
