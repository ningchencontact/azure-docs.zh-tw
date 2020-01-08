---
title: Azure Marketplace 中的受控服務供應項目
description: 受控服務供應項目能允許服務提供者在 Azure Marketplace 中向客戶銷售資源管理供應項目。
ms.date: 12/16/2019
ms.topic: conceptual
ms.openlocfilehash: 1b4f0d7457a74afe710a48f429cfe47535a9b122
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453576"
---
# <a name="managed-services-offers-in-azure-marketplace"></a>Azure Marketplace 中的受控服務供應項目

此文章說明 [Azure Marketplace](https://azuremarketplace.microsoft.com) 中的新 [受控服務] 供應項目類型。 受控服務供應項目可讓您搭配 Azure 委派的資源管理向客戶提供資源管理服務。 您可以向所有潛在客戶提供這些供應項目，或僅向一或多個特定客戶提供。 由於您會直接向客戶收取與這些受控服務相關的費用，因此 Microsoft 不會向您收取任何費用。

## <a name="understand-managed-services-offers"></a>了解受控服務供應項目

受控服務能簡化針對 Azure 委派的資源管理將客戶上線的程序。 當客戶在 Azure Marketplace 中購買供應項目時，他們將能夠指定應上架的訂用帳戶和 (或) 資源群組。 請注意，必須手動註冊 **Microsoft.ManagedServices** 資源提供者，訂用帳戶才能先獲得上線授權。

之後，貴組織中的使用者能夠根據您在 [Cloud Partner 入口網站](https://cloudpartner.azure.com/)中建立供應項目時所定義的存取權，在貴組織的租用戶中執行這些資源的管理工作。 這是透過資訊清單完成，該資訊清單可指定能使用 Azure 委派資源管理來存取客戶資源的 Azure AD 使用者、群組及服務主體，以及能定義其存取層級的角色。 透過將權限指派給 Azure AD 群組，而非一系列個別使用者或應用程式帳戶，您便可以在需要變更存取權時個別地加入或移除使用者。

## <a name="public-and-private-offers"></a>公用和私人供應項目

每個受控服務供應項目都包含一或多個方案。 方案可以是私用或公用。 

如果您想要將供應項目限制為僅提供特定客戶使用，則可以發佈私人方案。 當您這麼做時，該方案只能針對由您提供的特定訂用帳戶識別碼購買。 如需詳細資訊，請參閱[私人供應項目](../../marketplace/private-offers.md) \(部分機器翻譯\)。

公用方案能讓您將服務推廣給新的客戶。 當您僅需要客戶租用戶的有限存取權時，這通常是較為適合的選擇。 當您與客戶建立關聯性之後，如果他們決定將額外存取權授與您的組織，您可以僅為該客戶發佈新的私人方案，或是[使用 Azure Resource Manager 範本將他們上線以取得進一步存取](../how-to/onboard-customer.md)。

如有需要，您可以在相同的供應項目中同時包含公用與私人方案。

> [!IMPORTANT]
> 方案發行為公開之後，您就無法將它變更為私用。 若要控制哪些客戶可以接受您的供應專案和委派資源，請使用私用方案。 有了公用方案，您就不能將可用性限制在特定客戶或甚至是特定數目的客戶（不過，如果您選擇這樣做，則可以完全停止銷售方案）。 當客戶接受供應專案之後，目前沒有任何機制可以拒絕或移除委派，雖然您可以隨時聯繫客戶，並要求他們[移除您的存取權](../how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers)。

## <a name="publish-managed-service-offers"></a>發佈受控服務供應項目

若要了解如何發佈受控服務供應項目，請參閱[將受控服務供應項目發佈到 Azure Marketplace](../how-to/publish-managed-services-offers.md)。 如需使用 Cloud Partner 入口網站發佈至 Azure Marketplace 的一般資訊，請參閱 [Azure Marketplace 和 AppSource 發行指南](../../marketplace/marketplace-publishers-guide.md) \(部分機器翻譯\) 和[管理 Azure 與 AppSource Marketplace 供應項目](../../marketplace/cloud-partner-portal/manage-offers/cpp-manage-offers.md) \(部分機器翻譯\)。

## <a name="next-steps"></a>後續步驟

- 了解 [Azure 委派的資源管理](azure-delegated-resource-management.md)與[跨租用戶管理體驗](cross-tenant-management-experience.md)。
- [將受控服務供應項目發佈](../how-to/publish-managed-services-offers.md)到 Azure Marketplace。
