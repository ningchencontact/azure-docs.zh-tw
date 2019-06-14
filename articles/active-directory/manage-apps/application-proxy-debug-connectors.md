---
title: 偵錯應用程式 Proxy 連接器-Azure Active Directory |Microsoft Docs
description: Azure Active Directory (Azure AD) 應用程式 Proxy 連接器的問題進行偵錯。
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: mimart
ms.reviewer: japere
ms.openlocfilehash: c3088ae777fe1a64be218105d36fdb9e01d7b798
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66172231"
---
# <a name="debug-application-proxy-connector-issues"></a>偵錯應用程式 Proxy 連接器問題 

這篇文章可協助您針對 Azure Active Directory (Azure AD) 應用程式 Proxy 連接器的問題進行疑難排解。 如果您使用的內部部署 web 應用程式中，遠端存取的應用程式 Proxy 服務，但您無法連線到應用程式，請使用此流程圖連接器問題進行偵錯。 

## <a name="before-you-begin"></a>開始之前

本文假設您已安裝的應用程式 Proxy 連接器，但發生問題。 當應用程式 Proxy 針對問題進行疑難排解，我們建議您開始使用此疑難排解的流程，以判斷應用程式 Proxy 連接器已正確設定。 如果您仍然無法連線到應用程式，請依照下列中的疑難排解流程[偵錯應用程式 Proxy 應用程式問題](application-proxy-debug-apps.md)。  


如需有關應用程式 Proxy，並使用其連接器的詳細資訊，請參閱：

- [在內部部署應用程式，透過應用程式 Proxy 遠端存取](application-proxy.md)
- [應用程式 Proxy 連接器](application-proxy-connectors.md)
- [安裝並註冊連接器](application-proxy-add-on-premises-application.md)
- [針對應用程式 Proxy 問題和錯誤訊息進行疑難排解](application-proxy-troubleshoot.md)

## <a name="flowchart-for-connector-issues"></a>連接器問題的流程圖

此流程圖會引導您進行偵錯一些較常見的連接器問題的步驟。 如需每個步驟的詳細資訊，請參閱下列流程圖。

![顯示偵錯的連接器的步驟流程圖](media/application-proxy-debug-connectors/application-proxy-connector-debugging-flowchart.png)

|  | 動作 | 描述 | 
|---------|---------|---------|
|1 | 尋找指派至應用程式的連接器群組 | 您可能有多部伺服器上安裝連接器，連接器應該是在此情況下[指派給連接器群組](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups)。 若要深入了解連接器群組，請參閱[使用連接器群組在個別的網路和位置上發佈應用程式](application-proxy-connector-groups.md)。 |
|2 | 安裝連接器，並將群組指派 | 如果您沒有安裝連接器，請參閱[安裝並註冊連接器](application-proxy-add-on-premises-application.md#install-and-register-a-connector)。<br></br>如果連接器無法指派給群組，請參閱[指派給群組的連接器](application-proxy-connector-groups.md#create-connector-groups)。<br></br>如果應用程式不指派給連接器群組，請參閱[指派給連接器群組的應用程式](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups)。|
|3 | 連接器的伺服器上執行的連接埠測試 | 在連接器伺服器上，執行使用連接埠測試[telnet](https://docs.microsoft.com/windows-server/administration/windows-commands/telnet)或其他連接埠測試工具來檢查是否已開啟連接埠 443 和 80。|
|4 | 設定網域和連接埠 | [請確定您的網域和連接埠已正確設定](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment)，連接器才能正常運作中,，有特定的連接埠必須開啟和您的伺服器必須能夠存取的 Url。 |
|5 | 檢查後端 proxy 是否使用中 | 請檢查連接器會使用後端 proxy 伺服器或略過它們。 如需詳細資訊，請參閱 <<c0> [ 疑難排解連接器 proxy 問題和服務的連線能力問題](application-proxy-configure-connectors-with-proxy-servers.md#troubleshoot-connector-proxy-problems-and-service-connectivity-issues)。 |
|6 | 更新的連接器和後端 proxy 伺服器的更新程式 | 如果後端 proxy 正在使用中，您會想確定連接器使用相同的 proxy。 如需有關疑難排解及設定連接器以使用 proxy 伺服器的詳細資訊，請參閱[使用現有內部部署 proxy 伺服器](application-proxy-configure-connectors-with-proxy-servers.md)。 |
|7 | 載入連接器的伺服器上的應用程式的內部 URL | 在連接器伺服器上，載入應用程式的內部 URL。 |
|8 | 檢查內部網路連線 | 此偵錯流程，才能夠診斷您的內部網路中沒有連線問題。 應用程式必須能夠在內部針對此連接器來運作。 您可以啟用，並檢視連接器事件記錄檔中所述[應用程式 Proxy 連接器](application-proxy-connectors.md#under-the-hood)。 |
|9 | 延長的後端逾時值 | 在**其他設定**針對您的應用程式中，變更**後端應用程式逾時**設定為**長**。 請參閱[新增至 Azure AD 的內部部署應用程式](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)。 |
|10 | 如果問題持續發生，針對特定的流程問題、 檢閱應用程式和偵錯流程的 SSO | 使用[偵錯應用程式 Proxy 應用程式問題](application-proxy-debug-apps.md)疑難排解流程。 |

## <a name="next-steps"></a>後續步驟


* [使用連接器群組在個別的網路和位置上發佈應用程式](application-proxy-connector-groups.md)
* [使用現有的內部部署 Proxy 伺服器](application-proxy-configure-connectors-with-proxy-servers.md)
* [針對應用程式 Proxy 和連接器錯誤進行疑難排解](application-proxy-troubleshoot.md)
* [如何以無訊息方式安裝 Azure AD 應用程式 Proxy 連接器](application-proxy-register-connector-powershell.md)
