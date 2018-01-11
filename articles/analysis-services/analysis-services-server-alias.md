---
title: "Azure Analysis Services 伺服器別名 |Microsoft 文件"
description: "描述如何建立及使用伺服器名稱的別名。"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
ms.assetid: 
ms.service: analysis-services
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: owend
ms.openlocfilehash: a947dde1551c653faa54f088c1712c41c7657aa0
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2017
---
# <a name="alias-server-names"></a>伺服器別名

藉由使用伺服器名稱別名，使用者可以連線到您的 Azure Analysis Services 伺服器與短*別名*而不是伺服器名稱。 連接時從用戶端應用程式，指定別名做為端點使用**連結: / /**通訊協定格式。 然後，端點會傳回實際的伺服器名稱才能連接。

伺服器別名適合用於：

- 移轉而不會影響使用者的伺服器之間的模型。 
- 好記的伺服器名稱是方便使用者記憶。 
- 將使用者導向至不同的伺服器，在每天不同時間。 
- 將使用者導向到地理上更靠近，例如當使用 Azure Traffic Manager 的執行個體的不同區域中。 

任何 HTTP 端點，會傳回有效的 Azure Analysis Services 伺服器名稱可做為別名。

![使用連結格式的別名](media/analysis-services-alias/aas-alias-browser.png)

當從用戶端連線，別名輸入伺服器名稱是使用**連結: / /**通訊協定格式。 例如，在 Power BI Desktop:

![Power BI Desktop 連接](media/analysis-services-alias/aas-alias-connect-pbid.png)

## <a name="create-an-alias"></a>建立別名

若要建立別名端點，您可以使用任何方法會傳回有效的 Azure Analysis Services 伺服器名稱。 例如，包含實際伺服器的 Azure Blob 儲存體檔案的參考名稱，或建立並發佈 ASP.NET Web Form 應用程式。

在此範例中，Visual Studio 中建立 ASP.NET Web Form 應用程式。 主版頁面的參考和使用者控制項移除了 Default.aspx 頁面。 Default.aspx 的內容是只是下列頁面指示詞：

```
<%@ Page Title="Home Page" Language="C#" AutoEventWireup="true" CodeBehind="Default.aspx.cs" Inherits="FriendlyRedirect._Default" %>
```

在 Default.aspx.cs Page_Load 事件使用 Response.write 方法來傳回 Azure Analysis Services 伺服器名稱。

```
protected void Page_Load(object sender, EventArgs e)
{
    this.Response.Write("asazure://<region>.asazure.windows.net/<servername>");
}
```

## <a name="see-also"></a>請參閱

[用戶端程式庫](analysis-services-data-providers.md)   
[從 Power BI Desktop 連接](analysis-services-connect-pbi.md)
