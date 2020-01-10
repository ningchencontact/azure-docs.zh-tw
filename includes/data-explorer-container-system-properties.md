---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 01/08/2020
ms.author: orspodek
ms.openlocfilehash: f9788e4623ce60ad55d79558d1d77a17eb2a9f26
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/09/2020
ms.locfileid: "75779940"
---
### <a name="event-system-properties-mapping"></a>事件系統屬性對應

如果您在上表的 [**資料來源**] 區段中選取 [**事件系統屬性**]，請移至[Web UI](https://dataexplorer.azure.com/)以執行相關的 KQL 命令，以建立適當的對應。

   **針對 csv 對應：**

    ```kusto
    .create table MyTable ingestion csv mapping "CsvMapping1"
    '['
    '   { "column" : "messageid", "DataType":"string", "Properties":{"Ordinal":"0"}},'
    '   { "column" : "userid", "DataType":"string", "Properties":{"Ordinal":"1"}},'
    '   { "column" : "other", "DataType":"int", "Properties":{"Ordinal":"2"}}'
    ']'
    ```
 
   **針對 json 對應：**

    ```kusto
    .create table MyTable ingestion json mapping "JsonMapping1"
    '['
    '    { "column" : "messageid", "datatype" : "string", "Properties":{"Path":"$.message-id"}},'
    '    { "column" : "userid", "Properties":{"Path":"$.user-id"}},'
    '    { "column" : "other", "Properties":{"Path":"$.other"}}'
    ']'
    ```

   > [!TIP]
   > * 您必須在對應中包含所有選取的屬性。 
   > * 在 csv 對應中，屬性的順序很重要。 系統屬性必須列在所有其他屬性之前，以及它們出現在 [**事件系統屬性**] 下拉式清單中的相同順序。