---
title: Azure AD 權利管理（預覽）中的常見案例-Azure Active Directory
description: 瞭解 Azure Active Directory 權利管理（預覽）中常見案例應遵循的高階步驟。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/23/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81382ebecdff6c7b146386b3ae2b0768a7c834bf
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389074"
---
# <a name="common-scenarios-in-azure-ad-entitlement-management-preview"></a>Azure AD 權利管理（預覽）中的常見案例

> [!IMPORTANT]
> Azure Active Directory (Azure AD) 權利管理目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

有數種方式可讓您為組織設定權利管理。 不過，如果您只是開始使用，瞭解系統管理員、核准者和要求者的常見案例會很有説明。

## <a name="administrators"></a>系統管理員

### <a name="im-new-to-entitlement-management-and-i-want-help-with-getting-started"></a>我是權利管理的新手，我想要開始使用的協助

> [!div class="mx-tableFixed"]
> | 步驟 | 範例 |
> | --- | --- |
> | [遵循教學課程來建立您的第一個存取套件](entitlement-management-access-package-first.md) | [@no__t 1Azure 入口網站圖示](./media/entitlement-management-scenarios/azure-portal.png)](./media/entitlement-management-scenarios/azure-portal-expanded.png#lightbox) |

### <a name="i-want-to-allow-users-in-my-directory-to-request-access-to-groups-applications-or-sharepoint-sites"></a>我想要允許目錄中的使用者要求對群組、應用程式或 SharePoint 網站的存取權

> [!div class="mx-tableFixed"]
> | 步驟 | 範例 |
> | --- | --- |
> | **1.** [在目錄中建立新的存取封裝](entitlement-management-access-package-create.md#start-new-access-package) | ![建立存取套件](./media/entitlement-management-scenarios/access-package.png) |
> | **2.** [新增資源角色以存取封裝](entitlement-management-access-package-resources.md#add-resource-roles)<ul><li>群組</li><li>應用程式</li><li>SharePoint 網站</li></ul> | ![新增資源角色](./media/entitlement-management-scenarios/resource-roles.png) |
> | **3.** [新增原則](entitlement-management-access-package-request-policy.md#for-users-in-your-directory)<ul><li>適用于您目錄中的使用者</li><li>需要核准</li><li>生命週期設定</li></ul> | ![Add policy](./media/entitlement-management-scenarios/policy.png) |

### <a name="i-want-to-allow-users-from-my-business-partners-directory-including-users-not-yet-in-my-directory-to-request-access-to-groups-applications-or-sharepoint-sites"></a>我想要允許來自我的商務夥伴目錄的使用者（包括尚未在我的目錄中的使用者）要求存取群組、應用程式或 SharePoint 網站

> [!div class="mx-tableFixed"]
> | 步驟 | 範例 |
> | --- | --- |
> | **1.** [在目錄中建立新的存取封裝](entitlement-management-access-package-create.md#start-new-access-package) | ![建立存取套件](./media/entitlement-management-scenarios/access-package.png) |
> | **2.** [新增資源角色以存取封裝](entitlement-management-access-package-resources.md#add-resource-roles) | ![新增資源角色](./media/entitlement-management-scenarios/resource-roles.png) |
> | **3.** [為外部使用者新增原則](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)<ul><li>針對不在您目錄中的使用者</li><li>需要核准</li><li>生命週期設定</li></ul> | ![新增外部使用者的原則](./media/entitlement-management-scenarios/policy-external.png) |
> | **4.** [傳送連結以向您的商業夥伴要求存取套件](entitlement-management-access-package-settings.md)<ul><li>商務合作夥伴可以與使用者共用連結</li></ul> |  |

### <a name="i-want-to-change-the-groups-applications-or-sharepoint-sites-in-an-access-package"></a>我想要變更存取套件中的群組、應用程式或 SharePoint 網站

> [!div class="mx-tableFixed"]
> | 步驟 | 範例 |
> | --- | --- |
> | **1.** 開啟存取封裝 | ![新增資源角色](./media/entitlement-management-scenarios/resource-roles.png) |
> | **2.** [新增或移除資源角色](entitlement-management-access-package-resources.md#add-resource-roles) | ![新增資源角色](./media/entitlement-management-scenarios/resource-roles-add.png) |

### <a name="i-want-to-view-who-has-an-assignment-to-groups-applications-or-sharepoint-sites"></a>我想要查看誰有指派給群組、應用程式或 SharePoint 網站

> [!div class="mx-tableFixed"]
> | 步驟 | 範例 |
> | --- | --- |
> | **1.** 開啟存取套件 | ![新增資源角色](./media/entitlement-management-scenarios/resource-roles.png) |
> | **2.** [視圖指派](entitlement-management-access-package-assignments.md#view-who-has-an-assignment)<ul><li>查看哪些使用者可以存取存取套件</li><li>查看哪些使用者的存取權已過期</li></ul> |  |

### <a name="i-want-to-view-groups-applications-or-sharepoint-sites-a-user-has-access-to"></a>我想要查看使用者可存取的群組、應用程式或 SharePoint 網站

> [!div class="mx-tableFixed"]
> | 步驟 | 範例 |
> | --- | --- |
> | [查看使用者指派報表](entitlement-management-reports.md)<ul><li>查看要求的時間和核准者</li></ul> |  |

## <a name="approvers"></a>核准者

### <a name="i-want-to-approve-requests-to-access-groups-applications-or-sharepoint-sites"></a>我想要核准存取群組、應用程式或 SharePoint 網站的要求

> [!div class="mx-tableFixed"]
> | 步驟 | 範例 |
> | --- | --- |
> | **1.** [在我的存取權入口網站中開啟要求](entitlement-management-request-approve.md#open-request) | [@no__t 1My 存取入口網站圖示](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** [核准存取要求](entitlement-management-request-approve.md#approve-or-deny-request) | ![核准存取](./media/entitlement-management-scenarios/approve-access.png) |

## <a name="requestors"></a>要求者

### <a name="i-want-to-view-the-groups-applications-or-sharepoint-sites-available-to-me-and-request-access"></a>我想要查看可供我使用的群組、應用程式或 SharePoint 網站，並要求存取權

> [!div class="mx-tableFixed"]
> | 步驟 | 範例 |
> | --- | --- |
> | **1.** 登[入我的存取權入口網站](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [@no__t 1My 存取入口網站圖示](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** 尋找存取套件 |  |
> | **3.** [要求存取權](entitlement-management-request-access.md#request-an-access-package) | ![要求存取](./media/entitlement-management-scenarios/request-access.png) |

### <a name="im-an-external-user-and-i-want-to-request-access-to-groups-applications-or-sharepoint-sites-with-a-direct-link"></a>我是外部使用者，我想要透過直接連結要求存取群組、應用程式或 SharePoint 網站

> [!div class="mx-tableFixed"]
> | 步驟 | 範例 |
> | --- | --- |
> | **1.** [尋找您收到的存取套件連結](entitlement-management-access-package-settings.md) |  |
> | **2.** 登[入我的存取權入口網站](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [@no__t 1My 存取入口網站圖示](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **3.** [要求存取權](entitlement-management-request-access.md#request-an-access-package) | ![要求存取外部使用者](./media/entitlement-management-scenarios/request-access-external.png) |

### <a name="i-want-to-view-the-groups-applications-or-sharepoint-sites-i-already-have-access-to"></a>我想要查看我已經有存取權的群組、應用程式或 SharePoint 網站

> [!div class="mx-tableFixed"]
> | 步驟 | 範例 |
> | --- | --- |
> | **1.** 登[入我的存取權入口網站](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [@no__t 1My 存取入口網站圖示](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** 查看 active access 套件 |  |

## <a name="next-steps"></a>後續步驟

- [教學課程：建立您的第一個存取套件](entitlement-management-access-package-first.md)
- [委派和角色](entitlement-management-delegate.md)
