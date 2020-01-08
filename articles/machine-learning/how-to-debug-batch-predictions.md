---
title: ParallelRunStep 的調試和疑難排解
titleSuffix: Azure Machine Learning
description: 在適用于 Python 的 Azure Machine Learning SDK 中，針對機器學習管線進行 Debug 和疑難排解。 瞭解使用管線進行開發的常見陷阱，以及協助您在遠端執行之前和期間進行腳本的秘訣。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye, jmartens, larryfr, vaidyas
ms.author: trmccorm
author: tmccrmck
ms.date: 11/21/2019
ms.openlocfilehash: 20e88da6721a6715a7f31f5c423e305625528383
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/28/2019
ms.locfileid: "75540603"
---
# <a name="debug-and-troubleshoot-using-parallelrun"></a>使用 ParallelRun 進行調試和疑難排解
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將瞭解如何從[AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)進行[ParallelRunStep](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallel_run_step.parallelrunstep?view=azure-ml-py)類別的調試和疑難排解。

## <a name="testing-scripts-locally"></a>在本機測試腳本

如需機器學習管線，請參閱在[本機測試腳本一節](how-to-debug-pipelines.md#testing-scripts-locally)。 您的 ParallelRunStep 會在 ML 管線中以步驟的方式執行，因此相同的答案也適用于兩者。

## <a name="debugging-scripts-from-remote-context"></a>從遠端內容調試腳本

從本機對計分腳本進行的轉換，到在實際管線中對計分腳本進行的程式碼，可能是一項艱難的飛躍。 如需在入口網站中尋找記錄檔的相關資訊，請查看[從遠端內容對腳本進行偵錯工具的機器學習管線一節](how-to-debug-pipelines.md#debugging-scripts-from-remote-context)。 該區段中的資訊也適用于批次推斷執行。

例如，記錄檔 `70_driver_log.txt` 也包含： 

* 腳本執行期間的所有列印語句。
* 腳本的堆疊追蹤。

由於批次推斷作業的分散本質，因此有來自數個不同來源的記錄。 不過，系統會建立兩個可提供高階資訊的合併檔案：

- `~/logs/overview.txt`：此檔案提供到目前為止所建立之迷你批次（也稱為工作）數目的高階資訊，以及到目前為止已處理的迷你批次數目。 在此結束時，它會顯示作業的結果。 如果作業失敗，則會顯示錯誤訊息，以及開始進行疑難排解的位置。

- `~/logs/master.txt`：此檔案會提供執行中作業的主要節點（也稱為協調器）。 包括工作建立、進度監視、執行結果。

當您需要完整瞭解每個節點執行分數腳本的方式時，請查看每個節點的個別進程記錄檔。 您可以在 [`worker`] 資料夾中找到進程記錄，依背景工作節點分組：

- `~/logs/worker/<ip_address>/Process-*.txt`：此檔案會提供每個迷你批次的詳細資訊，因為它是由背景工作所挑選或完成。 針對每個迷你批次，此檔案包含：

    - 工作者進程的 IP 位址和 PID。 
    - 專案總數和已成功處理的專案數。 
    - 時鐘時間的開始和結束時間（`start1` 和 `end1`）。 
    - 花費在處理器時間的開始和結束時間（`start2` 和 `end2`）。 

您也可以找到每個背景工作進程的資源使用量資訊。 這項資訊是 CSV 格式，位於 `~/logs/performance/<ip_address>/`。 例如，檢查資源使用量時，請查看下列檔案：

- `process_resource_monitor_<ip>_<pid>.csv`：每個工作者進程資源使用量。 
- `sys_resource_monitor_<ip>.csv`：每個節點記錄檔。

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>如何? 從遠端內容中的使用者腳本記錄嗎？
您可以使用下列步驟來設定記錄器，使記錄顯示在入口網站的 [**記錄/使用者**] 資料夾中：
1. 將下面的第一個程式碼區段儲存 entry_script_helper 到 .py 檔案中，並將檔案放在與您的輸入腳本相同的資料夾中。 這個類別會取得 AmlCompute 內的路徑。 對於本機測試，您可以變更 `get_working_dir()` 以傳回本機資料夾。
2. 在 `init()` 方法中設定記錄器，然後使用它。 以下是範例的第二個程式碼區段。 

**entry_script_helper .py：**
```python
"""
This module provides helper features for entry script.
This file should be in Python search paths or in the same folder as the entry script.
"""
import os
import socket
import logging
import time
from multiprocessing import current_process
from azureml.core import Run


class EntryScriptHelper:
    """ A helper to provide common features for entry script."""

    LOG_CONFIGED = False

    def get_logger(self, name="EntryScript"):
        """ Return a logger.
            The logger will write to the 'users' folder and show up in azure portal.
        """
        return logging.getLogger(name)

    def config(self, name="EntryScript", level="INFO"):
        """ Config a logger. This should be called in init() in score module.
            Config the logger one time if not configed.
            The logger will write to the 'users' folder and show up in azure portal.
        """
        logger = logging.getLogger(name)

        formatter = logging.Formatter(
            "%(asctime)s|%(name)s|%(levelname)s|%(process)d|%(thread)d|%(funcName)s()|%(message)s"
        )
        formatter.converter = time.gmtime

        logger.setLevel(level)

        handler = logging.FileHandler(self.get_log_file_path())
        handler.setLevel(level)
        handler.setFormatter(formatter)
        logger.addHandler(handler)

        return logger

    def get_log_file_path(self):
        """ Get the log file path for users.
            Each process has its own log file, so there is not race issue among multiple processes.
        """
        ip_address = socket.gethostbyname(socket.gethostname())
        log_dir = os.path.join(self.get_log_dir(), "user", ip_address)
        os.makedirs(log_dir, exist_ok=True)
        return os.path.join(log_dir, f"{current_process().name}.txt")

    def get_log_dir(self):
        """ Return the folder for logs.
            Files and folders in it will be uploaded and show up in run detail page in the azure portal.
        """
        log_dir = os.path.join(self.get_working_dir(), "logs")
        os.makedirs(log_dir, exist_ok=True)
        return log_dir

    def get_working_dir(self):
        """ Return the working directory."""
        return os.path.join(os.environ.get("AZ_BATCHAI_INPUT_AZUREML", ""), self.get_run().id)

    def get_temp_dir(self):
        """ Return local temp directory."""
        local_temp_dir = os.path.join(
            os.environ.get("AZ_BATCHAI_JOB_TEMP", ""), "azureml-bi", str(os.getpid())
        )
        os.makedirs(local_temp_dir, exist_ok=True)
        return local_temp_dir

    def get_run(self):
        """ Return the Run from the context."""
        return Run.get_context(allow_offline=False)

```

**使用記錄器的範例專案腳本：**
```python
"""
This is a sample scoring module.

This module provides a sample which passes the input back without any change.
"""
import os
import logging
from entry_script_helper import EntryScriptHelper

LOG_NAME = "score_file_list"


def init():
    """ Init """
    EntryScriptHelper().config(LOG_NAME)
    logger = logging.getLogger(LOG_NAME)
    output_folder = os.path.join(os.environ.get("AZ_BATCHAI_INPUT_AZUREML", ""), "temp/output")
    logger.info(f"{__file__}.output_folder:{output_folder}")
    logger.info("init()")
    os.makedirs(output_folder, exist_ok=True)


def run(mini_batch):
    """ Accept and return the list back."""
    logger = logging.getLogger(LOG_NAME)
    logger.info(f"{__file__}, run({mini_batch})")

    output_folder = os.path.join(os.environ.get("AZ_BATCHAI_INPUT_AZUREML", ""), "temp/output")
    for file_name in mini_batch:
        with open(file_name, "r") as file:
            lines = file.readlines()
        base_name = os.path.basename(file_name)
        name = os.path.join(output_folder, base_name)
        logger.info(f"{__file__}: {name}")
        with open(name, "w") as file:
            file.write(f"ouput file {name} from {__file__}:\n")
            for line in lines:
                file.write(line)

    return mini_batch
```

## <a name="next-steps"></a>後續步驟

* 如需[contrib-管線步驟](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps?view=azure-ml-py)套件和 ParallelRunStep 類別的[檔](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallelrunstep?view=azure-ml-py)，請參閱 SDK 參考。

* 遵循使用管線進行批次評分的[advanced 教學](tutorial-pipeline-batch-scoring-classification.md)課程。
