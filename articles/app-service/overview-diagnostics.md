---
title: Azure App Service 診斷概觀 | Microsoft Docs
description: 了解如何疑難排解問題，以及在您的應用程式與 App Service 診斷。
keywords: App Service, Azure App Service, 診斷, 支援, Web 應用程式, 疑難排解, 自助
services: app-service
documentationcenter: ''
author: jen7714
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: jennile
ms.custom: seodec18
ms.openlocfilehash: f2f798be85e9c3aeb8d4b54cba89d8be059427e0
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147351"
---
# <a name="azure-app-service-diagnostics-overview"></a>Azure App Service 診斷概觀

當您執行 Web 應用程式時，必須為使用者因 500 錯誤向您反映網站已關閉的所有可能問題作好準備。 App Service 診斷是一種智慧型和互動式的體驗，可協助您疑難排解您的應用程式，而不需要設定。 當您執行遇到問題，您的應用程式時，App Service 診斷將點出問題，引導您到正確的資訊，更輕鬆快速地疑難排解和解決問題。

雖然這項體驗是最有幫助的當您遇到的問題與您的應用程式在過去 24 小時內，所有診斷圖形都可供您分析。

App Service 診斷不僅適用於 Windows 上您的應用程式，也適用於 [Linux/容器](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro)、[App Service Environment](https://docs.microsoft.com/azure/app-service/environment/intro) 和 [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) 上的應用程式。

## <a name="open-app-service-diagnostics"></a>開啟 App Service 診斷

若要存取 App Service 診斷，瀏覽至您的 App Service web 應用程式或 App Service Environment 現[Azure 入口網站](https://portal.azure.com)。 在左方導覽列中，按一下 [診斷並解決問題]  。

適用於 Azure Functions 瀏覽至您的函式應用程式，並在頂端導覽列中，按一下 **平台功能**，然後選取**診斷並解決問題**從**資源管理**一節。

在 App Service 診斷首頁上，您可以選擇最能描述您的應用程式的問題，在首頁中的每個磚中使用關鍵字的類別。 此外，此頁面是您可以在哪裡找到**診斷工具**Windows 應用程式。 請參閱[（僅適用於 Windows 應用程式） 的診斷工具](#diagnostic-tools-only-for-windows-app)。

![首頁](./media/app-service-diagnostics/app-service-diagnostics-homepage-1.png)

## <a name="interactive-interface"></a>互動式介面

一旦您選取最能配合您的應用程式問題的首頁類別時，App Service 診斷的互動式介面精靈，可以引導您診斷及解決您的應用程式的問題。 若要檢視之問題類別的完整診斷報告，您有興趣，您可以使用精靈所提供的圖格快速鍵。 圖格快速鍵會提供直接的方式來存取診斷計量。

![圖格快速鍵](./media/app-service-diagnostics/tile-shortcuts-2.png)

只要按一下這些圖格，您可以看到磚中所述的問題相關的主題清單。 這些主題會提供程式碼片段的完整報表的重要資訊。 您可以按一下任一這些主題，以調查進一步的問題。 此外，您可以按一下**檢視完整報告**探索在單一頁面上的所有主題。

![主題](./media/app-service-diagnostics/application-logs-insights-3.png)

![檢視完整的報告](./media/app-service-diagnostics/view-full-report-4.png)

## <a name="diagnostic-report"></a>診斷報告

選擇按一下 上一個主題來調查進一步的問題之後，您可以檢視更多詳細主題通常輔以圖形和 markdown。 診斷報告可以查明您的應用程式的問題，功能強大的工具。

![診斷報告](./media/app-service-diagnostics/full-diagnostic-report-5.png)

## <a name="health-checkup"></a>健康狀態檢查

如果您不知道您的應用程式有什麼或不知道要從何處開始對問題進行疑難排解時，健康狀態檢查將會是不錯的起點。 健康狀態檢查會分析您的應用程式，讓您快速的互動式概觀將點出狀況良好或什麼是癥結，告訴您哪裡尋找要調查此問題。 其智慧型及互動式的介面可提供您進行疑難排解程序的指導方針。 健康狀態檢查已與精靈體驗的 Windows 應用程式和 web 應用程式向下診斷整合針對 Linux 應用程式的報表。

### <a name="health-checkup-graphs"></a>健全狀況檢查圖形

健康狀態檢查中有四個不同的圖形。

- **要求和錯誤：** 顯示 HTTP 伺服器錯誤以及過去 24 小時內提出的要求數目的圖表。
- **應用程式效能：** 過去 24 小時內，不同的百分位數群組的顯示回應時間的圖表。
- **CPU 使用量：** 圖表顯示過去 24 小時內的每個執行個體的 CPU 使用率整體的百分比。  
- **記憶體使用量：** 過去 24 小時內顯示每個執行個體的整體百分比的實體記憶體使用量的圖表。

![健康狀態檢查](./media/app-service-diagnostics/health-checkup-6.png)

### <a name="investigate-application-code-issues-only-for-windows-app"></a>調查應用程式程式碼問題 （僅適用於 Windows 應用程式）

因為許多應用程式問題與您應用程式程式碼中的問題相關，App Service 診斷與 [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) 整合，以反白顯示要與所選停機狀況相互關聯的例外狀況和相依性問題。 Application Insights 有分別啟用。

![Application Insights](./media/app-service-diagnostics/application-insights-7.png)

若要檢視 Application Insights 例外狀況和相依性，請選取**web 應用程式，關閉**或是**web 應用程式變慢**圖格快速鍵。

### <a name="troubleshooting-steps-only-for-windows-app"></a>（僅適用於 Windows 應用程式） 的疑難排解步驟

如果偵測到問題的特定問題類別與過去 24 小時內，您可以檢視完整診斷報告，以及 App Service 診斷可能會提示您若要檢視更多的疑難排解建議和後續的步驟，以更具引導性質的體驗。

![Application Insights 和疑難排解和後續步驟](./media/app-service-diagnostics/troubleshooting-and-next-steps-8.png)

## <a name="diagnostic-tools-only-for-windows-app"></a>（僅適用於 Windows 應用程式） 的診斷工具

診斷工具包括更進階的診斷工具，協助您調查應用程式程式碼問題、 速度很慢、 連接字串和更多功能。 而主動的工具，可協助您降低 CPU 使用量、 要求和記憶體的問題。

### <a name="proactive-cpu-monitoring"></a>主動式 CPU 監視

主動式 CPU 監視提供簡單主動的方式來採取動作，當您針對您的應用程式的應用程式或子程序會使用高的 CPU 資源。 您可以設定您自己的 CPU 臨界值規則，以暫時減輕高的 CPU 問題，直到找到非預期的問題的真正原因。

![主動式 CPU 監視](./media/app-service-diagnostics/proactive-cpu-monitoring-9.png)

### <a name="proactive-auto-healing"></a>主動式自動修復

如同主動監視 CPU，積極的自動修復提供簡單、 主動的方式，來降低非預期的行為，您的應用程式。 您可以設定您自己的要求計數、 慢速要求、 記憶體限制和觸發程序的緩和措施的 HTTP 狀態碼為基礎的規則。 這項工具可用來暫時降低非預期的行為，直到找到問題的真正原因。 如需有關主動式自動修復的詳細資訊，請瀏覽[宣布新自動修復在 app service 診斷經驗](https://azure.github.io/AppService/2018/09/10/Announcing-the-New-Auto-Healing-Experience-in-App-Service-Diagnostics.html)。

![主動式自動修復](./media/app-service-diagnostics/proactive-auto-healing-10.png)

## <a name="change-analysis-only-for-windows-app"></a>變更分析 （僅適用於 Windows 應用程式）

在步調快速的開發環境中，有時可能難以追蹤對您的應用程式的所有變更，並讓單獨 pinpoint 導致狀況不良的行為變更。 變更分析可協助您縮小對您的應用程式，以協助疑難排解體驗的變更。 變更分析位於**應用程式變更**而也這類內嵌的診斷報告中**應用程式當機**讓您可以使用它與其他度量。

變更分析必須先啟用，才能使用此功能。 如需有關變更分析的詳細資訊，請瀏覽[宣布新的變更分析體驗，在 App Service 診斷](https://azure.github.io/AppService/2019/05/07/Announcing-the-new-change-analysis-experience-in-App-Service-Diagnostics-Analysis.html)。

![變更分析預設頁面](./media/app-service-diagnostics/change-analysis-default-page-11.png)

![差異檢視](./media/app-service-diagnostics/diff-view-12.png)