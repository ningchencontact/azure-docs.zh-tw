---
title: 在 Azure 流量管理員中管理端點 | Microsoft Docs
description: 本文將協助您從 Azure 流量管理員加入、移除、啟用和停用端點。
services: traffic-manager
documentationcenter: ''
author: KumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/08/2017
ms.author: kumud
ms.openlocfilehash: 0832010707fc9b5d5f435aac29940db6905d18d7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/19/2019
ms.locfileid: "58009296"
---
# <a name="add-disable-enable-or-delete-endpoints"></a>新增、停用、啟用或刪除端點

不論網站模式為何，Azure App Service 中的 Web Apps 功能已為資料中心內的網站提供容錯移轉和循環配置資源流量路由功能。 Azure 流量管理員可讓您在不同的資料中心的網站和雲端服務中指定容錯移轉和循環配置資源流量路由。 提供該項功能的首要步驟是將雲端服務或網站端點新增至流量管理員。

您也可以停用屬於流量管理員設定檔一部分的個別端點。 停用端點會將端點做為設定檔的一部分保留，但是設定檔會以好像未包含端點的方式運作。 此動作對於暫時移除處於維護模式、或被重新部署的端點而言很有用。 在重新啟動並執行端點之後，您可以將它啟用。

> [!NOTE]
> 停用端點會與其在 Azure 中的部署狀態無關。 健全的端點將會持續運作，並且即使在流量管理員中停用該服務時仍然可以接收流量。 此外，在一個設定檔中停用端點並不會影響它在另一個設定檔中的狀態。

## <a name="to-add-a-cloud-service-or-an-app-service-endpoint-to-a-traffic-manager-profile"></a>若要將雲端服務或應用程式服務端點新增至流量管理員設定檔：

1. 從瀏覽器登入 [Azure 入口網站](https://portal.azure.com)。
2. 在入口網站的搜尋列中，搜尋您想要修改的**流量管理員設定檔**名稱，然後按一下結果中顯示的流量管理員設定檔。
3. 在 [流量管理員設定檔] 刀鋒視窗中，請在 [設定] 區段中按一下 [端點]。
4. 在顯示的 [端點] 刀鋒視窗中，按一下 [新增]。
5. 在 [新增端點] 刀鋒視窗中，如下所示操作︰
    1. 在 [類型] 中，按一下 [Azure 端點]。
    2. 提供要用于识别此终结点的**名称**。
    3. 對於 [目標資源類型]，從下拉式清單中選擇適當的資源類型。
    4. 對於 [目標資源]，按一下 [選擇...] 以在 [資源] 刀鋒視窗中列出相同訂用帳戶下的資源。 在顯示的 [資源] 刀鋒視窗中，挑選您想要新增為第一個端點的服務。
    5. 对于**优先级**，选为 **1**。 這會使得所有流量傳送至此端點 (如果狀況良好)。
    6. 維持不勾選 [新增為已停用]。
    7. 按一下 [確定] 
6.  重複步驟 4 和 5，新增下一個 Azure 端點。 新增它時務必將 [優先順序] 值設為 [2]。
7.  這兩個端點新增完畢後，它們會顯示在 [流量管理員設定檔] 刀鋒視窗中，而且監視狀態是 [線上]。

> [!NOTE]
> 在使用容錯移轉流量路由方法從設定檔新增或移除端點後，容錯移轉優先權清單可能無法依您想要的方式排序。 您可以在 [組態] 頁面上調整容錯移轉優先順序清單的順序。 如需詳細資訊，請參閱 [設定容錯移轉流量路由](traffic-manager-configure-failover-routing-method.md)。

## <a name="to-disable-an-endpoint"></a>若要停用端點

1. 從瀏覽器登入 [Azure 入口網站](https://portal.azure.com)。
2. 在入口網站的搜尋列中，搜尋您想要修改的**流量管理員設定檔**名稱，然後按一下結果中顯示的流量管理員設定檔。
3. 在 [流量管理員設定檔] 刀鋒視窗中，請在 [設定] 區段中按一下 [端點]。 
4. 按一下您要停用的端點，然後在顯示的 [端點] 刀鋒視窗上，按一下 [編輯]。
5. 在 [端點] 刀鋒視窗中，將端點狀態變更為 [已停用]，然後按一下 [儲存]。
6. 用戶端會繼續將流量傳送至端點以取得存留時間 (TTL) 持續時間。 您可以在流量管理員設定檔的 [組態] 頁面上變更 TTL。

## <a name="to-enable-an-endpoint"></a>若要啟用端點

1. 從瀏覽器登入 [Azure 入口網站](https://portal.azure.com)。
2. 在入口網站的搜尋列中，搜尋您想要修改的**流量管理員設定檔**名稱，然後按一下結果中顯示的流量管理員設定檔。
3. 在 [流量管理員設定檔] 刀鋒視窗中，請在 [設定] 區段中按一下 [端點]。 
4. 单击要禁用的终结点，并在显示的“终结点”边栏选项卡中，单击“编辑”。
5. 在 [端點] 刀鋒視窗中，將端點狀態變更為 [已啟用]，然後按一下 [儲存]。
6. 用戶端會繼續將流量傳送至端點以取得存留時間 (TTL) 持續時間。 可以在流量管理器配置文件的“配置”页上更改 TTL。

## <a name="to-delete-an-endpoint"></a>若要刪除端點：

1. 從瀏覽器登入 [Azure 入口網站](https://portal.azure.com)。
2. 在入口網站的搜尋列中，搜尋您想要修改的**流量管理員設定檔**名稱，然後按一下結果中顯示的流量管理員設定檔。
3. 在 [流量管理員設定檔] 刀鋒視窗中，請在 [設定] 區段中按一下 [端點]。 
4. 单击要禁用的终结点，并在显示的“终结点”边栏选项卡中，单击“编辑”。
5. 在 [端點] 刀鋒視窗中，將端點狀態變更為 [已啟用]，然後按一下 [儲存]。


## <a name="next-steps"></a>後續步驟

* [管理流量管理員設定檔](traffic-manager-manage-profiles.md)
* [設定路由方法](traffic-manager-configure-routing-method.md)
* [疑難排解流量管理員的已降級狀態](traffic-manager-troubleshooting-degraded.md)
* [流量管理員的效能考量](traffic-manager-performance-considerations.md)
* [流量管理員的相關作業 (REST API 參考)](https://go.microsoft.com/fwlink/p/?LinkID=313584)

