---
title: Azure IoT Edge SQL 模組 | Microsoft Docs
description: 使用 Microsoft SQL 模組在邊緣儲存資料，並使用 Azure Functions 來設定資料格式。
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: ebertrams
ms.date: 02/21/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 30dc833edabfe3d13769bafc9fa0eb6bdd51bb23
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287202"
---
# <a name="store-data-at-the-edge-with-sql-server-databases"></a>使用 SQL Server 資料庫在邊緣儲存資料

使用 Azure IoT Edge 裝置可儲存在邊緣所產生的資料。 具有間歇性網際網路連線的裝置可保有自己的資料庫，只在連線時才向雲端回報變更。 透過程式設計而設定為只會將重要資料傳送到雲端的裝置，可將其餘資料儲存起來以便定期大量上傳。 結構化資料一旦位於雲端，便可共用給其他 Azure 服務，舉例來說，可用來建置機器學習模型。 

本文提供如何將 SQL Server 資料庫部署到 IoT Edge 裝置的指示。 在 IoT Edge 裝置上執行的 Azure Functions 會將傳入的資料結構化，然後傳送到資料庫。 本文中的步驟也可套用到會在 MySQL 或 PostgreSQL 等容器中工作的其他資料庫。 

## <a name="prerequisites"></a>先決條件 

在開始本文中的指示之前，請先完成下列教學課程：
* 在 [Windows](tutorial-simulate-device-windows.md) 或 [Linux](tutorial-simulate-device-linux.md) 中的模擬裝置上部署 Azure IoT Edge
* [將 Azure Function 部署為 IoT Edge 模組](tutorial-deploy-function.md)

下列文章雖非順利完成本教學課程所需，卻可提供實用內容：
* [使用 Docker 來執行 SQL Server 2017 容器映像](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker)
* [使用 Visual Studio Code 開發 Azure Functions 並部署至 Azure IoT Edge](how-to-vscode-develop-azure-function.md)

