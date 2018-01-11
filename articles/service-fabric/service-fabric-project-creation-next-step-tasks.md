---
title: "Service Fabric 專案建立後續步驟 |Microsoft Docs"
description: "深入了解您剛才在 Visual Studio 中建立的應用程式專案。  了解如何建置使用教學課程的服務，並深入了解開發適用於 Service Fabric 服務。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 299d1f97-1ca9-440d-9f81-d1d0dd2bf4df
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/07/2017
ms.author: rwike77
ms.openlocfilehash: 17eb1e7c2184fe9cae19685a47ea80716292b754
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2017
---
# <a name="your-service-fabric-application-and-next-steps"></a>您的 Service Fabric 應用程式和後續步驟
您的 Azure Service Fabric 應用程式已經建立。 本文說明某些教學課程，試試看，您的專案、 更多的資訊，您可能會想要和潛在的下一個步驟的結構。

## <a name="get-started-with-tutorials-walk-throughs-and-samples"></a>快速入門教學課程、 逐步解說，以及範例
準備好開始了嗎？  

透過.NET 應用程式教學課程的工作。 深入了解如何[建置的應用程式](service-fabric-tutorial-create-dotnet-app.md)前端的 ASP.NET Core 與可設定狀態後端，[部署應用程式](service-fabric-tutorial-deploy-app-to-party-cluster.md)至叢集，[設定 CI/CD](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)，和[設定監視和診斷](service-fabric-tutorial-monitoring-aspnet.md)。

或者，嘗試下列逐步解說的其中一個，然後建立您第一次...
- [在 Windows 上的 C# 可靠 Services 服務](service-fabric-reliable-services-quick-start.md) 
- [在 Windows 上的 C# Reliable Actors 服務](service-fabric-reliable-actors-get-started.md) 
- [在 Windows 上的客體可執行服務](quickstart-guest-app.md) 
- [Windows 容器應用程式](service-fabric-get-started-containers.md) 

您可能也會想要試用我們[範例應用程式](http://aka.ms/servicefabricsamples)。

## <a name="have-questions-or-feedback--need-to-report-an-issue"></a>有任何疑問或意見嗎？  要回報問題？
閱讀[常見問題的解答](service-fabric-common-questions.md)並尋找可以執行的 Service Fabric 和它的使用方式的答案。

[支援選項](service-fabric-support.md)列出 stackoverflow 文章和 MSDN 上的論壇詢問問題，以及選項報告問題、 取得支援，以及傳送產品意見反應。

## <a name="the-application-project"></a>應用程式專案
每個新應用程式都包含一個應用程式專案。 視所選擇的服務類型而定，可能會有一個或兩個額外的專案。

應用程式專案包含：

* 一組對構成應用程式之服務的參考。
* 三個發行設定檔 (1-Node Local、5-Node Local、Cloud)，可用來維護喜好設定，以搭配不同的環境使用，例如叢集端點的相關喜好設定，以及是否預設要執行升級部署。
* 三個應用程式參數檔案 (同上)，可用來維護環境特定應用程式組態，例如為服務建立之分割區的數目。 深入了解如何[設定您的應用程式的多個環境](service-fabric-manage-multiple-environment-app-configuration.md)。
* 部署指令碼，可用來從命令列部署您的應用程式，或透過自動化連續整合和部署管線中部署您的應用程式。 深入了解[部署應用程式使用 PowerShell](service-fabric-deploy-remove-applications.md)。
* 應用程式資訊清單，描述應用程式。 您可以在 ApplicationPackageRoot 資料夾下方找到資訊清單。 深入了解[應用程式和服務資訊清單](service-fabric-application-model.md)。



## <a name="learn-more-about-the-programming-models"></a>深入了解程式設計模型
Service Fabric 提供多種撰寫和管理服務的方式。  以下是上的 概觀和概念性資訊[無狀態與可設定狀態的可靠服務](service-fabric-reliable-services-introduction.md)， [Reliable Actors](service-fabric-reliable-actors-introduction.md)，[容器](service-fabric-containers-overview.md)，[客體可執行檔](service-fabric-deploy-existing-app.md)，和[無狀態與可設定狀態的 ASP.NET Core services](service-fabric-reliable-services-communication-aspnetcore.md)。

## <a name="learn-about-service-communication"></a>深入了解服務通訊
Service Fabric 應用程式是由不同的服務，其中每個服務會執行特殊的工作所組成。 這些服務可能會與對方進行通訊，且有可能會連接到並與服務通訊，叢集外的用戶端應用程式。 深入了解如何[設定與您的服務之間的通訊](service-fabric-connect-and-communicate-with-services.md)Service Fabric 中。 

## <a name="learn-about-configuring-application-security"></a>瞭解如何設定應用程式安全性
您可以保護在叢集中不同的使用者帳戶下執行的應用程式。 在以該使用者帳戶部署時，Service Fabric 也會協助保護應用程式所使用的資源，例如檔案、目錄和憑證。 如此一來，即使在共用主控環境中，執行中的應用程式就不會彼此干擾。  深入了解如何[設定您的應用程式的安全性原則](service-fabric-application-runas-security.md)。

您的應用程式可能包含機密資訊，例如儲存體連接字串、 密碼或其他不會處理以純文字的值。 深入了解如何[管理您的應用程式中的機密](service-fabric-application-secret-management.md)。

## <a name="learn-about-the-application-lifecycle"></a>深入了解應用程式生命週期
因為與其他平台，Service Fabric 應用程式通常會經歷下列階段： 設計、 開發、 測試、 部署、 升級、 維護和移除。 [這篇文章](service-fabric-application-lifecycle.md)提供 Api 和由整個 Service Fabric 應用程式生命週期的階段的不同角色的使用方式的概觀。

## <a name="next-steps"></a>後續步驟
- [在 Azure 中建立 Windows 叢集](service-fabric-tutorial-create-vnet-and-windows-cluster.md)。
- 視覺化您的叢集，包括具有已部署的應用程式和實體配置， [Service Fabric 總管](service-fabric-visualizing-your-cluster.md)。
- [版本和升級服務](service-fabric-application-upgrade-tutorial.md)


