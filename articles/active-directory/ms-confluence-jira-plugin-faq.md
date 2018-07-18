---
title: Azure Active Directory SSO 外掛程式常見問題集 | Microsoft Docs
description: 針對在 Azure Active Directory 和 Jira/Confluence 之間設定單一登入的常見問題集和答案。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4b663047-7f88-443b-97bd-54224b232815
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2018
ms.author: jeedes
ms.openlocfilehash: eb7576c48d3836eec8051d849cefe4c5ec6658c9
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2018
ms.locfileid: "34699201"
---
# <a name="faq-for-the-azure-active-directory-sso-plug-in"></a>Azure Active Directory SSO 外掛程式常見問題集

如果您有關於此外掛程式的任何查詢，請參閱以下的常見問題集。

## <a name="what-does-the-plug-in-do"></a>這個外掛程式能做什麼？

此外掛程式提供適用於 Atlassian Jira (包括 Jira 核心、Jira 軟體、Jira 服務台) 和 Confluence 內部部署軟體的單一登入 (SSO) 功能。 此外掛程式搭配 Azure Active Directory (Azure AD) 使用，成為識別提供者 (IdP)。

## <a name="which-atlassian-products-does-the-plug-in-work-with"></a>此外掛程式與哪些 Atlassian 產品搭配使用？

此外掛程式與內部部署版本的 Jira 和 Confluence 搭配使用。

## <a name="does-the-plug-in-work-on-cloud-versions"></a>此外掛程式可否在雲端版本上運作？

編號 此外掛程式僅支援內部部署版本的 Jira 和 Confluence。

## <a name="which-versions-of-jira-and-confluence-does-the-plug-in-support"></a>此外掛程式支援哪些 Jira 和 Confluence 版本？

此外掛程式支援這些版本：

* Jira 核心和軟體：6.0 到 7.8
* Jira Service Desk：3.0 到 3.2
* Confluence：5.0 到 5.10

## <a name="is-the-plug-in-free-or-paid"></a>此外掛程式是免費還是需付費？

是免費的附加元件。

## <a name="do-i-need-to-restart-jira-or-confluence-after-i-deploy-the-plug-in"></a>部署此外掛程式之後是否需要重新啟動 Jira 或 Confluence？

不需要重新啟動。 您可以立即開始使用此外掛程式。

## <a name="how-do-i-get-support-for-the-plug-in"></a>如何取得此外掛程式的支援？

您可以連絡 [Azure AD SSO 整合小組](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)以取得此外掛程式所需的任何支援。 小組會在營業時間 24-48 小時內回應。

您也可以透過 Azure 入口網站管道向 Microsoft 發出支援票證。

## <a name="would-the-plug-in-work-on-a-mac-or-ubuntu-installation-of-jira-and-confluence"></a>此外掛程式是否可用於安裝在 Mac 或 Ubuntu 上的 Jira 和 Confluence？

我們只在安裝於 64 位元 Windows 伺服器的 Jira 和 Confluence 上測試過這個外掛程式。

## <a name="does-the-plug-in-work-with-idps-other-than-azure-ad"></a>此外掛程式是否可與 Azure AD 以外的 IdP 搭配運作？

編號 它只能與 Azure AD 搭配運作。

## <a name="what-version-of-saml-does-the-plug-in-work-with"></a>此外掛程式可以與哪個版本的 SAML 搭配運作？

可以與 SAML 2.0 搭配運作。

## <a name="does-the-plug-in-do-user-provisioning"></a>此外掛程式會進行使用者佈建嗎？

編號 此外掛程式只提供以 SAML 2.0 為基礎的 SSO。 必須在使用 SSO 登入之前，於應用程式中佈建使用者。

## <a name="does-the-plug-in-support-cluster-versions-of-jira-and-confluence"></a>此外掛程式支援叢集版本的 Jira 和 Confluence 嗎？

編號 此外掛程式與內部部署版本的 Jira 和 Confluence 搭配使用。

## <a name="does-the-plug-in-work-with-http-versions-of-jira-and-confluence"></a>此外掛程式能與 HTTP 版本的 Jira 和 Confluence 搭配使用嗎？

編號 此外掛程式只能與已啟用 HTTPS 的安裝搭配運作。