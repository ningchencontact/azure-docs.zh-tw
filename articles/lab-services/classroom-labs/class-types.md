---
title: Azure 實驗室服務的範例課程類型 | Microsoft Docs
description: 提供一些您可以使用 Azure 實驗室服務為其設定實驗室的課程類型。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 09/30/2019
ms.author: spelluru
ms.openlocfilehash: 0e8b203240b4b2d6b67534ab52a7cd4ccf5df571
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2019
ms.locfileid: "71976590"
---
# <a name="class-types-overview---azure-lab-services"></a>課程類型概觀 - Azure 實驗室服務
Azure 實驗室服務可讓您在雲端中快速設定教室實驗室環境。 本節中的文章提供如何使用 Azure 實驗室服務設定數種課堂實驗室的指引。

## <a name="deep-learning-in-natural-language-processing"></a>自然語言處理中的深度學習
您可以使用 Azure 實驗室服務，設定以自然語言處理 (NLP) 中的深度學習為焦點的實驗室。 自然語言處理 (NLP) 是一種人工智慧 (AI) 形式，可讓電腦具有翻譯、語音辨識和其他語言理解功能。 參與 NLP 類別的學生可透過 Linux 虛擬機器 (VM) 了解如何運用神經網路演算法來開發深入學習模型，用以分析撰寫的人類語言。 

如需如何設定此類實驗室的詳細資訊，請參閱[設定以自然語言處理中的深度學習為焦點的實驗室](class-type-deep-learning-natural-processing.md)。

## <a name="shell-scripting-on-linux"></a>Linux 上的殼層指令碼
您可以設定實驗室來教授 Linux 上的殼層指令碼。 指令碼是系統管理中很有用的部分，可讓管理員避免重複的工作。 在此範例案例中，課程涵蓋了傳統的 Bash 指令碼和增強型指令碼。 增強型指令碼是結合了 Bash 命令與 Ruby 的指令碼。 這種方法可讓 Ruby 傳遞資料，以及讓 Bash 命令與殼層互動。 

參與這些指令碼課程的學生可透過 Linux 虛擬機器了解 Linux 的基本概念，同時也能熟悉 Bash 殼層指令碼。 此 Linux 虛擬機器已啟用遠端桌面存取，且已安裝 [gedit](https://help.gnome.org/users/gedit/stable/) 和 [Visual Studio Code](https://code.visualstudio.com/) 文字編輯器。

如需如何設定此類實驗室的詳細資訊，請參閱 [Linux 上的殼層指令碼](class-type-shell-scripting-linux.md)。

## <a name="ethical-hacking"></a>道德入侵 
您可以針對專注在道德入侵之鑑識調查方面的課程設定實驗室。 滲透測試 (由道德入侵社群所使用的作法) 會在有人嘗試取得系統或網路的存取權，以示範惡意攻擊者可能會利用的弱點時發生。 

在道德入侵課程中，學生可以學習防禦弱點的現代化技術。 每個學生都會有 Windows Server 主機虛擬機器，該虛擬機器會有兩部巢狀虛擬機器，其中一部具有 **Metaspoiltable** 映像，另一部則具有 [Kali Linux](https://www.kali.org/) \(英文\) 映像。 Metasploitable 虛擬機器會用於盜用目的，而 Kali 虛擬機器則提供執行鑑識調查工作所需之工具的存取。

如需如何設定此類實驗室的詳細資訊，請參閱[設定實驗室以指導道德入侵課程](class-type-ethical-hacking.md)。

## <a name="next-steps"></a>後續步驟
請參閱下列文章： 

- [使用 Azure 實驗室服務，設定以自然語言處理中的深度學習為焦點的實驗室](class-type-deep-learning-natural-processing.md)
- [Linux 上的殼層指令碼](class-type-shell-scripting-linux.md)