---
title: 設定和管理 Azure Notebook 專案
description: 如何透過 Azure Notebooks UI 與直接終端機存取來管理專案中繼資料、專案檔、專案的環境和設定步驟。
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 35dd6ff1-a14a-4a2e-b173-6d8467de3e89
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2019
ms.author: kraigb
ms.openlocfilehash: d1f94c5fd774b51f57da2885d1ccd8eb909cd3c0
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2019
ms.locfileid: "59268002"
---
# <a name="manage-and-configure-projects"></a>管理及設定專案

Azure Notebooks 中的專案本質上是基礎 Linux 虛擬機器 (Jupyter Notebook 執行所在) 的組態，以及檔案資料夾和描述性中繼資料。 Azure Notebooks 的專案儀表板可讓您管理檔案，或者設定專案的特性：

- 專案要在哪個計算層上執行，該層可以為免費層或 Azure 虛擬機器。
- 專案中繼資料包括名稱、描述、共用專案時使用的識別碼，以及專案為公用或私用。
- 專案的筆記本、資料和其他檔案，管理它們就和在其他檔案系統中管理一樣。
- 專案的環境，您可以透過啟動指令碼或直接透過終端機來管理。
- 記錄檔，您可以透過終端機存取。

> [!Note]
> 此處所述的管理和設定功能僅適用於最初建立專案的專案擁有者。 不過，您可以將專案複製到您自己的帳戶中，在此情況下您會成為擁有者，並可以視需要設定專案。

每當您執行 Notebook 或其他檔案時，Azure Notebooks 會啟動基礎虛擬機器。 伺服器會自動儲存檔案，並且在處於非使用狀態 60 分鐘之後關閉。 您也可以使用 [關機] 命令 (鍵盤快速鍵：h)，隨時停止伺服器。

## <a name="compute-tier"></a>計算層

專案儀表板上的 [執行] 下拉式清單是您選取專案要在上面執行之計算層的位置。 根據預設，專案是在**免費計算**層上執行，受限為 4GB 的記憶體和 1GB 的資料，以防止不當使用：

![專案儀表板上的計算層下拉式清單](media/project-compute-tier-list.png)

您可以藉由使用已佈建在 Azure 訂用帳戶中的其他虛擬機器來繞過這些限制。 您必須在該虛擬機器上安裝並執行 JupyterHub。 「資料科學虛擬機器」映像 (任何作業系統) 是很好的選擇，因為它們預設就包含 JupyterHub。

在您有了適當設定的 Azure 虛擬機器之後，請從下拉式清單中選取 [Direct Compute] \(直接計算\) 選項，此選項會提示您輸入名稱 (會在清單中顯示)、VM 的 IP 位址和連接埠 (通常是 8000，這是 JupyterHub 接聽的預設連接埠)，以及 VM 認證：

![收集 [直接計算] 選項伺服器資訊的提示](media/project-compute-tier-direct.png)

如果下列條件成立，下拉式清單也會顯示[資料科學虛擬機器 (DSVM)](/azure/machine-learning/data-science-virtual-machine) 執行個體。 (如果未符合這些條件中的任一個，您仍然可以使用 [直接連線] 選項連線到 DSVM，並輸入從 Azure 入口網站取得的值。)

- 您以使用 Azure Active Directory (AAD) 的帳戶 (如公司帳戶) 登入 Azure Notebooks。
- 您的帳戶已連接到 Azure 訂用帳戶。
- 您在該訂用帳戶中有一或多個虛擬機器 (至少有「讀者」存取權)，這些虛擬機器使用「適用於 Linux (Ubuntu) 的資料科學虛擬機器」映像。

![專案儀表板上的下拉式清單中的資料科學虛擬機器執行個體](media/project-compute-tier-dsvm.png)

當您選取 DSVM 執行個體時，Azure Notebooks 可能會提示您輸入您在建立 VM 時使用的特定機器認證。

若要建立新的 DSVM 執行個體，請依照[建立 Ubuntu 資料科學 VM](/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro) 中的指示執行。 如果您希望 DSVM 出現在 Azure Notebooks 的下拉式清單中，請使用**適用於 Linux (Ubuntu) 的資料科學虛擬機器**映像。  如果基於其他原因，您需要使用 Windows 或 CentOS 映像，您可以使用 [Direct Compute] \(直接計算\) 選項來手動連線至 DSVM。

