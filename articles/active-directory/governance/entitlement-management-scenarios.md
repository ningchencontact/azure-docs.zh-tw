---
title: Azure AD 權限管理 （預覽）-Azure Active Directory 中的常見案例
description: 了解 Azure Active Directory 權限管理 （預覽） 中的常見案例所應遵循的高層級步驟。
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/23/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 27d54d51a16f4ed94f24f9afa005c63b22778cf9
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190357"
---
# <a name="common-scenarios-in-azure-ad-entitlement-management-preview"></a>Azure AD 權限管理 （預覽） 中的常見案例

> [!IMPORTANT]
> Azure Active Directory (Azure AD) 權利管理目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

有數種方式，您可以為您的組織設定權限管理。 不過，如果您剛開始使用，最好先了解系統管理員、 核准者，以及要求者的常見案例。

## <a name="administrators"></a>系統管理員

### <a name="im-new-to-entitlement-management-and-i-want-help-with-getting-started"></a>我剛接觸權利管理，而且希望開始說明

> [!div class="mx-tableFixed"]
> | 步驟 | 範例 |
> | --- | --- |
> | [遵循教學課程建立您第一次存取套件](entitlement-management-access-package-first.md) | [![Azure 入口網站的圖示](./media/entitlement-management-scenarios/azure-portal.png)](./media/entitlement-management-scenarios/azure-portal-expanded.png#lightbox) |

### <a name="i-want-to-allow-users-in-my-directory-to-request-access-to-groups-applications-or-sharepoint-sites"></a>我想要讓我來要求群組、 應用程式或 SharePoint 網站的權限的目錄中的使用者

> [!div class="mx-tableFixed"]
> | 步驟 | 範例 |
> | --- | --- |
> | **1.** [在目錄中建立新的存取封裝](entitlement-management-access-package-create.md#start-new-access-package) | ![建立存取套件](./media/entitlement-management-scenarios/access-package.png) |
> | **2.** [加入資源的角色，以存取套件](entitlement-management-access-package-edit.md#add-resource-roles)<ul><li>群組</li><li>[應用程式]</li><li>SharePoint 網站</li></ul> | ![新增資源角色](./media/entitlement-management-scenarios/resource-roles.png) |
> | **3.** [新增原則](entitlement-management-access-package-edit.md#policy-for-users-in-your-directory)<ul><li>您的目錄中的使用者</li><li>需要核准</li><li>到期設定</li></ul> | ![Add policy](./media/entitlement-management-scenarios/policy.png) |

### <a name="i-want-to-allow-users-from-my-business-partners-directory-including-users-not-yet-in-my-directory-to-request-access-to-groups-applications-or-sharepoint-sites"></a>我想要允許從我的業務的合作夥伴目錄 （包括使用者尚未我的目錄） 的使用者群組、 應用程式或 SharePoint 網站的要求存取

> [!div class="mx-tableFixed"]
> | 步驟 | 範例 |
> | --- | --- |
> | **1.** [在目錄中建立新的存取封裝](entitlement-management-access-package-create.md#start-new-access-package) | ![建立存取套件](./media/entitlement-management-scenarios/access-package.png) |
> | **2.** [加入資源的角色，以存取套件](entitlement-management-access-package-edit.md#add-resource-roles) | ![新增資源角色](./media/entitlement-management-scenarios/resource-roles.png) |
> | **3.** [新增外部使用者的原則](entitlement-management-access-package-edit.md#policy-for-users-not-in-your-directory)<ul><li>不在您的目錄中的使用者</li><li>需要核准</li><li>到期設定</li></ul> | ![新增外部使用者的原則](./media/entitlement-management-scenarios/policy-external.png) |
> | **4.** [傳送要求給您的商務夥伴存取封裝我存取入口網站 連結](entitlement-management-access-package-edit.md#copy-my-access-portal-link)<ul><li>商務夥伴可以與使用者共用連結</li></ul> |  |

### <a name="i-want-to-change-the-groups-applications-or-sharepoint-sites-in-an-access-package"></a>我想要變更群組、 應用程式或存取封裝中的 SharePoint 網站

> [!div class="mx-tableFixed"]
> | 步驟 | 範例 |
> | --- | --- |
> | **1.** 開啟存取封裝 | ![新增資源角色](./media/entitlement-management-scenarios/resource-roles.png) |
> | **2.** [新增或移除資源角色](entitlement-management-access-package-edit.md#add-resource-roles) | ![新增資源角色](./media/entitlement-management-scenarios/resource-roles-add.png) |

### <a name="i-want-to-view-who-has-an-assignment-to-groups-applications-or-sharepoint-sites"></a>我想要檢視誰可以指派給群組、 應用程式或 SharePoint 網站

> [!div class="mx-tableFixed"]
> | 步驟 | 範例 |
> | --- | --- |
> | **1.** 開啟存取封裝 | ![新增資源角色](./media/entitlement-management-scenarios/resource-roles.png) |
> | **2.** [檢視指派](entitlement-management-access-package-edit.md#view-who-has-an-assignment)<ul><li>檢視哪些使用者可以存取套件的存取權</li><li>檢視哪些使用者的存取權已過期</li></ul> |  |

### <a name="i-want-to-view-groups-applications-or-sharepoint-sites-a-user-has-access-to"></a>我想要檢視群組、 應用程式或使用者有權存取的 SharePoint 網站

> [!div class="mx-tableFixed"]
> | 步驟 | 範例 |
> | --- | --- |
> | [檢視指派的使用者報告](entitlement-management-reports.md)<ul><li>檢視這些要求時以及誰核准</li></ul> |  |

## <a name="approvers"></a>核准者

### <a name="i-want-to-approve-requests-to-access-groups-applications-or-sharepoint-sites"></a>我想要核准存取群組、 應用程式或 SharePoint 網站的要求

> [!div class="mx-tableFixed"]
> | 步驟 | 範例 |
> | --- | --- |
> | **1.** [在我存取入口網站中開啟的要求](entitlement-management-request-approve.md#open-request) | [![我存取入口網站圖示](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** [核准存取要求](entitlement-management-request-approve.md#approve-or-deny-request) | ![核准存取](./media/entitlement-management-scenarios/approve-access.png) |

## <a name="requestors"></a>要求者

### <a name="i-want-to-view-the-groups-applications-or-sharepoint-sites-available-to-me-and-request-access"></a>我想要檢視群組、 應用程式或供我的 SharePoint 網站，並要求存取權

> [!div class="mx-tableFixed"]
> | 步驟 | 範例 |
> | --- | --- |
> | **1.** [登入我存取入口網站](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [![我存取入口網站圖示](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** 尋找存取套件 |  |
> | **3.** [要求存取](entitlement-management-request-access.md#request-an-access-package) | ![要求存取](./media/entitlement-management-scenarios/request-access.png) |

### <a name="im-an-external-user-and-i-want-to-request-access-to-groups-applications-or-sharepoint-sites-with-a-direct-link"></a>我是外部使用者，而我想要要求存取群組、 應用程式或 SharePoint 網站，直接連結

> [!div class="mx-tableFixed"]
> | 步驟 | 範例 |
> | --- | --- |
> | **1.** [尋找您所收到的我存取入口網站連結](entitlement-management-access-package-edit.md#copy-my-access-portal-link) |  |
> | **2.** [登入我存取入口網站](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [![我存取入口網站圖示](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **3.** [要求存取](entitlement-management-request-access.md#request-an-access-package) | ![要求存取的外部使用者](./media/entitlement-management-scenarios/request-access-external.png) |

### <a name="i-want-to-view-the-groups-applications-or-sharepoint-sites-i-already-have-access-to"></a>我想要檢視我已經有存取權的群組、 應用程式或 SharePoint 網站

> [!div class="mx-tableFixed"]
> | 步驟 | 範例 |
> | --- | --- |
> | **1.** [登入我存取入口網站](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [![我存取入口網站圖示](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** 檢視作用中存取套件 |  |

## <a name="next-steps"></a>後續步驟

- [教學課程：建立您第一次存取封裝](entitlement-management-access-package-first.md)
- [委派工作](entitlement-management-delegate.md)
