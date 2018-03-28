---
title: Microsoft Azure Active Directory 單一登入外掛程式常見問題集 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與適用於 JIRA 的 Microsoft Azure Active Directory 單一登入之間的單一登入。
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
ms.date: 02/06/2018
ms.author: jeedes
ms.openlocfilehash: 571fbd5078f66375f6e81cba2a790121366f9d60
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/16/2018
---
# <a name="microsoft-azure-active-directory-single-sign-on-plugin-faq"></a>Microsoft Azure Active Directory 單一登入外掛程式常見問題集 

## <a name="1-whats-the-microsoft-sso-add-on"></a>1.什麼是 Microsoft SSO 附加元件？

此附加元件提供適用於 Atlassian 之 JIRA (包括 JIRA 核心、JIRA 軟體、JIRA 服務台) 和 Confluence 內部部署軟體的的單一登入功能。 附加元件可以搭配 Azure AD 作為 IdP 運作。

## <a name="2-add-on-works-with-which-atlassian-products"></a>2.附加元件可搭配哪些 Atlassian 產品運作？

目前附加元件只能搭配內部部署版本的 JIRA 和 Confluence 運作。

## <a name="3-does-this-add-on-work-on-cloud-version"></a>3.此附加元件可否在雲端版本上運作？

編號 僅支援內部部署版本的 JIRA 和 Confluence。

## <a name="4-which-versions-of-jira-and-confluence-are-supported"></a>4.支援哪些版本的 JIRA 和 Confluence？

以下是支援的版本清單：

* JIRA 核心和軟體：6.0 到 7.2.2 
* JIRA 服務台：3.0 至 3.2 
* Confluence：5.0 到 5.10

## <a name="5-is-this-add-on-free-or-paid"></a>5.這個附加元件是免費還是需付費？

這是免費的附加元件，可以從 Atlassian 市集進行安裝。

## <a name="6-do-i-need-to-restart-jiraconfluence-once-i-deploy-the-add-on"></a>6.部署附加元件之後是否需要重新啟動 JIRA/Confluence

附加元件部署之後不需要重新啟動。 您可以在部署之後立即開始使用附加元件。

## <a name="7-how-do-i-get-support-for-the-add-on"></a>7.如何取得附加元件的支援？

請以電子郵件與我們連絡：<email>。我們會在 <> 小時內回應。 您也可以透過 Azure 入口網站管道向 Microsoft 發出支援票證。 您也可以在一般工作日的上午 <> 點到下午 <> 點期間來電與我們連絡：<Number>。

## <a name="8-would-this-add-on-work-on-mac-or-ubuntu-installation-of-jira-and-confluence"></a>8.此附加元件是否可在安裝於 Mac 或 Ubuntu 的 JIRA 和 Confluence 上運作？

我們只在安裝於 64 位元 Windows 伺服器的 JIRA 和 Confluence 上測試過這個附加元件。

## <a name="9-does-this-add-on-work-with-other-idps-than-azure-ad"></a>9.此附加元件是否可與 Azure AD 以外的 IdP 搭配運作？

編號 附加元件只能與 Azure AD 搭配運作。

## <a name="10-what-version-of-saml-does-the-add-on-work-with"></a>10.附加元件可以與哪個版本的 SAML 搭配運作？

附加元件可以與 SAML 2.0 搭配運作。

## <a name="11-does-the-add-on-do-use-provisioning-as-well"></a>11.附加元件是否也使用佈建？

編號 目前附加元件只提供以 SAML 2.0 為基礎的 SSO。 必須在使用 SSO 登入之前於應用程式中佈建使用者。

## <a name="12-are-cluster-versions-of-jira-and-confluence-supported-by-add-on"></a>12.附加元件是否支援叢集版本的 JIRA 和 Confluence？

編號 附加元件只能搭配內部部署版本的 JIRA 和 Confluence 運作。

## <a name="13-would-this-plugin-work-with-http-version-of-jira-and-confluence"></a>13.此外掛程式是否可以與 HTTP 版本的 JIRA 和 Confluence 搭配運作？

編號 附加元件只能與已啟用 HTTPS 的安裝搭配運作。

## <a name="14-do-i-need-to-buy-license-of-the-add-on"></a>14.是否需要購買附加元件的授權？

附加元件是免費的。