> [!IMPORTANT]
> 使用直接計算或資料科學虛擬機器時，您在其執行的 notebook 必須是完全獨立的。 目前，Azure Notebooks 只複製 *.ipynb*至 VM 的檔案，但不複製任何其他檔案專案中。 如此一來，其他 Vm 上執行的 notebook 會找不到 其他專案檔案。
>
> 您可以解決這個問題有兩種：
>
> 1. 手動將專案檔複製到 VM。
>
> 2. 內嵌安裝 notebook 中的檔案，您先執行之前的主要的 notebook。 在安裝 notebook 中，建立每個檔案其中的資料格包含檔案內容的程式碼儲存格。 然後，在每個資料格的頂端插入命令`%%writefile <filename>`，其中`<filename>`是要接收內容的檔案名稱。 當您執行 notebook 時，它會建立在 VM 上的所有這些檔案。 如需範例，請參閱[setup.ipynb 檔案，在 Microsoft 寵物偵測器示範](https://github.com/Microsoft/connect-petdetector/blob/master/setup.ipynb)(GitHub)。
>
>     ![使用 %%writefile 命令的程式碼儲存格的開頭](media/setup-notebook-writefile-command.png)

## <a name="edit-project-metadata"></a>編輯專案中繼資料

在專案儀表板中，選取 [專案設定]，然後選取 [資訊] 索引標籤，其中包含如下表所述的專案中繼資料。 您可以隨時變更專案中繼資料。

| 設定 | 描述 |
| --- | --- |
| 專案名稱 | Azure Notebooks 用於顯示用途的專案易記名稱。 例如，"Hello World in Python"。 |
| 專案識別碼 | 自訂識別碼會成為您用來共用專案 URL 的一部分。 此識別碼可以使用字母、 數字和連字號、 限制為 30 個字元，並不能[保留的專案 ID](create-clone-jupyter-notebooks.md#reserved-project-ids)。 如果您不確定要使用的項目，常見的慣例是使用專案名稱的小寫版本，讓空格轉變成連字號，例如 「 my-notebook-project 」 (必要時，可截斷以符合長度限制)。 |
| 公用專案 | 如果設定，可讓任何人存取的專案連結。 建立私人專案時，請清除此選項。 |
| 隱藏複製 | 如果設定，其他使用者無法看到為此專案所做的複製清單。 隱藏複製對於與並非屬於相同組織的許多人員共用的專案相當有用，例如在使用 Notebook 來教授課程時。 |

> [!Important]
>
> 變更專案識別碼會導致連結到您先前共用的專案的任何連結無效。

## <a name="manage-project-files"></a>管理專案檔

專案儀表板會顯示專案資料夾系統的內容。 您可以使用各種命令來管理這些檔案。

### <a name="create-new-files-and-folders"></a>建立新的檔案和資料夾

[+ 新增] 命令 (鍵盤快速鍵：n) 會建立新的檔案或資料夾。 使用命令時，請先選取要建立的項目類型：

| 項目類型 | 描述 | 命令行為 |
| --- | --- | --- |
| **筆記本** | Jupyter Notebook | 顯示快顯，您可以在其中指定 Notebook 的檔案名稱和語言。 |
| **資料夾** | 子資料夾 | 在專案的檔案清單中建立及編輯欄位，在該欄位中輸入資料夾名稱。 |
| **空白的檔案** | 您可以在空白檔案中儲存任何內容，例如文字、資料等等。 | 在專案的檔案清單中建立及編輯欄位，在該欄位中輸入檔案名稱。 |
| **Markdown** | Markdown 檔案。 | 在專案的檔案清單中建立及編輯欄位，在該欄位中輸入檔案名稱。 |

### <a name="upload-files"></a>上傳檔案

[上傳] 命令提供從其他位置匯入資料的兩個選項：[從 URL] 和 [從電腦]。 如需詳細資訊，請參閱[使用 Azure Notebook 專案中的資料檔案](work-with-project-data-files.md)。

### <a name="select-file-specific-commands"></a>選取特定檔案的命令

專案檔案清單中的每個項目都會透過以滑鼠右鍵按一下內容功能表來提供命令：

![檔案內容功能表上的命令](media/project-file-commands.png)

| 命令 | 鍵盤快速鍵 |  動作 |
| --- | --- | --- |
| 執行 | r (或按一下) | 執行 Notebook 檔案。 其他檔案類型開啟供檢視。  |
| 複製連結 | y | 將檔案的連結複製到剪貼簿。 |
| 在 Jupyter 實驗室中執行 | j | 在 JupyterLab 中執行 Notebook，這是比 Jupyter 通常會提供的介面更加偏向開發人員導向的介面。 |
| 預覽 | p | 開啟檔案的 HTML 預覽；針對 Notebook，預覽是 Notebook 的唯讀轉譯。 如需詳細資訊，請參閱[預覽](#preview)一節。 |
| 編輯檔案 | i | 開啟檔案進行編輯。 |
| 下載 | d | 下載 zip 檔案，其中包含檔案或資料夾的內容。 |
| 重新命名 | a | 提示您輸入檔案或資料夾的新名稱。 |
| 刪除 | x | 提示您確認，然後從專案中永久移除檔案。 刪除無法復原。 |
| 移動 | m | 將檔案移到相同專案中不同的資料夾。 |

#### <a name="preview"></a>預覽

檔案或 Notebook 的預覽是內容的唯讀檢視；已停用執行 Notebook 資料格。 預覽會向具有檔案或 Notebook 連結但是未登入 Azure Notebooks 的任何人顯示。 登入之後，使用者可以將 Notebook 複製到他們自己的帳戶，或是將 Notebook 下載到其本機電腦。

預覽頁面支援數個工具列命令，有鍵盤快速鍵：

| 命令 | 鍵盤快速鍵 |  動作 |
| --- | --- | --- |
| 共用 | s | 顯示共用的快顯，您可以從其中取得連結、分享到社交媒體、取得內嵌的 HTML，以及傳送電子郵件。 |
| 複製 | c  | 將 Notebook 複製到您的帳戶。 |
| 執行 | r | 如果可以，請執行 Notebook。 |
| 下載 | d | 下載 Notebook 的複本。 |

## <a name="configure-the-project-environment"></a>設定專案環境

有三種方式可設定您 Notebook 執行所在的基礎虛擬機器環境：

- 包含單次初始化指令碼
- 使用專案的環境設定來指定設定步驟
- 透過終端機來存取虛擬機器。

每當啟動虛擬機器時，就會套用所有形式的專案組態，因此會影響專案內的所有 Notebook。

### <a name="one-time-initialization-script"></a>單次初始化指令碼

Azure Notebooks 第一次為專案建立伺服器時，它會在名為 aznbsetup.sh 的專案中尋找檔案。如果此檔案存在，Azure Notebooks 會執行它。 指令碼的輸出會在您的專案資料夾中儲存為 .aznbsetup.log。

### <a name="environment-setup-steps"></a>環境設定步驟

您可以使用專案的環境設定，來建立設定環境的個別步驟。

在專案儀表板中，選取 [專案設定]，然後選取 [環境] 索引標籤，在其中新增、移除和修改專案的設定步驟：

![專案設定會快顯，且已選取 [環境] 索引標籤](media/project-settings-environment-steps.png)

若要新增步驟，請先選取 [+ 新增]，然後在 [作業] 下拉式清單中選取步驟類型：

![新環境設定步驟的作業選取器](media/project-settings-environment-details.png)

專案的資訊取決於您選擇的作業類型：

- **Requirements.txt**：在第二個下拉式清單中，選取已在專案中的 requirements.txt 檔案。 然後從出現的第三個下拉式清單中選取 Python 版本。 藉由使用 requirements.txt 檔案，Azure Notebooks 會在啟動 Notebook 伺服器時執行 `pip install -r` 與 requirements.txt 檔案。 您不需要明確安裝 Notebook 本身內的套件。

- **Shell 指令碼**：在第二個下拉式清單中，在專案中選取 bash shell 指令碼 (通常是具有 .sh 副檔名的檔案)，其中包含您希望執行以初始化環境的任何命令。

- **Environment.yml**：在第二個下拉式清單中，針對使用 conda 環境的 Python 專案選取 environments.yml 檔案。

當您完成新增步驟時，選取 [儲存]。

### <a name="use-the-terminal"></a>使用終端機

在專案儀表板上，[終端機] 命令會開啟 Linux 終端機，可讓您直接存取伺服器。 在終端機中，您可以下載資料、編輯或管理檔案、檢查處理序，甚至是使用 vi 和 nano 等工具。

> [!Note]
> 如果您在專案環境中具有啟動指令碼，開啟終端機可能會顯示訊息，表示設定仍在進行中。

您可以在終端機中發出任何標準 Linux 命令。 您也可以在主資料夾中使用 `ls`，以查看虛擬機器上存在的不同環境，例如 *anaconda2_501*、*anaconda3_420*、*anaconda3_501*、*IfSharp* 和 *R*，以及包含專案的 project 資料夾：

![Azure Notebooks 中的專案終端機](media/project-terminal.png)

若要影響特定環境，請先將目錄變更到該環境資料夾。

對於 Python 環境，您可以在每個環境的 bin 資料夾中找到 `pip` 和 `conda`。 您也可以使用環境的內建別名：

```bash
# Anaconda 2 5.3.0/Python 2.7: python27
python27 -m pip install <package>

# Anaconda 3 4.2.0/Python 3.5: python35
python35 -m pip install <package>

# Anaconda 3 5.3.0/Python 3.6: python36
python36 -m pip install <package>
```

對伺服器所做的變更僅會套用至目前工作階段，除了您在 project 資料夾本身中建立的檔案和資料夾以外。 例如，在專案資料夾中編輯檔案會在工作階段之間保存，但是具有 `pip install` 的套件則否。

> [!Note]
> 如果您使用 `python` 或 `python3`，則會叫用系統安裝的 Python 版本，該版本不會用於 Notebook。 您也沒有 `pip install` 之類作業的權限，因此請務必使用版本特定別名。

## <a name="access-notebook-logs"></a>存取 Notebook 記錄

如果您在執行 Notebook 時遇到問題，Jupyter 的輸出會儲存在名為 .nb.log 的資料夾中。 您可以透過 [終端機] 命令或專案儀表板來存取這些記錄。

通常當您在本機執行 Jupyter 時，可能已經從終端機視窗啟動它。 終端機視窗會顯示輸出，例如核心狀態。

若要檢視記錄，在終端機中使用下列命令：

```bash
cat .nb.log
```

您也可以從 Python Notebook 中的程式碼資料格使用命令：

```bash
!cat .nb.log
```

## <a name="next-steps"></a>後續步驟

- [作法：使用專案資料檔案](work-with-project-data-files.md)
- [在 Notebook 中存取雲端資料](access-data-resources-jupyter-notebooks.md)