完成所需的教學課程之後，請在您的機器上備妥所有所需的先決條件： 
* 至少具有一部 IoT Edge 裝置的作用中 Azure IoT 中樞。
* 具有至少 2 GB RAM 和 2 GB 磁碟機的 IoT Edge 裝置。
* [Visual Studio Code](https://code.visualstudio.com/)。 
* [Visual Studio Code 的 Azure IoT Edge 擴充功能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)。 
* [C# for Visual Studio Code (採用 OmniSharp 技術) 擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)。 
* [Docker](https://docs.docker.com/engine/installation/)
* [.NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd)。 
* [Python 2.7](https://www.python.org/downloads/)
* [IoT Edge 控制指令碼](https://pypi.python.org/pypi/azure-iot-edge-runtime-ctl) \(英文\)
* AzureIoTEdgeFunction 範本 (`dotnet new -i Microsoft.Azure.IoT.Edge.Function`)

採用 x64 處理器架構的 Windows 和 Linux 容器皆適用於本教學課程。 SQL Server 不支援 ARM 處理器。

## <a name="deploy-a-sql-server-container"></a>部署 SQL Server 容器

在本節中，您會將 MS-SQL 資料庫新增到模擬的 IoT Edge 裝置。 使用 SQL Server 2017 Docker 容器映像，其有以 [Windows](https://hub.docker.com/r/microsoft/mssql-server-windows-developer/) \(英文\) 容器及 [Linux](https://hub.docker.com/r/microsoft/mssql-server-linux/) \(英文\) 容器的形式提供。 

### <a name="deploy-sql-server-2017"></a>部署 SQL Server 2017

根據預設，本節中的程式碼會使用免費的 SQL Server 2017 Developer Edition 來建立容器。 如果您想要改為執行生產版本，請參閱[執行生產容器映像](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-docker#production)以了解詳細資訊。 

在步驟 3 中，您會對 SQL Server 容器新增用於建立環境變數和持續儲存體的重要建立選項。 所設定的[環境變數](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-environment-variables)會接受使用者授權合約並定義密碼。 [持續儲存體](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-docker#persist)的設定則要使用[掛接](https://docs.docker.com/storage/volumes/)。 掛接會建立連結了 sqlvolume 磁碟區容器的 SQL Server 2017 容器，讓您的資料在即使容器已刪除的情況下仍可保留下來。 

1. 在 Visual Studio Code 中開啟 `deployment.json` 檔案。 
2. 以下列程式碼取代此檔案的 **modules** 區段： 

   ```json
   "modules": {
          "filterFunction": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "<docker registry address>/filterfunction:latest",
              "createOptions": "{}"
            }
          },
          "tempSensor": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview",
              "createOptions": "{}"
            }
          },
          "sql": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "",
              "createOptions": ""
             }
          }
        }
   ```

3. 使用在已完成的＜[將 Azure Function 部署為 IoT Edge 模組](tutorial-deploy-function.md)＞教學課程中填入的位址取代 `<docker registry address>`。

   >[!NOTE]
   >容器登錄位址與您從登錄複製的登入伺服器相同。 其格式應該是 `<your container registry name>.azurecr.io`

4. 根據您所執行的作業系統，使用下列程式碼更新 SQL 模組的設定： 

   * Windows:

      ```json
      "image": "microsoft/mssql-server-windows-developer",
      "createOptions": "{\"Env\": [\"ACCEPT_EULA=Y\",\"MSSQL_SA_PASSWORD=Strong!Passw0rd\"],\"HostConfig\": {\"Mounts\": [{\"Target\": \"C:\\\\mssql\",\"Source\": \"sqlVolume\",\"Type\": \"volume\"}],\"PortBindings\": {\"1433/tcp\": [{\"HostPort\": \"1401\"}]}}}"
      ```

   * Linux：

      ```json
      "image": "microsoft/mssql-server-linux:2017-latest",
      "createOptions": "{\"Env\": [\"ACCEPT_EULA=Y\",\"MSSQL_SA_PASSWORD=Strong!Passw0rd\"],\"HostConfig\": {\"Mounts\": [{\"Target\": \"/var/opt/mssql\",\"Source\": \"sqlVolume\",\"Type\": \"volume\"}],\"PortBindings\": {\"1433/tcp\": [{\"HostPort\": \"1401\"}]}}}"
      ```

5. 儲存檔案。 
6. 在 VS Code 命令選擇區中，選取 [Edge: 建立 Edge 裝置的部署]。 
7. 選取 IoT Edge 裝置識別碼。
8. 選取您所更新的 `deployment.json` 檔案。 在輸出視窗中，您可以看到部署的對應輸出。 
9. 若要啟動 Edge 執行階段，請在命令選擇區中選取 [Edge: 啟動 Edge]。

>[!TIP]
>每當您在生產環境中建立了 SQL Server 容器時，就應該[變更預設的系統管理員密碼](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker#change-the-sa-password)。

## <a name="create-the-sql-database"></a>建立 SQL 資料庫

本節會引導您設定 SQL 資料庫，以便儲存與 IoT Edge 裝置連線之感應器所傳來的溫度資料。 如果您使用模擬裝置，則此資料會來自 tempSensor 容器。 

在命令列工具中連線到您的資料庫： 

* Windows 容器
   ```cmd
   docker exec -it sql cmd
   ```

* Linux 容器
   ```bash
   docker exec -it sql bash
   ```

開啟 SQL 命令工具： 

* Windows 容器
   ```cmd
   sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
   ```

* Linux 容器
   ```bash
   /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
   ```

建立您的資料庫： 

* Windows 容器
   ```sql
   CREATE DATABASE MeasurementsDB
   ON
   (NAME = MeasurementsDB, FILENAME = 'C:\mssql\measurementsdb.mdf')
   GO
   ```

* Linux 容器
   ```sql
   CREATE DATABASE MeasurementsDB
   ON
   (NAME = MeasurementsDB, FILENAME = '/var/opt/mssql/measurementsdb.mdf')
   GO
   ```

定義您的資料表： 

   ```sql
   CREATE TABLE MeasurementsDB.dbo.TemperatureMeasurements (measurementTime DATETIME2, location NVARCHAR(50), temperature FLOAT)
   GO
   ```

您可以自訂 SQL Server Docker 檔案，以便自動設定您要部署在多個 IoT Edge 裝置上的 SQL Server。 如需詳細資訊，請參閱 [Microsoft SQL Server 容器示範專案](https://github.com/twright-msft/mssql-node-docker-demo-app)。

## <a name="understand-the-sql-connection"></a>了解 SQL 連線

在其他教學課程中，我們使用了路由讓容器能夠在彼此隔離的情況下保持通訊。 不過，在使用 SQL Server 資料庫時就必須有更緊密的關聯。 

IoT Edge 會在啟動時自動建置橋接器 (Linux) 或 NAT (Windows) 網路。 此網路稱為 **azure-iot-edge**。 如果您需要偵錯此連線，則可在命令列中查閱其屬性：

* Windows

   ```cmd
   docker network inspect azure-iot-edge
   ```

* Linux

   ```bash
   sudo docker network inspect azure-iot-edge
   ```

IoT Edge 也可以透過 Docker 解析容器名稱的 DNS，因此您不需要依據 IP 位址來參照 SQL Server 資料庫。 

舉例來說，以下是我們在下一節所使用的連接字串： 

   ```csharp
   Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;
   ```

您可以看到，連接字串會依據容器的名稱 **sql** 來參照容器。 如果您將模組變更為其他名稱，也請更新此連接字串。 否則，請繼續進行下一節。 

## <a name="update-your-azure-function"></a>更新 Azure 函式
若要將資料傳送到資料庫，請更新您在上一個教學課程中所建立的 FilterFunction Azure 函式。 變更這個檔案，使其將感應器所接收的資料結構化，然後儲存在 SQL 資料表中。 

1. 在 Visual Studio Code 中，開啟 FilterFunction 資料夾。 
2. 使用下列程式碼 (內含上一節所提到的 SQL 連接字串) 取代 run.csx 檔案： 

   ```csharp
   #r "Microsoft.Azure.Devices.Client"
   #r "Newtonsoft.Json"
   #r "System.Data.SqlClient"

   using System.IO;
   using Microsoft.Azure.Devices.Client;
   using Newtonsoft.Json;
   using Sql = System.Data.SqlClient;
   using System.Threading.Tasks;

   // Filter messages based on the temperature value in the body of the message and the temperature threshold value.
   public static async Task Run(Message messageReceived, IAsyncCollector<Message> output, TraceWriter log)
   {
       const int temperatureThreshold = 25;
       byte[] messageBytes = messageReceived.GetBytes();
       var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

       if (!string.IsNullOrEmpty(messageString))
       {
           // Get the body of the message and deserialize it
           var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

           //Store the data in SQL db
           const string str = "Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;";
           using (Sql.SqlConnection conn = new Sql.SqlConnection(str))
           {
           conn.Open();
           var insertMachineTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'machine', " + messageBody.machine.temperature + ");";
           var insertAmbientTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'ambient', " + messageBody.ambient.temperature + ");"; 
               using (Sql.SqlCommand cmd = new Sql.SqlCommand(insertMachineTemperature + "\n" + insertAmbientTemperature, conn))
               {
               //Execute the command and log the # rows affected.
               var rows = await cmd.ExecuteNonQueryAsync();
               log.Info($"{rows} rows were updated");
               }
           }

           if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
           {
               // Send the message to the output as the temperature value is greater than the threshold
               var filteredMessage = new Message(messageBytes);
               // Copy the properties of the original message into the new Message object
               foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
               {
                   filteredMessage.Properties.Add(prop.Key, prop.Value);
               }
               // Add a new property to the message to indicate it is an alert
               filteredMessage.Properties.Add("MessageType", "Alert");
               // Send the message        
               await output.AddAsync(filteredMessage);
               log.Info("Received and transferred a message with temperature above the threshold");
           }
       }
   }

   //Define the expected schema for the body of incoming messages
   class MessageBody
   {
       public Machine machine {get;set;}
       public Ambient ambient {get; set;}
       public string timeCreated {get; set;}
   }
   class Machine
   {
      public double temperature {get; set;}
      public double pressure {get; set;}         
   }
   class Ambient
   {
      public double temperature {get; set;}
      public int humidity {get; set;}         
   }
   ```

## <a name="update-your-container-image"></a>更新容器映像

為了套用您所做的變更，請更新容器映像、加以發佈，然後重新啟動 IoT Edge。

1. 在 Visual Studio Code 中，展開 [Docker] 資料夾。 
2. 根據您所使用的平台，展開 [windows-nano] 或 [linux-x64] 資料夾。 
3. 在 [Dockerfile] 檔案上按一下滑鼠右鍵，然後選取 [建置 IoT Edge 模組 Docker 映像]。
4. 瀏覽至 [FilterFunction] 專案資料夾，然後按一下 [選取資料夾作為 EXE_DIR]。
5. 在 VS Code 視窗頂端的快顯文字方塊中，輸入映像名稱。 例如： `<your container registry address>/filterfunction:latest`。 如果您要部署到本機登錄，則名稱應該是 `<localhost:5000/filterfunction:latest>`。
6. 在 VS Code 命令選擇區中，選取 [Edge: 推送 IoT Edge 模組 Docker 映像]。 
7. 在快顯文字方塊中，輸入相同的映像名稱。 
8. 在 VS Code 命令選擇區中，選取 [Edge: 重新啟動 Edge]。

## <a name="view-the-local-data"></a>檢視本機資料

容器重新啟動之後，從溫度感應器收到的資料就會儲存在 IoT Edge 裝置上的本機 SQL Server 2017 資料庫中。 

在命令列工具中連線到您的資料庫： 

* Windows 容器
   ```cmd
   docker exec -it sql cmd
   ```

* Linux 容器
   ```bash
   docker exec -it sql bash
   ```

開啟 SQL 命令工具： 

* Windows 容器
   ```cmd
   sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
   ```

* Linux 容器
   ```bash
   /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
   ```

檢視您的資料： 

   ```sql
   SELECT * FROM MeasurementsDB.dbo.TemperatureMeasurements
   GO
   ```

## <a name="next-steps"></a>後續步驟

* 了解如何[在 Docker 上設定 SQL Server 2017 容器映像](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-docker)。

* 瀏覽 [mssql-docker GitHub 存放庫](https://github.com/Microsoft/mssql-docker)中的資源、意見反應和已知問題。
