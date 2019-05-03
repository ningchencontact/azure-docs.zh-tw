---
title: Azure 區塊鏈服務協會
description: ''
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: e745a4ee4789ef46a61b5cb0bbf806c41ef631ec
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027911"
---
# <a name="azure-blockchain-service-consortium"></a>Azure 區塊鏈服務協會

使用 Azure 區塊鏈 Service，您可以建立私用協會可以限制為特定的參與者，網路中的每個區塊鏈網路的區塊鏈網路。 私用協會區塊鏈網路中的參與者可以檢視，並與其互動的區塊鏈。 Azure 區塊鏈 Service 中的協會網路可以包含兩種類型的參與者角色的成員：

* **系統管理員**-特殊權限可以採取協會管理動作，並可以參與區塊鏈交易的參與者。

* **使用者**-無法採取任何協會管理動作，但可以參與區塊鏈交易的參與者。

協會網路可以是混合的參與者角色，而且可以有任意數目的每種角色類型。 必須有至少一個系統管理員。

下圖顯示多個參與者協會網路：

![私用協會網路圖表](./media/consortium/network-diagram.png)

透過 Azure 區塊鏈 Service 中的協會管理，您可以管理協會網路中的參與者。 協會的管理根據共識模型的網路。 在目前的預覽版本中，Azure 區塊鏈 Service 會提供協會管理集中式的一致性模型。 任何特殊權限的參與者，以管理角色可以採取協會管理動作，例如新增或移除網路中的參與者。

## <a name="roles"></a>角色

協會的參與者可以是個人或組織，而且可以指派使用者角色或系統管理員角色。 下表列出兩個角色之間的大致差異：

|  動作 | 使用者角色 | 系統管理員角色
|--------|:----:|:------------:|
| 建立新的成員 | 是 | 是 |
| 邀請新的成員 | 否 | 是 |
| 設定或變更成員的參與者角色 | 否 | 是 |
| 變更成員的顯示名稱 | 僅針對自己的成員 | 僅針對自己的成員 |
| 移除成員 | 僅針對自己的成員 | 是 |
| 參與的區塊鏈交易 | 是 | 是 |

### <a name="user-role"></a>使用者角色

使用者會與沒有系統管理員功能的協會參與者。 它們不能參與管理相關協會的成員。 使用者可以變更其成員的顯示名稱，而且可以從協會本身移除。

### <a name="administrator"></a>系統管理員

系統管理員可以管理內協會成員。 系統管理員可以邀請成員、 移除成員，或更新成員協會內的角色。
一律必須至少一個協會內的系統管理員。 最後一個系統管理員必須指定為系統管理員角色另一個參與者，才會離開協會。

## <a name="managing-members"></a>管理成員

只有系統管理員可以邀請協會其他參與者。 系統管理員邀請參與者使用其 Azure 訂用帳戶識別碼。

一旦受邀，與會者就可以藉由部署 Azure Blockchain 服務中的新成員加入的區塊鏈協會。 若要檢視及加入受邀的協會，您必須指定相同的 Azure 訂用帳戶識別碼在邀請中由網路系統管理員。

系統管理員可以移除任何參與者協會，包括其他系統管理員。 成員只能移除本身協會。

## <a name="consortium-management-smart-contract"></a>協會管理智慧合約

Azure 區塊鏈 Service 中的協會管理是透過協會管理智能合約。 當您部署新的區塊鏈成員時，智能合約會自動部署至您的節點。

在 Azure 入口網站中，您可以檢視根協會管理智慧合約的位址。 **RootContract 位址**是區塊鏈成員的 [概觀] 區段中。

![RootContract address](./media/consortium/rootcontract-address.png)

您可以使用協會 management 協會的管理智慧合約與互動[PowerShell 模組](manage-consortium-powershell.md)，Azure 入口網站中，或直接透過智慧合約使用 Azure 區塊鏈 Service 會產生以太坊帳戶。

## <a name="ethereum-account"></a>以太坊帳戶

建立成員時，會建立以太坊帳戶金鑰。 Azure 區塊鏈服務會使用金鑰來建立與協會管理相關的交易。 以太坊帳戶金鑰是由 Azure 區塊鏈服務自動管理。

在 Azure 入口網站中，您可以檢視成員的帳戶。 區塊鏈成員的 [概觀] 區段中，是成員帳戶。

![成員的帳戶](./media/consortium/member-account.png)

您可以按一下成員的帳戶，並輸入新密碼，以太坊帳戶重設。 以太坊帳戶地址和密碼會重設。  

## <a name="next-steps"></a>後續步驟

[如何管理使用 PowerShell 的 Azure 區塊鏈 Service 中的成員](manage-consortium-powershell.md)
