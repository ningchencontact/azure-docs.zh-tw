---
title: 加入資料列：模組參考
titleSuffix: Azure Machine Learning service
description: 了解如何使用 Azure Machine Learning 服務中加入資料列模組串連兩個資料集。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ffd693ea3452ef48dc3e05e7bc4a6d3988a487b0
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028751"
---
# <a name="add-rows-module"></a>新增資料列的模組

本文說明 Azure Machine Learning 服務的視覺化介面 （預覽） 的模組。

您可以使用此模組來串連兩個資料集。 在串連的第二個資料集的資料列會加入至第一個資料集的結尾。  
  
串連的資料列是在下列情況很有用：  
  
+ 您已經產生一系列評估統計資料，和您想要將它們結合成一個資料表，以更輕鬆的報告。  
  
+ 您已使用不同的資料集，以及您想要合併的資料集來建立最終資料集。  

## <a name="how-to-use-add-rows"></a>如何使用 加入資料列  

若要串連兩個資料集的資料列，資料列必須有完全相同的結構描述。 這表示，相同數目的資料行，而且相同的資料行中的資料類型。

1.  拖曳**加入資料列**到您的實驗的模組，您可以找到下**Data Transformation**，請在**操控**類別目錄。

2. 兩個輸入連接埠來連接資料集。 您想要附加的資料集應該連接到第二個 （右） 的連接埠。 
  
3.  執行實驗。 輸出資料集中的資料列數目應該等於兩個輸入資料集的資料列的總和。

    如果您將加入的兩個輸入的相同資料集**加入資料列**重複模組，將資料集。 

## <a name="next-steps"></a>後續步驟

請參閱[可用的模組集](module-reference.md)Azure Machine Learning 服務。 