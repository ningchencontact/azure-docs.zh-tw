---
title: 適用於使用 Ansible 搭配 Azure 的工具
description: 安裝並使用適用於 Ansible 搭配 Azure 的個別工具
ms.service: ansible
keywords: ansible, azure, devops, 工具, vs code, visual studio code, 擴充功能
author: tomarcher
manager: routlaw
ms.author: tarcher
ms.date: 01/14/2018
ms.topic: article
ms.openlocfilehash: 73d31054bb0c40dd08bf4b4a93c31c59354b5bed
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2018
---
# <a name="tools-for-using-ansible-with-azure"></a>適用於使用 Ansible 搭配 Azure 的工具

下列工具可讓您更輕鬆且更有效率地使用 Ansible 和 Azure。

## <a name="visual-studio-code-extension-for-ansible"></a>適用於 Ansible 的 Visual Studio Code 擴充功能

[適用於 Ansible 的 Visual Studio Code 擴充功能](https://marketplace.visualstudio.com/items?itemName=vscoss.vscode-ansible)提供數個功能，可從 Visual Studio Code 使用 Ansible：

- 在您輸入時，自動完成 Ansible 文件中的 Ansible 指示詞、模組和增益集。
- 在撰寫 Ansible 腳本時，按一下 &lt;Ctrl>&lt;空白鍵> 可顯示程式碼片段。
- 語法反白顯示會根據 YAML 語法，以不同的色彩和字型顯示您 Ansible 腳本的程式碼。
- 可以從 Visual Studio Code 終端機視窗中執行 Ansible 腳本。
    - (僅限 Windows) 可以從 Docker 容器執行 Ansible。
    - (Linux 及 macOS) 可以從 Docker 容器或從本機 Ansible 安裝來執行 Ansible。 
- 可以從 Azure Cloud Shell 中執行 Ansible 腳本。