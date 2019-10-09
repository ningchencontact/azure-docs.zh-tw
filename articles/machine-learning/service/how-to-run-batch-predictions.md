---
title: 使用管線對大型資料執行批次預測
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning，以非同步方式對大量資料進行批次預測。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens, garye
ms.author: jordane
author: jpe316
ms.date: 07/12/2019
ms.openlocfilehash: 910974eac6a67c9c9fe68c502f2876ef68bb94eb
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2019
ms.locfileid: "72028529"
---
# <a name="run-batch-predictions-on-large-data-sets-with-azure-machine-learning-pipelines"></a>在具有 Azure Machine Learning 管線的大型資料集上執行批次預測

在本文中，您將瞭解如何使用 Azure Machine Learning 的 ML 管線，以非同步方式對大量資料進行預測。

批次預測 (或批次評分) 會針對非同步的應用程式，利用獨特的輸送量來提供符合成本效益的推斷。 批次預測管線可以調整規模，以針對數個 TB 的生產資料執行推斷。 批次預測會對適用於大型資料集合的高輸送量且射後不理的預測進行最佳化。

>[!TIP]
> 如果您的系統需要低延遲處理 (為了快速處理單一文件或小型文件集)，請使用[即時評分](how-to-consume-web-service.md)而不是批次預測。

