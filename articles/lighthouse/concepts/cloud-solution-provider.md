---
title: 雲端解決方案提供者方案考量
description: 在使用 Azure 委派的資源管理時，請務必考慮安全性與存取控制。
author: JnHs
ms.service: lighthouse
ms.author: jenhayes
ms.date: 08/22/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: 76df2942b176fd7d0e785213e44655ffd11da410
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286214"
---
# <a name="azure-lighthouse-and-the-cloud-solution-provider-program"></a>Azure Lighthouse 與雲端解決方案提供者計畫

如果您是 [CSP (雲端解決方案提供者)](https://docs.microsoft.com/partner-center/csp-overview) 合作夥伴，則您可能已使用[管理代表 (AOBO)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) 功能，存取透過 CSP 計畫為您的客戶建立的 Azure 訂用帳戶。 此存取權可讓您直接支援、設定及管理客戶的訂用帳戶。

透過 Azure Lighthouse，您將可搭配使用 Azure 委派的資源管理與 AOBO。 這有助於您為使用者啟用更精細的權限來改善安全性，以及減少不必要的存取。 此外，效率和延展性也得以提高，因為使用者可在您的租用戶中使用單一登入跨多個客戶訂用帳戶工作。

## <a name="administer-on-behalf-of-aobo"></a>管理代表 (AOBO)

若使用 AOBO，則您租用戶中有[系統管理代理人](https://docs.microsoft.com/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles)角色的任何使用者，都能使用 AOBO 來存取您透過 CSP 計畫建立的 Azure 訂用帳戶。 需要存取任何客戶訂用帳戶的任何使用者，都必須是此群組的成員。 AOBO 不允許建立搭配不同客戶來使用的不同群組，或為群組或使用者建立不同角色的彈性設定。

![使用 AOBO 進行租用戶管理](../media/csp-1.jpg)

## <a name="azure-delegated-resource-management"></a>Azure 委派的資源管理

若使用 Azure 委派的資源管理，您可以將不同群組指派至不同客戶或角色，如下列圖表所示。 因為使用 Azure 委派的資源管理，使用者會有適當層級的存取權，所以您可減少有「系統管理代理人」角色 (因此有完整 AOBO 存取權) 的使用者數目。 這有助於限制對客戶資源的非必要存取，進而提高安全性。 此外，這也讓您能更有彈性地大規模管理多個客戶。

依照[將訂用帳戶上線至 Azure 委派的資源管理](../how-to/onboard-customer.md) \(英文\) 中所描述的步驟，將您透過 CSP 計畫所建立的訂用帳戶上線。 您租用戶中有「系統管理代理人」角色的任何使用者都能執行此上線作業。

![使用 AOBO 和 Azure 委派的資源管理進行租用戶管理](../media/csp-2.jpg)

## <a name="next-steps"></a>後續步驟

- 了解[跨租用戶管理體驗](cross-tenant-management-experience.md)。
- 了解如何[讓訂用帳戶在 Azure 委派的資源管理中上線](../how-to/onboard-customer.md)。
- 了解[雲端解決方案提供者計畫](https://docs.microsoft.com/partner-center/csp-overview)。
