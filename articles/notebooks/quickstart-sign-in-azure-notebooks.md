---
title: 登入 Azure Notebooks
description: 快速登入 Azure Notebooks 並設定使用者識別碼，如此可讓您存取己儲存的專案並與他人供用 Notebooks。
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: fb8c94b1-6d0a-4b77-8d14-ae6efcdd99f4
ms.service: notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: f595fbb6571c3deaaa0644ba9d62f598181592e3
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2018
ms.locfileid: "53106306"
---
# <a name="quickstart-sign-in-and-set-a-user-id"></a>快速入門：登入並設定使用者識別碼

雖然您不用登入就可以檢視 Azure Notebooks，但是您必須登入以執行 Notebook、存取已儲存的專案和 Notebook，以及與其他人共用您的 Notebook。

## <a name="sign-in"></a>登入

1. 選取 [notebooks.azure.com](https://notebooks.azure.com/) 右上方的 [登入]。

    ![Azure Notebooks 登入命令的位置](media/accounts/sign-in-command.png)

1. 出現提示時，請輸入 Microsoft 帳戶或工作或學校帳戶的電子郵件地址，然後選取 [下一步]。 帳戶類型會在[您的 Azure Notebooks 使用者帳戶](azure-notebooks-user-account.md)上說明。 如果您沒有 Microsoft 帳戶，或想要申請一個專門用於 Azure Notebooks 的帳戶，請選取 [建立帳戶]：

    ![在登入提示中建立新的 Microsoft 帳戶命令](media/accounts/create-new-microsoft-account.png)

1. 在系統提示時輸入您的密碼。

1. 如果您是第一次登入，Azure Notebooks 會要求存取您帳戶的權限。 選取 [是] 以繼續進行：

    ![帳戶權限提示](media/accounts/account-permission-prompt.png)

## <a name="set-a-user-id"></a>設定使用者識別碼

1. 在第一次登入時，您會獲得指派暫時使用者識別碼，例如 "anon-idrca3"。 只要您的使用者識別碼開頭是 "anon-"，Azure Notebooks 就會提示您建立自己的識別碼。 您的使用者識別碼會在您取得以共用專案和 Notebook 的任何 URL 中使用，因此請選擇唯一且有意義的識別碼。

    ![提示輸入 Azure Notebooks 的使用者識別碼](media/accounts/create-user-id.png)

    如果您選取 [不用了，謝謝]，Azure Notebooks 會持續在您每一次登入時提示您輸入使用者識別碼。 您也可以隨時在您的[使用者設定檔](azure-notebooks-user-profile.md)中設定使用者識別碼。

1. 成功登入之後，Azure Notebooks 會將您導覽至公用設定檔頁面，您可以在其中選取 [編輯設定檔資訊] 以填入其餘資訊 (如需詳細資訊，請參閱[您的設定檔和使用者識別碼](azure-notebooks-user-profile.md))：

    ![Azure Notebooks 設定檔頁面的初始檢視](media/accounts/profile-page-new.png)

## <a name="sign-out"></a>登出

若要登出，請在頁面右上方選取您的使用者名稱，然後選取 [登出]：

![Azure Notebooks 登出命令的位置](media/accounts/sign-out-command.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [快速入門：建立及共用 Notebook](quickstart-create-share-jupyter-notebook.md)
