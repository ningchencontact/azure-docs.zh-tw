---
title: 設定 Azure 的 Red Hat OpenShift 開發環境 |Microsoft Docs
description: 以下是使用 Microsoft Azure 的 Red Hat OpenShift 的必要條件。
services: openshift
keywords: red hat openshift 設定設定
author: TylerMSFT
ms.author: twhitney
ms.date: 05/10/2019
ms.topic: conceptual
ms.service: openshift
manager: jeconnoc
ms.openlocfilehash: 6e859f57f9b5f24ea2f0172f5aa35a60d9769f19
ms.sourcegitcommit: f013c433b18de2788bf09b98926c7136b15d36f1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2019
ms.locfileid: "65551713"
---
# <a name="set-up-your-azure-red-hat-openshift-dev-environment"></a>設定 Azure 的 Red Hat OpenShift 開發環境

若要建置並執行 Microsoft Azure 的 Red Hat OpenShift 的應用程式，您將需要：

* 購買 Azure 保留的虛擬機器執行個體。
* 安裝新版 2.0.65 （或更新版本） 的 Azure CLI （或使用 Azure Cloud Shell）。
* 報名`openshiftmanagedcluster`功能和相關聯的資源提供者。
* 建立 Azure Active Directory (Azure AD) 租用戶。
* 建立 Azure AD 應用程式物件。
* 建立 Azure AD 使用者。

下列指示將引導您完成所有這些必要條件。

## <a name="purchase-azure-virtual-machine-reserved-instances"></a>購買 Azure 保留的虛擬機器執行個體

您可以使用 Azure 的 Red Hat OpenShift 之前，您必須購買 Azure 保留的虛擬機器執行個體。

如果您是 Azure 的客戶，這裡的如何[採購 Azure 虛擬機器保留執行個體](https://aka.ms/openshift/buy)。 保留可減少您預先支付受完整管理的 Azure 服務費用。 請參閱[*什麼是 Azure 保留*](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)若要進一步了解保留項目，以及如何它們節省成本。

如果您不是 Azure 的客戶[連絡銷售員](https://aka.ms/openshift/contact-sales)並填妥頁面底部的啟動程序的銷售表單。

## <a name="install-the-azure-cli"></a>安裝 Azure CLI

Azure 的 Red Hat OpenShift 需要版本 2.0.65 或更高版本的 Azure cli。 如果您已經安裝 Azure CLI，您可以檢查您的執行哪一個版本：

```bash
az --version
```

輸出的第一行會有的 CLI 版本，例如`azure-cli (2.0.65)`。

以下是指示[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)如果您需要新的安裝或升級。

或者，您可以使用[Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)。 在使用 Azure Cloud Shell 時，務必要選取**Bash**環境，如果您打算遵循[建立和管理 Azure 的 Red Hat OpenShift 叢集](tutorial-create-cluster.md)教學課程系列。

## <a name="register-providers-and-features"></a>註冊提供者和功能

`Microsoft.ContainerService openshiftmanagedcluster`功能， `Microsoft.Solutions`，和`Microsoft.Network`您的訂用帳戶，以手動方式部署您的第一個 Azure Red Hat OpenShift 叢集之前，必須註冊提供者。

若要手動註冊這些提供者和功能，請使用下列指示從 Bash 殼層，如果您已安裝 CLI，或從 Azure Cloud Shell (Bash) 工作階段在 Azure 入口網站中：

1. 如果您有多個 Azure 訂用帳戶，指定相關訂用帳戶識別碼：

    ```bash
    az account set --subscription <SUBSCRIPTION ID>
    ```

2. 註冊 Microsoft.ContainerService openshiftmanagedcluster 功能：

    ```bash
    az feature register --namespace Microsoft.ContainerService -n openshiftmanagedcluster
    ```

3. 註冊 Microsoft.Solutions 提供者：

    ```bash
    az provider register -n Microsoft.Solutions --wait
    ```

4. 註冊 Microsoft.Network 提供者：

    ```bash
    az provider register -n Microsoft.Network --wait
    ```

5. 註冊 Microsoft.KeyVault 提供者：

    ```bash
    az provider register -n Microsoft.KeyVault --wait
    ```

6. 重新整理 Microsoft.ContainerService 資源提供者註冊：

    ```bash
    az provider register -n Microsoft.ContainerService --wait
    ```

## <a name="create-an-azure-active-directory-azure-ad-tenant"></a>建立 Azure Active Directory (Azure AD) 租用戶

Azure Red Hat OpenShift 服務所需的相關聯的 Azure Active Directory (Azure AD) 租用戶，Microsoft 代表您的組織和其關聯性。 Azure AD 租用戶，可讓您註冊、 建置和管理應用程式，以及使用其他 Azure 服務。

如果您沒有 Azure AD 作為租用戶用於 Azure 的 Red Hat OpenShift 叢集，或您想要建立租用戶進行測試，請依照下列中的指示[建立 Azure AD 租用戶 Azure Red Hat OpenShift 叢集](howto-create-tenant.md)之前繼續使用本指南。

## <a name="create-an-azure-ad-user-security-group-and-application-object"></a>建立 Azure AD 使用者、 安全性群組和應用程式物件

Azure 的 Red Hat OpenShift 需要在您的叢集，例如設定存放裝置上執行工作的權限。 這些權限透過表示[服務主體](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object)。 您也會想要測試您的 Azure Red Hat OpenShift 叢集上執行的應用程式建立新的 Active Directory 使用者。

請依照下列中的指示[建立 Azure AD 應用程式物件和使用者](howto-aad-app-configuration.md)若要建立服務主體，產生您的應用程式的用戶端密碼和驗證回呼 URL，並建立新的 Azure AD 安全性群組和使用者存取叢集。

## <a name="next-steps"></a>後續步驟

您現在可以開始使用 Azure 的 Red Hat OpenShift ！

請嘗試本教學課程：
> [!div class="nextstepaction"]
> [建立 Azure Red Hat OpenShift 叢集](tutorial-create-cluster.md)

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli