---
title: Azure Machine Learning 和 FPGA
description: 了解如何使用 FPGA 為模型和深度類神經網路加速。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 05/07/2018
ms.openlocfilehash: 493b3f8de617146af534349e18ed49b83c46dee8
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
---
# <a name="what-is-a-field-programmable-gate-array-fpga"></a>什麼是現場可程式化閘陣列 (FPGA)？

現場可程式化閘陣列 (FPGA) 是可以視需要而重新設定的積體電路。 您可以視需要而變更 FPGA，以實作新的邏輯。 Azure Machine Learning 硬體加速模型可讓您將定型的模型部署到 Azure 雲端中的 FPGA。

此功能是由 Project Brainwave 提供技術支援，可處理將深度類神經網路 (DNN) 轉譯為 FPGA 程式。 

## <a name="why-use-an-fpga"></a>為什麼要使用 FPGA？

FPGA 提供超低延遲，而且對於批次大小 (不需要大的批次大小) 的計分資料特別有效。  它們符合成本效益且適用於 Azure。  Project Brainwave 可以在這些 FPGA 上平行處理預先定型的 DNN 以擴充您的服務。

## <a name="next-steps"></a>後續步驟

[在 FPGA 上將模型部署為 Web 服務](how-to-deploy-fpga-web-service.md)

[了解如何安裝 FPGA SDK](reference-fpga-package-overview.md)