在下列步驟中，您會建立[機器學習管線](concept-ml-pipelines.md)來註冊預先定型的電腦視覺模型（[開始-V3](https://arxiv.org/abs/1512.00567)）。 然後，您可以使用預先定型的模型，對 Azure Blob 儲存體帳戶中可用的映射執行批次評分。 這些用於計分的影像均為來自 [ImageNet](http://image-net.org/) \(英文\) 資料集的未標記影像。

## <a name="prerequisites"></a>必要條件

- 如果您沒有 Azure 訂用帳戶，請在開始前先建立一個免費帳戶。 試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。

- 設定開發環境以安裝 Azure Machine Learning SDK。 如需詳細資訊，請參閱[設定 Azure Machine Learning 的開發環境](how-to-configure-environment.md)。

- 建立 Azure Machine Learning 工作區，此工作區將會保存您的所有管線資源。 您可以使用下列程式碼，或者如需其他選項，請參閱[建立工作區設定檔](how-to-configure-environment.md#workspace)。

  ```python
  from azureml.core import Workspace
  ws = Workspace.create(name = '<workspace-name>',
                        subscription_id = '<subscription-id>',
                        resource_group = '<resource-group>',
                        location = '<workspace_region>',
                        exist_ok = True
                        )
  ```

## <a name="set-up-machine-learning-resources"></a>設定機器學習資源

下列步驟會設定執行管線所需的資源：

- 存取已經具備預先定型之模型、輸入標籤及要評分之映像 (已經為您設定此項) 的資料存放區。
- 設定資料存放區來儲存您的輸出。
- 設定  `DataReference` 物件，以指向上述資料存放區中的資料。
- 設定管線步驟執行所在的計算機器或叢集。

### <a name="access-the-datastores"></a>存取資料存放區

首先，存取具備模型、標籤及影像的資料存放區。

使用名為*sampledata*的公用 blob 容器，其位於保存 ImageNet 評估集之映射的*pipelinedata*帳戶中。 此公用容器的資料存放區名稱為 *images_datastore*。 向您的工作區註冊此資料存放區：

```python
from azureml.core import Datastore

account_name = "pipelinedata"
datastore_name = "images_datastore"
container_name = "sampledata"

batchscore_blob = Datastore.register_azure_blob_container(ws,
                                                          datastore_name=datastore_name,
                                                          container_name=container_name,
                                                          account_name=account_name,
                                                          overwrite=True)
```

接下來，設定以針對輸出使用預設的資料存放區。

當您建立工作區時，預設會將[檔案儲存體](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) 和 [Blob 儲存體](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) 連結至工作區。 檔案儲存體是工作區的預設資料存放區，但您也可以使用 Blob 儲存體作為資料存放區。 如需詳細資訊，請參閱 [Azure 儲存體選項](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)。

```python
def_data_store = ws.get_default_datastore()
```

### <a name="configure-data-references"></a>設定資料參考

現在，請參考管線中的資料作為管線步驟的輸入。

管線中的資料來源會由 [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference) 物件來表示。  `DataReference` 物件會指向位於資料存放區中或可從資料存放區存取的資料。 您需要針對輸入影像使用的目錄、儲存預先定型之模型的目錄、適用於標籤的目錄及輸出目錄，使用 `DataReference` 物件。

```python
from azureml.data.data_reference import DataReference

input_images = DataReference(datastore=batchscore_blob,
                             data_reference_name="input_images",
                             path_on_datastore="batchscoring/images",
                             mode="download")

model_dir = DataReference(datastore=batchscore_blob,
                          data_reference_name="input_model",
                          path_on_datastore="batchscoring/models",
                          mode="download")

label_dir = DataReference(datastore=batchscore_blob,
                          data_reference_name="input_labels",
                          path_on_datastore="batchscoring/labels",
                          mode="download")

output_dir = PipelineData(name="scores",
                          datastore=def_data_store,
                          output_path_on_compute="batchscoring/results")
```

### <a name="set-up-compute-target"></a>設定計算目標

在 Azure Machine Learning 中，*計算* (或*計算目標*) 係指會在您機器學習管線中執行計算步驟的機器或叢集。 例如，您可以使用[AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute%28class%29?view=azure-ml-py)類別來建立 Azure Machine Learning 計算。

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget

compute_name = "gpucluster"
compute_min_nodes = 0
compute_max_nodes = 4
vm_size = "STANDARD_NC6"

if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('Found compute target. just use it. ' + compute_name)
else:
    print('Creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(
        vm_size=vm_size,  # NC6 is GPU-enabled
        vm_priority='lowpriority',  # optional
        min_nodes=compute_min_nodes,
        max_nodes=compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(ws,
                                          compute_name,
                                          provisioning_config)

    compute_target.wait_for_completion(
        show_output=True,
        min_node_count=None,
        timeout_in_minutes=20)
```

## <a name="prepare-the-model"></a>準備模型

在您可以使用預先定型的模型之前，必須先下載模型，並向您的工作區註冊該模型。

### <a name="download-the-pretrained-model"></a>下載預先定型的模型

從 <http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz> 下載預先定型的電腦視覺模型 (InceptionV3)。 然後，將其解壓縮到 `models` 子資料夾。

```python
import os
import tarfile
import urllib.request

model_dir = 'models'
if not os.path.isdir(model_dir):
    os.mkdir(model_dir)

url = "http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz"
response = urllib.request.urlretrieve(url, "model.tar.gz")
tar = tarfile.open("model.tar.gz", "r:gz")
tar.extractall(model_dir)
```

### <a name="register-the-model"></a>註冊模型

註冊模型的方法如下：

```python
import shutil
from azureml.core.model import Model

# register downloaded model
model = Model.register(
    model_path="models/inception_v3.ckpt",
    model_name="inception",  # This is the name of the registered model
    tags={'pretrained': "inception"},
    description="Imagenet trained tensorflow inception",
    workspace=ws)
```

## <a name="write-your-scoring-script"></a>撰寫您的計分指令碼

>[!Warning]
>下列程式碼只是[範例 Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/pipeline-batch-scoring/pipeline-batch-scoring.ipynb) \(英文\) 所使用之 [batch_score.p](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/pipeline-batch-scoring/batch_scoring.py) 中內含的範例。 您將需針對您的案例建立自己的計分指令碼。

@No__t 0 腳本會接受*dataset_path*中的輸入影像、model_dir 中的預先定型模型 *，* 並將*results-label*輸出到*output_dir*。

```python
# Snippets from a sample scoring script
# Refer to the accompanying batch-scoring Notebook
# https://github.com/Azure/MachineLearningNotebooks/blob/master/pipeline/pipeline-batch-scoring.ipynb
# for the implementation script

# Get labels
def get_class_label_dict(label_file):
  label = []
  proto_as_ascii_lines = tf.gfile.GFile(label_file).readlines()
  for l in proto_as_ascii_lines:
    label.append(l.rstrip())
  return label

class DataIterator:
  # Definition of the DataIterator here

def main(_):
    # Refer to batch-scoring Notebook for implementation.
    label_file_name = os.path.join(args.label_dir, "labels.txt")
    label_dict = get_class_label_dict(label_file_name)
    classes_num = len(label_dict)
    test_feeder = DataIterator(data_dir=args.dataset_path)
    total_size = len(test_feeder.labels)

    # get model from model registry
    model_path = Model.get_model_path(args.model_name)
    with tf.Session() as sess:
        test_images = test_feeder.input_pipeline(batch_size=args.batch_size)
        with slim.arg_scope(inception_v3.inception_v3_arg_scope()):
            input_images = tf.placeholder(tf.float32, [args.batch_size, image_size, image_size, num_channel])
            logits, _ = inception_v3.inception_v3(input_images,
                                                        num_classes=classes_num,
                                                        is_training=False)
            probabilities = tf.argmax(logits, 1)

        sess.run(tf.global_variables_initializer())
        sess.run(tf.local_variables_initializer())
        coord = tf.train.Coordinator()
        threads = tf.train.start_queue_runners(sess=sess, coord=coord)
        saver = tf.train.Saver()
        saver.restore(sess, model_path)
        out_filename = os.path.join(args.output_dir, "result-labels.txt")

        # copy the file to artifacts
        shutil.copy(out_filename, "./outputs/")
```

## <a name="build-and-run-the-batch-scoring-pipeline"></a>建置並執行批次評分管線

### <a name="prepare-the-run-environment"></a>準備執行環境

針對指令碼指定 conda 相依性。 當您稍後建立管線步驟時，將需要這個物件。

```python
from azureml.core.runconfig import DEFAULT_GPU_IMAGE
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

cd = CondaDependencies.create(
    pip_packages=["tensorflow-gpu==1.10.0", "azureml-defaults"])

# Runconfig
amlcompute_run_config = RunConfiguration(conda_dependencies=cd)
amlcompute_run_config.environment.docker.enabled = True
amlcompute_run_config.environment.docker.gpu_support = True
amlcompute_run_config.environment.docker.base_image = DEFAULT_GPU_IMAGE
amlcompute_run_config.environment.spark.precache_packages = False
```

### <a name="specify-the-parameter-for-your-pipeline"></a>指定管線的參數

使用  [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py) \(英文\) 物件搭配預設值來建立管線參數。

```python
from azureml.pipeline.core.graph import PipelineParameter
batch_size_param = PipelineParameter(
    name="param_batch_size",
    default_value=20)
```

### <a name="create-the-pipeline-step"></a>建立管線步驟

使用指令碼、環境設定和參數來建立管線步驟。 指定您已經附加至工作區的計算目標以作為指令碼執行目標。 使用 [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py) \(英文\) 來建立管線步驟。

```python
from azureml.pipeline.steps import PythonScriptStep
inception_model_name = "inception_v3.ckpt"

batch_score_step = PythonScriptStep(
    name="batch_scoring",
    script_name="batch_score.py",
    arguments=["--dataset_path", input_images,
               "--model_name", "inception",
               "--label_dir", label_dir,
               "--output_dir", output_dir,
               "--batch_size", batch_size_param],
    compute_target=compute_target,
    inputs=[input_images, label_dir],
    outputs=[output_dir],
    runconfig=amlcompute_run_config,
    source_directory=scripts_folder
```

### <a name="run-the-pipeline"></a>執行管道

現在執行管線，並檢查它所產生的輸出。 輸出具有對應至每個輸入影像的分數。

```python
import pandas as pd
from azureml.pipeline.core import Pipeline

# Run the pipeline
pipeline = Pipeline(workspace=ws, steps=[batch_score_step])
pipeline_run = Experiment(ws, 'batch_scoring').submit(
    pipeline, pipeline_params={"param_batch_size": 20})

# Wait for the run to finish (this might take several minutes)
pipeline_run.wait_for_completion(show_output=True)

# Download and review the output
step_run = list(pipeline_run.get_children())[0]
step_run.download_file("./outputs/result-labels.txt")

df = pd.read_csv("result-labels.txt", delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
df.head()
```

## <a name="publish-the-pipeline"></a>發佈管線

在滿意執行結果之後，請發佈管線，以便稍後可以使用不同的輸入值來執行它。 當您發佈管線時，您會取得一個 REST 端點。 此端點會接受利用一組您已經使用 [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py) \(英文\) 合併的參數來叫用管線。

```python
published_pipeline = pipeline_run.publish_pipeline(
    name="Inception_v3_scoring",
    description="Batch scoring using Inception v3 model",
    version="1.0")
```

## <a name="rerun-the-pipeline-by-using-the-rest-endpoint"></a>使用 REST 端點重新執行管線

若要重新執行管線，您將需要 Azure Active Directory 驗證標頭權杖，如 [AzureCliAuthentication 類別](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py) \(英文\) 中所述。

```python
from azureml.pipeline.core.run import PipelineRun
from azureml.pipeline.core import PublishedPipeline

rest_endpoint = published_pipeline.endpoint
# specify batch size when running the pipeline
response = requests.post(rest_endpoint,
                         headers=aad_token,
                         json={"ExperimentName": "batch_scoring",
                               "ParameterAssignments": {"param_batch_size": 50}})

# Monitor the run
published_pipeline_run = PipelineRun(ws.experiments["batch_scoring"], run_id)

RunDetails(published_pipeline_run).show()
```

## <a name="next-steps"></a>後續步驟

若要查看此端對端工作，請嘗試[GitHub](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines)中的批次評分筆記本，或移至[Azure 架構中心](/azure/architecture/reference-architectures/ai/batch-scoring-python)以查看範例解決方案架構。
