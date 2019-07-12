---
title: Azure 應用程式供應項目測試磁碟機 |Azure Marketplace
description: 如何在 Azure Marketplace 上設定 Azure 應用程式供應項目的試用產品。
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pabutler
ms.openlocfilehash: 42e533cdcedfb47a46934f77714d61a640a8d7d1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "64942874"
---
# <a name="azure-applications-test-drive-tab"></a>Azure 應用程式試用產品索引標籤

使用 [試用產品] 索引標籤，為您的客戶提供試用體驗。

## <a name="test-drive-benefits"></a>試用產品的權益

為您的客戶建立試用體驗是確定他們能有信心購買的最佳做法。 在可用的試用選項中，試用產品是獲得高品質潛在客戶及提高那些潛在客戶轉換為付費客戶之轉換率最有效的方式。

它可讓客戶以自我引導方式實際操作您試用產品的主要功能與優點，而且這是以真實世界實作案例來示範。

## <a name="how-a-test-drive-works"></a>試用產品如何運作

潛在客戶在 Marketplace 上搜尋並探索您的應用程式。 客戶登入並同意使用規定。 此時，客戶收到您預先設定的環境，並在固定的數小時內試用，而您也得到可進行後續追蹤的最適合潛在客戶。 如需詳細資訊，請參閱[什麼是試用產品？](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

## <a name="setting-up-a-test-drive"></a>設定試用產品

使用下列步驟來啟用和設定試用產品。

### <a name="to-enable-a-test-drive"></a>啟用試用產品：

1. 在 [新增供應項目]  下方，選取 [試用產品]  索引標籤。
2. 在 [試用產品]  下方，針對 [啟用試用產品]  選取 [是]  。

   ![啟用試用產品](./media/managed-app-enable-testdrive.png)

### <a name="to-configure-a-test-drive"></a>設定試用產品：

啟用試用產品之後，您將會填寫下列表單來設定試用產品：
  
 - 詳細資料
 - 技術設定
 - 試用產品部署訂用帳戶詳細資料

下一個螢幕擷取畫面會顯示所有試用產品表單。 欄位名稱上附加的星號 (*) 表示此為必填欄位。 

![設定試用產品](./media/managed-app-configure-testdrive.png)

下表說明為受控應用程式設定試用產品所需的欄位。  需要加上星號的欄位。

|      欄位         |  描述      |
|  ---------------   |  ---------------  |
| **說明\***  |  說明可在試用產品上完成的動作。 您可以使用基本 HTML 標記來設定這個說明的格式。 例如，&lt;p&gt;、&lt;em&gt;、&lt;ul&gt;、&lt;li&gt;、&lt;ol&gt; 及標題。                |
| **使用者手冊\***  |  上傳客戶可用來逐步進行試用產品體驗的使用者手冊。 這份文件必須是 .pdf 檔案。    |
| **試用產品示範影片** |  選擇性的視訊逐步解說您的試用產品。 客戶可以先觀看這段影片，然後再取得試用產品。 提供影片在 YouTube 或 Vimeo 上的 URL。 如果您選取 [+ 新增影片]  ，系統將會提示您提供下列資訊：<ul><li>名稱</li><li>URL</li><li>縮圖 (.png 格式，533 x 324 像素)</li></ul>  |
| **執行個體\***      | 設定所需的執行個體數目、要在哪個區域設定，以及您的客戶多快就能取得試用產品。 如需詳細資訊，請參閱[如何發佈試用產品](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive#how-to-publish-a-test-drive)。           |
| **測試磁碟機的持續時間 （小時）\*** | 輸入整數的時數。 允許的範圍是從 1 到 999。 |
| **測試磁碟機的 ARM 範本\***     | 上傳含有適用於您應用程式之 Azure Resource Manager 範本的壓縮 (.zip) 檔案。 如需詳細資訊，請參閱 [Azure Resource Manager 試用產品](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)。 |
| **存取資訊\***          | 在您的客戶取得試用產品之後，提供存取資訊。 例如，用來存取試用產品的 URL 和簽署資訊。 。 您可以使用基本 HTML 標記來設定這個說明的格式。 例如，&lt;p&gt;、&lt;em&gt;、&lt;ul&gt;、&lt;li&gt;、&lt;ol&gt; 及標題。 |
| **Azure 訂用帳戶識別碼\***       | 這會授與對 Azure 服務與 Azure 入口網站的存取權。 訂用帳戶是回報資源使用狀況並針對服務計費的位置。 如果您還沒有將僅用於試用產品的獨立 Azure 訂用帳戶，請建立一個訂用帳戶。  |
| **Azure AD 租用戶識別碼\***          | 提供 Azure Active Directory 中現有的租用戶，或建立適用於這個試用產品的租用戶。  |
| **Azure AD App Id\***             | 建立並註冊新的應用程式。 Microsoft 會使用此應用程式，在您的試用產品執行個體上執行作業。  |
| **Azure AD 應用程式金鑰\***            | 建立應用程式的驗證金鑰，並將它貼到這個欄位。   |
|  |  |

提供所有必要資訊之後，請選取 [儲存]  以完成設定試用產品。


## <a name="next-steps"></a>後續步驟

[Marketplace 索引標籤](./cpp-marketplace-tab.md)
