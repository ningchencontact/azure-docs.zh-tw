---
title: Azure App Service 的安全性建議
description: Azure App Service 的安全性建議。 實作這些建議會說明我們的共同的責任模型和將中所述，滿足您的安全性責任有所改善 web 應用程式解決方案的整體安全性。
services: app-service
author: barclayn
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: barclayn
ms.custom: security-recommendations
ms.openlocfilehash: 53cd2b1dde1158a1c46f798e57911dad110dc87e
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718260"
---
# <a name="security-recommendations-for-app-service"></a>App Service 的安全性建議

本文包含適用於 Azure App Service 的安全性建議。 實作這些建議會說明我們的共同的責任模型和將中所述，滿足您的安全性責任有所改善您的 Web 應用程式解決方案的整體安全性。 如需有關哪些 Microsoft 滿足服務提供者的責任，讀取[Azure 基礎結構安全性](../security/azure-security-infrastructure.md)。

## <a name="general"></a>一般

| 建議 | 註解 |
|-|-|----|
| 保持最新狀態 | 使用支援的平台，程式設計語言、 通訊協定和架構的最新版本。 |

## <a name="identity-and-access-management"></a>身分識別和存取管理

| 建議 | 註解 |
|-|----|
| 停用匿名存取 | 除非您需要支援匿名要求，停用匿名存取。 如需有關 Azure App Service 驗證選項的詳細資訊，請參閱 < [Azure App Service 中驗證和授權](overview-authentication-authorization.md)。|
| 需要驗證 | 可能的話，使用而不需要撰寫程式碼的 App Service 驗證模組，來處理驗證和授權。 請參閱[Azure App Service 中驗證和授權](overview-authentication-authorization.md)。 |
| 保護具有已驗證的存取權的後端資源 | 您可以使用使用者的身分識別，或使用應用程式身分識別來向後端資源。 當您選擇使用應用程式身分識別[受控身分識別](overview-managed-identity.md)。
| 需要用戶端憑證驗證 | 用戶端憑證驗證會提高安全性，只允許來自可使用您提供的憑證進行驗證的用戶端的連線。 |

## <a name="data-protection"></a>資料保護

| 建議 | 註解 |
|-|-|
| 將 HTTP 至 HTTPs 重新導向 | 根據預設，用戶端可以使用 HTTP 或 HTTPS 連接至 web 應用程式。 我們會建議因為 HTTPS 來提供安全的連線，也就是同時使用 SSL/TLS 通訊協定，將 HTTP 重新導向至 HTTPs 加密及驗證。 |
| 對 Azure 資源的通訊進行加密 | 當您的應用程式連接至 Azure 資源，例如[SQL Database](https://azure.microsoft.com/services/sql-database/)或是[Azure 儲存體](/azure/storage/)，連接會保持在 Azure 中。 因為連線中斷透過在 Azure 中的共用網路，您應該一律會加密所有通訊。 |
| 所需的最可能的 TLS 版本 | 自 2018年新的 Azure App Service 應用程式會使用 TLS 1.2。 TLS 的較新版本包含透過較舊的通訊協定版本的安全性改進。 |
| 使用 FTPS | App Service 同時支援使用 FTP 和 FTPS 來部署您的檔案。 使用 FTPS 而不是 FTP 時可能。 如有其中一個或兩個通訊協定都不在使用中，您應該[停用它們](deploy-ftp.md#enforce-ftps)。 |
| 保護應用程式資料 | 不會將應用程式祕密，例如資料庫認證、 API 權杖或私密金鑰儲存在程式碼或組態檔中。 普遍接受的方法是使用您所選語言的標準模式，將它們當作[環境變數](https://wikipedia.org/wiki/Environment_variable)存取。 在 Azure App Service 中，您可以定義透過環境變數[應用程式設定](web-sites-configure.md)並[連接字串](web-sites-configure.md)。 應用程式設定和連接字串會加密儲存在 Azure 中。 只有在應用程式啟動時，已插入您的應用程式處理序記憶體之前，應用程式設定會進行解密。 加密金鑰會定期輪替。 或者，您可以將您的 Azure App Service 應用程式的整合[Azure Key Vault](/azure/key-vault/)進階的祕密管理。 藉由[利用受控識別存取 Key Vault](../key-vault/tutorial-web-application-keyvault.md)，App Service 應用程式便可安全地存取您所需的祕密。 |

## <a name="networking"></a>網路功能

| 建議 | 註解 |
|-|-|
| 使用靜態 IP 限制 | 在 Windows 上的 azure App Service 可讓您定義允許存取您的應用程式的 IP 位址的清單。 允許清單可以包含個別 IP 位址，或以子網路遮罩所定義的 IP 位址範圍。 如需詳細資訊，請參閱 [Azure App Service 靜態 IP 限制](app-service-ip-restrictions.md)。  |
| 使用隔離的定價層 | 除了隔離的定價層，所有層會在 Azure App Service 中的共用的網路基礎結構上都執行您的應用程式。 隔離的層可讓您完整的網路隔離執行您的應用程式內的專用[App Service environment](environment/intro.md)。 App Service 環境就會在您擁有的 [Azure 虛擬網路](/azure/virtual-network/)執行個體中執行。|
| 使用安全連線時存取內部部署資源 | 您可以使用[混合式連線](app-service-hybrid-connections.md)，[虛擬網路整合](web-sites-integrate-with-vnet.md)，或[App Service 環境的](environment/intro.md)連線到內部部署資源。 |
| 限制暴露於傳入的網路流量 | 網路安全性群組可讓您限制網路存取，並控制公開的端點數目。 如需詳細資訊，請參閱 <<c0> [ 方式來控制輸入流量至 App Service Environment](environment/app-service-app-service-environment-control-inbound-traffic.md)。 |

## <a name="monitoring"></a>監視

| 建議 | 註解 |
|-|-|
|使用 Azure 資訊安全中心標準層 | [Azure 資訊安全中心](../security-center/security-center-app-services.md)搭配 Azure App Service 會原生整合。 它可以執行評量，並提供安全性建議。 |

## <a name="next-steps"></a>後續步驟

請洽詢您的應用程式提供者，以查看是否有額外的安全性需求。 如需有關開發安全應用程式的詳細資訊，請參閱 <<c0> [ 安全開發文件](../security/abstract-develop-secure-apps.md)。
