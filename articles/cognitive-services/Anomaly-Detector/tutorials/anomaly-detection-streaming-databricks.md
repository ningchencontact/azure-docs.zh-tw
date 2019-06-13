---
title: 教學課程：使用 Azure Databricks 對串流資料進行異常情況偵測
description: 使用 Azure Databricks 與異常偵測器 API 來監視資料中的異常狀況。
titlesuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 05/08/2019
ms.author: aahi
ms.openlocfilehash: a5790b5412023f06d9f9fd1d2ff61c11db4c53f3
ms.sourcegitcommit: f9448a4d87226362a02b14d88290ad6b1aea9d82
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/07/2019
ms.locfileid: "66807473"
---
# <a name="tutorial-anomaly-detection-on-streaming-data-using-azure-databricks"></a>教學課程：使用 Azure Databricks 對串流資料進行異常情況偵測

[Azure Databricks](https://azure.microsoft.com/services/databricks/)是快速、 輕鬆且共同作業的 Apache Spark 架構分析服務。 異常偵測器 API 的 Azure 認知服務的組件提供監視您的時間序列資料的方式。 使用本教學課程中的資料幾近即時地串流執行異常偵測使用 Azure Databricks。 您將內嵌 twitter 資料，使用 Azure 事件中樞，並匯入到 Azure Databricks 使用 Spark 事件中樞連接器。 之後，您將使用的 API 來偵測異常行為對串流資料。 

下圖顯示此應用程式流程：

![Azure Databricks 與事件中樞和認知服務](../media/tutorials/databricks-cognitive-services-tutorial.png "Azure Databricks 與事件中樞和認知服務")

本教學課程涵蓋下列工作：

> [!div class="checklist"]
> * 建立 Azure Databricks 工作區
> * 在 Azure Databricks 中建立 Spark 叢集
> * 建立 Twitter 應用程式來存取串流資料
> * 在 Azure Databricks 中建立 Notebook
> * 連結事件中樞與 Twitter API 的程式庫
> * 建立異常偵測器資源，並擷取存取金鑰
> * 將推文傳送至事件中樞
> * 從事件中樞讀取推文
> * 對推文執行異常偵測

> [!Note]
> 本教學課程介紹一種方法實作的建議[解決方案架構](https://azure.microsoft.com/solutions/architecture/anomaly-detector-process/)異常偵測器 api。

如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

> [!Note]
> 無法完成本教學課程使用免費的試用版金鑰，異常偵測器 api。 若要使用免費帳戶建立 Azure Databricks 叢集，在建立叢集之前，請先移至您的設定檔，並將訂用帳戶變更為**隨用隨付**。 如需詳細資訊，請參閱 [Azure 免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

- [Azure 事件中樞命名空間](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)和事件中樞。

- [連接字串](../../../event-hubs/event-hubs-get-connection-string.md)存取事件中樞命名空間。 連接字串應該以類似的格式：

    `Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<key name>;SharedAccessKey=<key value>` 。 

- 事件中樞的共用的存取原則名稱和原則金鑰。

請參閱 Azure 事件中樞[快速入門](../../../event-hubs/event-hubs-create.md)如需建立命名空間和事件中樞資訊。

## <a name="create-an-azure-databricks-workspace"></a>建立 Azure Databricks 工作區

在本節中，您會建立 Azure Databricks 工作區中使用[Azure 入口網站](https://portal.azure.com/)。

1. 在 Azure 入口網站中，選取 [建立資源]   > [分析]   > [Azure Databricks]  。

    ![Azure 入口網站上的 Databricks](../media/tutorials/azure-databricks-on-portal.png "Azure 入口網站上的 Databricks")

3. 在 [Azure Databricks 服務]  下方提供下列值，以建立 Databricks 工作區：


    |屬性  |描述  |
    |---------|---------|
    |**工作區名稱**     | 提供您 Databricks 工作區的名稱        |
    |**訂用帳戶**     | 從下拉式清單中選取您的 Azure 訂用帳戶。        |
    |**資源群組**     | 指定您是要建立新的資源群組，還是使用現有資源群組。 資源群組是存放 Azure 方案相關資源的容器。 如需詳細資訊，請參閱 [Azure 資源群組概觀](../../../azure-resource-manager/resource-group-overview.md)。 |
    |**位置**     | 選取 **美國東部 2**或其中一個其他可用的區域。 請參閱[提供的 Azure 服務的區域](https://azure.microsoft.com/regions/services/)區域可用性。        |
    |定價層      |  選擇 [標準]  或 [進階]  。 請勿選擇**試用版**。 如需這些定價層的詳細資訊，請參閱 [Databricks 定價頁面](https://azure.microsoft.com/pricing/details/databricks/)。       |

    選取 [建立]  。

4. 工作區建立需要幾分鐘的時間。 

## <a name="create-a-spark-cluster-in-databricks"></a>在 Databricks 中建立 Spark 叢集

1. 在 Azure 入口網站中，移至您所建立的 Databricks 工作區，然後選取 [啟動工作區]  。

2. 系統會將您重新導向至 Azure Databricks 入口網站。 從入口網站中，選取**新的叢集**。

    ![Azure 上的 Databricks](../media/tutorials/databricks-on-azure.png "Azure 上的 Databricks")

3. 在 **新的叢集**頁面上，提供值以建立叢集。

    ![在 Azure 上建立 Databricks Spark 叢集](../media/tutorials/create-databricks-spark-cluster.png "在 Azure 上建立 Databricks Spark 叢集")

    接受下列值以外的所有其他預設值：

   * 輸入叢集的名稱。
   * 針對本文，使用 **5.2** 執行階段建立叢集。 請勿選取**5.3**執行階段。
   * 請確定**後終止\_\_停止活動幾分鐘**核取方塊已選取。 如果不使用叢集，請終止叢集，提供持續時間 （以分鐘為單位）。

     選取 [建立叢集]  。 
4. 叢集建立作業需要幾分鐘的時間。 叢集在執行後，您就可以將 Notebook 連結至叢集，並執行 Spark 作業。

## <a name="create-a-twitter-application"></a>建立 Twitter 應用程式

若要收到推文的串流，您必須在 Twitter 中建立應用程式。 請依照下列步驟建立 Twitter 應用程式，並記錄要完成本教學課程所需的值。

1. 從網頁瀏覽器移至 [Twitter 應用程式管理](https://apps.twitter.com/) ，然後選取 [建立新的應用程式]  。

    ![建立 Twitter 應用程式](../media/tutorials/databricks-create-twitter-app.png "建立 Twitter 應用程式")

2. 在 [建立應用程式]  頁面上，提供新應用程式的詳細資料，然後選取 [建立 Twitter 應用程式]  。

    ![Twitter 應用程式詳細資料](../media/tutorials/databricks-provide-twitter-app-details.png "Twitter 應用程式詳細資料")

3. 在應用程式頁面上，選取 [金鑰和存取權杖]  索引標籤，並複製 [取用者金鑰]  和 [取用者秘密]  的值。 此外，請選取 [建立我的存取權杖]  來產生存取權杖。 複製 [存取權杖]  和 [存取權杖祕密]  的值。

    ![Twitter 應用程式詳細資料](../media/tutorials/twitter-app-key-secret.png "Twitter 應用程式詳細資料")

儲存您所擷取的 Twitter 應用程式值。 在本教學課程後續的內容中，您會需要這些值。

## <a name="attach-libraries-to-spark-cluster"></a>將程式庫連結至 Spark 叢集

在本教學課程中，您會使用 Twitter API 將推文傳送至事件中樞。 您也會使用 [Apache Spark 事件中樞連接器](https://github.com/Azure/azure-event-hubs-spark)來讀取資料並將資料寫入至 Azure 事件中樞。 若要在叢集中使用這些 API，請將其作為程式庫新增至 Azure Databricks，然後讓這些 API 與您的 Spark 叢集產生關聯。 下列指示說明如何新增程式庫，以**共用**工作區中的資料夾。

1. 在 Azure Databricks 工作區中，選取 [工作區]  ，然後以滑鼠右鍵按一下 [共用]  。 從快顯功能表中，選取 [建立]   > [程式庫]  。

   ![新增程式庫對話方塊](../media/tutorials/databricks-add-library-option.png "新增程式庫對話方塊")

2. 在 [新文件庫] 頁面中，如**來源**選取**Maven**。 針對**座標**，輸入您想要新增套件的座標。 以下是本教學課程所使用之程式庫的 Maven 座標：

   * Spark 事件中樞連接器 - `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`
   * Twitter API - `org.twitter4j:twitter4j-core:4.0.7`

     ![提供 Maven 座標](../media/tutorials/databricks-eventhub-specify-maven-coordinate.png "提供 Maven 座標")

3. 選取 [建立]  。

4. 選取程式庫新增所在的資料夾，然後選取程式庫名稱。

    ![選取要新增的程式庫](../media/tutorials/select-library.png "選取要新增的程式庫")

5. 如果在文件庫頁面中沒有叢集，請選取**叢集**並執行您所建立的叢集。 等到狀態顯示 'Running'，然後移至 程式庫 頁面的 上一步。
在程式庫 頁面上，選取您要使用該媒體櫃，然後選取的叢集**安裝**。 若要順利與叢集相關聯的程式庫之後，立即變更狀態**已安裝**。

    ![安裝至叢集程式庫](../media/tutorials/databricks-library-attached.png "到叢集的安裝程式庫")

6. 對 Twitter 套件 `twitter4j-core:4.0.7` 重複執行這些步驟。

## <a name="get-a-cognitive-services-access-key"></a>取得認知服務存取金鑰

在本教學課程中，您可以使用[Azure 認知服務異常偵測器 Api](../overview.md)上以近乎即時的推文的串流執行異常偵測。 您使用 Api 之前，您必須在 Azure 上建立異常偵測器資源，並擷取存取金鑰，才能使用異常偵測器 Api。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

2. 選取 [+ 建立資源]  。

3. 在 Azure Marketplace 中，選取**AI + 機器學習服務** > **查看所有** > **認知服務-更** >  **異常偵測器**。 或者，您可以使用[此連結](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector)以前往**建立**直接對話方塊。

    ![建立異常偵測器資源](../media/tutorials/databricks-cognitive-services-anomaly-detector.png "建立異常偵測器資源")

4. 在 [建立]  對話方塊中提供下列值：

    |值 |描述  |
    |---------|---------|
    |Name     | 異常偵測器資源的名稱。        |
    |訂用帳戶     | Azure 訂用帳戶資源相關聯。        |
    |位置     | Azure 位置。        |
    |定價層     | 服務的定價層。 如需有關異常偵測器定價的詳細資訊，請參閱 <<c0> [ 定價頁面](https://azure.microsoft.com/pricing/details/cognitive-services/anomaly-detector/)。        |
    |資源群組     | 指定您是要建立新的資源群組，還是選取現有資源群組。        |


     選取 [建立]  。

5. 建立資源之後，從**概觀**索引標籤上，複製並儲存**端點**螢幕擷取畫面所示的 URL。 然後選取**顯示存取金鑰**。

    ![顯示存取金鑰](../media/tutorials/cognitive-services-get-access-keys.png "顯示存取金鑰")

6. 底下**金鑰**，選取複製圖示，針對您想要使用的索引鍵。 將儲存的存取金鑰。

    ![複製存取金鑰](../media/tutorials/cognitive-services-copy-access-keys.png "複製存取金鑰")

## <a name="create-notebooks-in-databricks"></a>在 Databricks 中建立 Notebook

在本節中，您會在 Databricks 工作區中建立兩個具有下列名稱的 Notebook

- **SendTweetsToEventHub** - 生產者 Notebook，可供用來從 Twitter 取得推文，再將推文串流至事件中樞。
- **AnalyzeTweetsFromEventHub** -您用來從事件中樞讀取推文和執行異常偵測的取用者 notebook。

1. 在 Azure Databricks 工作區中，選取**工作區**從左窗格中。 從 [工作區]  下拉式清單選取 [建立]  ，然後選取 [Notebook]  。

    ![在 Databricks 中建立 Notebook](../media/tutorials/databricks-create-notebook.png "在 Databricks 中建立 Notebook")

2. 在 **建立的 Notebook**對話方塊方塊中，輸入**SendTweetsToEventHub**做為名稱，選取**Scala**作為語言，然後選取您稍早建立的 Spark 叢集。

    ![在 Databricks 中建立 Notebook](../media/tutorials/databricks-notebook-details.png "在 Databricks 中建立 Notebook")

    選取 [建立]  。

3. 重複上述步驟以建立 **AnalyzeTweetsFromEventHub** Notebook。

## <a name="send-tweets-to-event-hubs"></a>將推文傳送至事件中樞

在 **SendTweetsToEventHub** Notebook 中貼上下列程式碼，並將預留位置取代為您稍早所建立之事件中樞命名空間和 Twitter 應用程式的值。 此 notebook 建立時間及數目的"Like"s 擷取推文加上關鍵字"Azure"，並即時將事件以串流至事件中樞。

```scala
//
// Send Data to Eventhub
//

import scala.collection.JavaConverters._
import com.microsoft.azure.eventhubs._
import java.util.concurrent._
import com.google.gson.{Gson, GsonBuilder, JsonParser}
import java.util.Date
import scala.util.control._
import twitter4j._
import twitter4j.TwitterFactory
import twitter4j.Twitter
import twitter4j.conf.ConfigurationBuilder

// Event Hub Config
val namespaceName = "[Placeholder: EventHub namespace]"
val eventHubName = "[Placeholder: EventHub name]"
val sasKeyName = "[Placeholder: EventHub access key name]"
val sasKey = "[Placeholder: EventHub access key key]"
val connStr = new ConnectionStringBuilder()
  .setNamespaceName(namespaceName)
  .setEventHubName(eventHubName)
  .setSasKeyName(sasKeyName)
  .setSasKey(sasKey)

// Connect to the Event Hub
val pool = Executors.newScheduledThreadPool(1)
val eventHubClient = EventHubClient.create(connStr.toString(), pool)

def sendEvent(message: String) = {
  val messageData = EventData.create(message.getBytes("UTF-8"))
  eventHubClient.get().send(messageData)
  System.out.println("Sent event: " + message + "\n")
}

case class MessageBody(var timestamp: Date, var favorite: Int)
val gson: Gson = new GsonBuilder().setDateFormat("yyyy-MM-dd'T'HH:mm:ss.SSS'Z'").create()

val twitterConsumerKey = "[Placeholder: Twitter consumer key]"
val twitterConsumerSecret = "[Placeholder: Twitter consumer seceret]"
val twitterOauthAccessToken = "[Placeholder: Twitter oauth access token]"
val twitterOauthTokenSecret = "[Placeholder: Twitter oauth token secret]"

val cb = new ConfigurationBuilder()
cb.setDebugEnabled(true)
  .setOAuthConsumerKey(twitterConsumerKey)
  .setOAuthConsumerSecret(twitterConsumerSecret)
  .setOAuthAccessToken(twitterOauthAccessToken)
  .setOAuthAccessTokenSecret(twitterOauthTokenSecret)

val twitterFactory = new TwitterFactory(cb.build())
val twitter = twitterFactory.getInstance()

// Getting tweets with keyword "Azure" and sending them to the Event Hub in realtime!

val query = new Query(" #Azure ")
query.setCount(100)
query.lang("en")

var finished = false
var maxStatusId = Long.MinValue
var preMaxStatusId = Long.MinValue
val innerLoop = new Breaks
while (!finished) {
  val result = twitter.search(query)
  val statuses = result.getTweets()
  var lowestStatusId = Long.MaxValue
  innerLoop.breakable {
    for (status <- statuses.asScala) {
      if (status.getId() <= preMaxStatusId) {
        preMaxStatusId = maxStatusId
        innerLoop.break
      }
      if(!status.isRetweet()) {
        sendEvent(gson.toJson(new MessageBody(status.getCreatedAt(), status.getFavoriteCount())))
      }
      lowestStatusId = Math.min(status.getId(), lowestStatusId)
      maxStatusId = Math.max(status.getId(), maxStatusId)
    }
  }
  
  if (lowestStatusId == Long.MaxValue) {
    preMaxStatusId = maxStatusId
  }
  Thread.sleep(10000)
  query.setMaxId(lowestStatusId - 1)
}

// Close connection to the Event Hub
eventHubClient.get().close()
pool.shutdown()
```

若要執行 Notebook，請按 **SHIFT + ENTER**。 您會看到如下列程式碼片段所示的輸出。 時間戳記的組合和數字"Like"s 擷取至事件中樞的輸出中的每個事件。

    Sent event: {"timestamp":"2019-04-24T09:39:40.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:38:48.000Z","favorite":1}

    Sent event: {"timestamp":"2019-04-24T09:38:36.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:37:27.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:37:00.000Z","favorite":2}

    Sent event: {"timestamp":"2019-04-24T09:31:11.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:30:15.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:30:02.000Z","favorite":1}

    ...
    ...

## <a name="read-tweets-from-event-hubs"></a>從事件中樞讀取推文

在  **AnalyzeTweetsFromEventHub** notebook 中貼上下列程式碼，並將預留位置取代為您您稍早建立的異常偵測器資源的值。 此 Notebook 會讀取您稍早使用 **SendTweetsToEventHub** Notebook 串流至事件中樞的推文。

首先，撰寫用戶端呼叫異常偵測器。 
```scala

//
// Anomaly Detection Client
//

import java.io.{BufferedReader, DataOutputStream, InputStreamReader}
import java.net.URL
import java.sql.Timestamp

import com.google.gson.{Gson, GsonBuilder, JsonParser}
import javax.net.ssl.HttpsURLConnection

case class Point(var timestamp: Timestamp, var value: Double)
case class Series(var series: Array[Point], var maxAnomalyRatio: Double, var sensitivity: Int, var granularity: String)
case class AnomalySingleResponse(var isAnomaly: Boolean, var isPositiveAnomaly: Boolean, var isNegativeAnomaly: Boolean, var period: Int, var expectedValue: Double, var upperMargin: Double, var lowerMargin: Double, var suggestedWindow: Int)
case class AnomalyBatchResponse(var expectedValues: Array[Double], var upperMargins: Array[Double], var lowerMargins: Array[Double], var isAnomaly: Array[Boolean], var isPositiveAnomaly: Array[Boolean], var isNegativeAnomaly: Array[Boolean], var period: Int)

object AnomalyDetector extends Serializable {

  // Cognitive Services API connection settings
  val subscriptionKey = "[Placeholder: Your Anomaly Detector resource access key]"
  val endpoint = "[Placeholder: Your Anomaly Detector resource endpoint]"
  val latestPointDetectionPath = "/anomalydetector/v1.0/timeseries/last/detect"
  val batchDetectionPath = "/anomalydetector/v1.0/timeseries/entire/detect";
  val latestPointDetectionUrl = new URL(endpoint + latestPointDetectionPath)
  val batchDetectionUrl = new URL(endpoint + batchDetectionPath)
  val gson: Gson = new GsonBuilder().setDateFormat("yyyy-MM-dd'T'HH:mm:ss.SSS'Z'").setPrettyPrinting().create()

  def getConnection(path: URL): HttpsURLConnection = {
    val connection = path.openConnection().asInstanceOf[HttpsURLConnection]
    connection.setRequestMethod("POST")
    connection.setRequestProperty("Content-Type", "text/json")
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey)
    connection.setDoOutput(true)
    return connection
  }

  // Handles the call to Cognitive Services API.
  def processUsingApi(request: String, path: URL): String = {
    println(request)
    val encoded_text = request.getBytes("UTF-8")
    val connection = getConnection(path)
    val wr = new DataOutputStream(connection.getOutputStream())
    wr.write(encoded_text, 0, encoded_text.length)
    wr.flush()
    wr.close()

    val response = new StringBuilder()
    val in = new BufferedReader(new InputStreamReader(connection.getInputStream()))
    var line = in.readLine()
    while (line != null) {
      response.append(line)
      line = in.readLine()
    }
    in.close()
    return response.toString()
  }

  // Calls the Latest Point Detection API.
  def detectLatestPoint(series: Series): Option[AnomalySingleResponse] = {
    try {
      println("Process Timestamp: " + series.series.apply(series.series.length-1).timestamp.toString + ", size: " + series.series.length)
      val response = processUsingApi(gson.toJson(series), latestPointDetectionUrl)
      println(response)
      // Deserializing the JSON response from the API into Scala types
      val anomaly = gson.fromJson(response, classOf[AnomalySingleResponse])
      Thread.sleep(5000)
      return Some(anomaly)
    } catch {
      case e: Exception => {
        println(e)
        e.printStackTrace()
        return None
      }
    }
  }

  // Calls the Batch Detection API.
  def detectBatch(series: Series): Option[AnomalyBatchResponse] = {
    try {
      val response = processUsingApi(gson.toJson(series), batchDetectionUrl)
      println(response)
      // Deserializing the JSON response from the API into Scala types
      val anomaly = gson.fromJson(response, classOf[AnomalyBatchResponse])
      Thread.sleep(5000)
      return Some(anomaly)
    } catch {
      case e: Exception => {
        println(e)
        return None
      }
    }
  }
}
```

若要執行 Notebook，請按 **SHIFT + ENTER**。 您會看到如下列程式碼片段所示的輸出。

    import java.io.{BufferedReader, DataOutputStream, InputStreamReader}
    import java.net.URL
    import java.sql.Timestamp
    import com.google.gson.{Gson, GsonBuilder, JsonParser}
    import javax.net.ssl.HttpsURLConnection
    defined class Point
    defined class Series
    defined class AnomalySingleResponse
    defined class AnomalyBatchResponse
    defined object AnomalyDetector

然後準備未來使用的彙總函式。
```scala
//
// User Defined Aggregation Function for Anomaly Detection
//

import org.apache.spark.sql.Row
import org.apache.spark.sql.expressions.{MutableAggregationBuffer, UserDefinedAggregateFunction}
import org.apache.spark.sql.types.{StructType, TimestampType, FloatType, MapType, BooleanType, DataType}
import scala.collection.immutable.ListMap

class AnomalyDetectorAggregationFunction extends UserDefinedAggregateFunction {
  override def inputSchema: StructType = new StructType().add("timestamp", TimestampType).add("value", FloatType)
  
  override def bufferSchema: StructType = new StructType().add("point", MapType(TimestampType, FloatType))
  
  override def dataType: DataType = BooleanType
  
  override def deterministic: Boolean = false
  
  override def initialize(buffer: MutableAggregationBuffer): Unit = {
    buffer(0) = Map()
  }
  
  override def update(buffer: MutableAggregationBuffer, input: Row): Unit = {
    buffer(0) = buffer.getAs[Map[java.sql.Timestamp, Float]](0) + (input.getTimestamp(0) -> input.getFloat(1))
  }
  
  override def merge(buffer1: MutableAggregationBuffer, buffer2: Row): Unit = {
    buffer1(0) = buffer1.getAs[Map[java.sql.Timestamp, Float]](0) ++ buffer2.getAs[Map[java.sql.Timestamp, Float]](0)
  }
  
  override def evaluate(buffer: Row): Any = {
    val points = buffer.getAs[Map[java.sql.Timestamp, Float]](0)
    if (points.size > 12) {
      val sorted_points = ListMap(points.toSeq.sortBy(_._1.getTime):_*)
      var detect_points: List[Point] = List()
      sorted_points.keys.foreach {
        key => detect_points = detect_points :+ new Point(key, sorted_points(key))
      }
      
      
      // 0.25 is maxAnomalyRatio. It represents 25%, max anomaly ratio in a time series.
      // 95 is the sensitivity of the algorithms.
      // Check Anomaly detector API reference (https://aka.ms/anomaly-detector-rest-api-ref)
      
      val series: Series = new Series(detect_points.toArray, 0.25, 95, "hourly")
      val response: Option[AnomalySingleResponse] = AnomalyDetector.detectLatestPoint(series)
      if (!response.isEmpty) {
        return response.get.isAnomaly
      }
    }
    
    return None
  }
}

```

若要執行 Notebook，請按 **SHIFT + ENTER**。 您會看到如下列程式碼片段所示的輸出。

    import org.apache.spark.sql.Row
    import org.apache.spark.sql.expressions.{MutableAggregationBuffer, UserDefinedAggregateFunction}
    import org.apache.spark.sql.types.{StructType, TimestampType, FloatType, MapType, BooleanType, DataType}
    import scala.collection.immutable.ListMap
    defined class AnomalyDetectorAggregationFunction

從事件中樞，以便進行異常偵測，然後載入資料。 將預留位置取代為您稍早建立 Azure 事件中樞值。

```scala
//
// Load Data from Eventhub
//

import org.apache.spark.eventhubs._
import org.apache.spark.sql.types._
import org.apache.spark.sql.functions._

val connectionString = ConnectionStringBuilder("[Placeholder: EventHub namespace connection string]")
  .setEventHubName("[Placeholder: EventHub name]")
  .build

val customEventhubParameters =
  EventHubsConf(connectionString)
  .setConsumerGroup("$Default")
  .setMaxEventsPerTrigger(100)

val incomingStream = spark.readStream.format("eventhubs").options(customEventhubParameters.toMap).load()

val messages =
  incomingStream
  .withColumn("enqueuedTime", $"enqueuedTime".cast(TimestampType))
  .withColumn("body", $"body".cast(StringType))
  .select("enqueuedTime", "body")

val bodySchema = new StructType().add("timestamp", TimestampType).add("favorite", IntegerType)

val msgStream = messages.select(from_json('body, bodySchema) as 'fields).select("fields.*")

msgStream.printSchema

display(msgStream)

```

輸出現在會類似下列映像。 請注意您在資料表中的日期可能不同於本教學課程中的日期，因為資料是即時。
![載入資料從事件中樞](../media/tutorials/load-data-from-eventhub.png "負載資料從事件中樞")

您現在已使用 Apache Spark 的事件中樞連接器，以近乎即時的方式將資料從 Azure 事件中樞串流至 Azure Databricks。 如需如何使用 Spark 事件中樞連接器的詳細資訊，請參閱[連接器文件](https://github.com/Azure/azure-event-hubs-spark/tree/master/docs)。



## <a name="run-anomaly-detection-on-tweets"></a>對推文執行異常偵測

在本節中，您可以執行異常偵測使用異常偵測器 API 收到的推文。 在本節中，您會將程式碼片段新增至相同的 **AnalyzeTweetsFromEventHub** Notebook。

若要執行異常偵測，首先，您要依小時彙總計量的計數。
```scala
//
// Aggregate Metric Count by Hour
//

// If you want to change granularity, change the groupBy window. 
val groupStream = msgStream.groupBy(window($"timestamp", "1 hour"))
  .agg(avg("favorite").alias("average"))
  .withColumn("groupTime", $"window.start")
  .select("groupTime", "average")

groupStream.printSchema

display(groupStream)
```
輸出現在會類似下列的程式碼片段。
```
groupTime                       average
2019-04-23T04:00:00.000+0000    24
2019-04-26T19:00:00.000+0000    47.888888888888886
2019-04-25T12:00:00.000+0000    32.25
2019-04-26T09:00:00.000+0000    63.4
...
...

```

然後取得差異彙總的輸出結果。 異常偵測需要較長的 [記錄] 視窗，因為我們使用差異保留歷程記錄資料，您要偵測的點。 取代"[版面配置區： 資料表名稱]"限定的差異資料表名稱 （例如，「 tweet 」） 建立。 取代"[預留位置： 檢查點的資料夾名稱]"的字串值是唯一的每次您執行此程式碼 (比方說，「 etl-從-事件中樞-20190605")。
若要深入了解在 Azure Databricks 上的差異 Lake，請參閱[差異 Lake 指南](https://docs.azuredatabricks.net/delta/index.html)


```scala
//
// Output Aggregation Result to Delta
//

groupStream.writeStream
  .format("delta")
  .outputMode("complete")
  .option("checkpointLocation", "/delta/[Placeholder: table name]/_checkpoints/[Placeholder: folder name for checkpoints]")
  .table("[Placeholder: table name]")

```

取代"[版面配置區： 資料表名稱]"具有相同的差異資料表名稱中，您已選取上面。
```scala
//
// Show Aggregate Result
//

val twitterCount = spark.sql("SELECT COUNT(*) FROM [Placeholder: table name]")
twitterCount.show()

val twitterData = spark.sql("SELECT * FROM [Placeholder: table name] ORDER BY groupTime")
twitterData.show(200, false)

display(twitterData)
```
輸出如下所示： 
```
groupTime                       average
2019-04-08T01:00:00.000+0000    25.6
2019-04-08T02:00:00.000+0000    6857
2019-04-08T03:00:00.000+0000    71
2019-04-08T04:00:00.000+0000    55.111111111111114
2019-04-08T05:00:00.000+0000    2203.8
...
...

```

現在，彙總的時間序列資料持續被內嵌的差異。 然後您可以排程每小時的作業，以偵測異常的最新的點。 取代"[版面配置區： 資料表名稱]"具有相同的差異資料表名稱中，您已選取上面。

```scala
//
// Anomaly Detection
//

import java.time.Instant
import java.time.format.DateTimeFormatter
import java.time.ZoneOffset
import java.time.temporal.ChronoUnit

val detectData = spark.read.format("delta").table("[Placeholder: table name]")

// You could use Databricks to schedule an hourly job and always monitor the latest data point
// Or you could specify a const value here for testing purpose
// For example, val endTime = Instant.parse("2019-04-16T00:00:00Z")
val endTime = Instant.now()

// This is when your input of anomaly detection starts. It is hourly time series in this tutorial, so 72 means 72 hours ago from endTime.
val batchSize = 72
val startTime = endTime.minus(batchSize, ChronoUnit.HOURS)

val DATE_TIME_FORMATTER = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss").withZone(ZoneOffset.UTC);

val series = detectData.filter($"groupTime" <= DATE_TIME_FORMATTER.format(endTime))
  .filter($"groupTime" > DATE_TIME_FORMATTER.format(startTime))
  .sort($"groupTime")

series.createOrReplaceTempView("series")

//series.show()

// Register the function to access it
spark.udf.register("anomalydetect", new AnomalyDetectorAggregationFunction)

val adResult = spark.sql("SELECT '" + endTime.toString + "' as datetime, anomalydetect(groupTime, average) as anomaly FROM series")
adResult.show()
```
結果，如下所示： 

```
+--------------------+-------+
|           timestamp|anomaly|
+--------------------+-------+
|2019-04-16T00:00:00Z|  false|
+--------------------+-------+


That's it! Using Azure Databricks, you have successfully streamed data into Azure Event Hubs, consumed the stream data using the Event Hubs connector, and then run anomaly detection on streaming data in near real time.
Although in this tutorial, the granularity is hourly, you can always change the granularity to meet your need. 

## Clean up resources

After you have finished running the tutorial, you can terminate the cluster. To do so, in the Azure Databricks workspace, select **Clusters** from the left pane. For the cluster you want to terminate, move the cursor over the ellipsis under **Actions** column, and select the **Terminate** icon and then select **Confirm**.

![Stop a Databricks cluster](../media/tutorials/terminate-databricks-cluster.png "Stop a Databricks cluster")

If you don't manually terminate the cluster it will automatically stop, provided you selected the **Terminate after \_\_ minutes of inactivity** checkbox while creating the cluster. In such a case, the cluster will automatically stop if it has been inactive for the specified time.

## Next steps

In this tutorial, you learned how to use Azure Databricks to stream data into Azure Event Hubs and then read the streaming data from Event Hubs in real time. Advance to the next tutorial to learn how to call the Anomaly Detector API and visualize anomalies using Power BI desktop. 

> [!div class="nextstepaction"]
>[Batch anomaly detection with Power BI desktop](batch-anomaly-detection-powerbi.md)
