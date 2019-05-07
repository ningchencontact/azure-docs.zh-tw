---
title: 設定適用於 IoT 的 Azure 資訊安全中心解決方案 (預覽) | Microsoft Docs
description: 了解如何使用適用於 IoT 的 Azure 資訊安全中心來設定端對端 IoT 解決方案。
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: ae2207e8-ac5b-4793-8efc-0517f4661222
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: c60b421e9b60c6a2191fe2be189d1abd1c328f24
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65200785"
---
# <a name="quickstart-configure-your-iot-solution"></a>快速入門：設定 IoT 解決方案

> [!IMPORTANT]
> 適用於 IoT 的 Azure 資訊安全中心目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本文說明如何使用適用於 IoT 的 ASC 來執行 IoT 安全性解決方案的初始設定。 

## <a name="azure-security-center-asc-for-iot"></a>適用於 IoT 的 Azure 資訊安全中心 (ASC)

適用於 IoT 的 ASC 可為 Azure 型 IoT 解決方案提供完整的端對端安全性。

使用適用於 IoT 的 ASC 時，您便可以在一個儀表板內監視整個 IoT 解決方案，裡面會顯示您在 Azure 中的所有 IoT 裝置、IoT 平台和後端資源。

在 IoT 中樞上啟用後，適用於 IoT 的 ASC 就會自動識別其他同樣連線至 IoT 中樞，並與 IoT 解決方案相關的 Azure 服務。

除了自動偵測關聯性外，您還可以挑出並選擇要將哪些其他 Azure 資源標記為 IoT 解決方案的一部分。
您所做的選擇會讓您可以新增整個訂用帳戶、資源群組或單一資源。

在定義所有資源關聯性後，適用於 IoT 的 ASC 便會利用 Azure 資訊安全中心來為這些資源提供安全性建議和警示。

## <a name="add-azure-resources-to-your-iot-solution"></a>將 Azure 資源新增至 IoT 解決方案

若要將新的資源新增至 IoT 解決方案，請執行下列步驟： 

1. 在 Azure 入口網站中開啟您的 **IoT 中樞**。 
2. 從左側功能表中選取並開啟 [安全性] 下方的 [資源]。 
3. 選取 [新增資源]。
4. 選擇屬於您 IoT 解決方案的資源。
5. 按一下 [新增] 。 

恭喜！ 您已將新的資源新增至 IoT 解決方案。

現在，適用於 IoT 的 ASC 會監視您剛新增了資源，並且會在 IoT 解決方案內顯示相關的安全性建議和警示。

## <a name="next-steps"></a>後續步驟

請前往下一篇文章，以了解如何建立安全性模組...

> [!div class="nextstepaction"]
> [建立安全性模組](quickstart-create-security-twin.md)