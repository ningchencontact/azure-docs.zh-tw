---
title: 包含檔案
description: 包含檔案與先決條件
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 66b5f8e2-e08d-43c8-b460-6204aecda8f7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.custom: include file
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: maheshu
ms.openlocfilehash: 1fba8cc9ae40cf5539016bbd73de65f557a64136
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2019
ms.locfileid: "59551575"
---
> [!IMPORTANT]
> **請先啟用 Azure AD Domain Services 的密碼雜湊同步處理，再完成本文中的工作。**
>
> 請根據 Azure AD 目錄中的使用者類型來遵循下列指示。 如果您在 Azure AD 目錄中混合使用純雲端和已同步的使用者帳戶，則下列兩個指示都必須完成。 您可能無法實現下列作業，如果您嘗試使用 B2B 來賓帳戶 （例如您 gmail 或從不同的身分識別提供者的我們允許 MSA） 因為我們沒有同步處理至受控網域，因為這些使用者的密碼會在目錄中的來賓帳戶。 這些帳戶，包括其密碼的完整資訊是 Azure AD 外部，而這項資訊不是在 Azure AD 中，因此它不會不甚至進行同步處理受控網域。 
> - [僅限雲端使用者帳戶的指示](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)
> - [從內部部署目錄同步的使用者帳戶指示](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant.md)
