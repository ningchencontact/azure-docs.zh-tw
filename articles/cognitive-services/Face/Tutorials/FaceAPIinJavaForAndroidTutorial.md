---
title: 適用於 Android 的 Java 臉部 API 教學課程 | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 建立簡單 Android 應用程式，以使用認知服務臉部 API 偵測並框出影像中的臉部。
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 5164a261d482d0cca3842a973d2109b17999bd25
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2018
ms.locfileid: "35370379"
---
# <a name="getting-started-with-face-api-in-java-for-android-tutorial"></a>在適用於 Android 的 Java 中開始使用臉部 API 教學課程

在本教學課程中，您將學習如何建立和開發簡單 Android 應用程式，以叫用臉部 API 來偵測影像中的臉部。 應用程式會框出偵測到的臉部，來顯示結果。

![GettingStartedAndroid](../Images/android_getstarted2.1.PNG)

## <a name="preparation"></a> 準備

若要使用教學課程，您需要下列必要條件：

- 已安裝 Android Studio 和 SDK
- Android 裝置 (選擇性地用於測試)。

## <a name="step1"></a>步驟 1：訂閱臉部 API 並取得訂用帳戶金鑰

使用任何臉部 API 之前，您必須註冊以在 Microsoft 認知服務入口網站中訂閱臉部 API。 請參閱[訂用帳戶](https://azure.microsoft.com/try/cognitive-services/)。 本教學課程可以同時使用主要金鑰和次要金鑰。

## <a name="step2"></a>步驟 2：建立應用程式架構

在此步驟中，您將建立 Android 應用程式專案，來實作用於挑選和顯示影像的基本 UI。 請僅遵循下列指示： 

1. 開啟 Android Studio。
2. 從 [檔案] 功能表中，按一下 [新增專案...]
3. 將應用程式命名為 **MyFirstApp**，然後按一下 [下一步]。 

    ![GettingStartAndroidNewProject](../Images/AndroidNewProject.png)

4. 視需要選擇目標平台，然後按一下 [下一步]。 

    ![GettingStartAndroidNewProject2](../Images/AndroidNewProject2.png)

5. 選取 [基本活動]，然後按一下 [下一步]。
6. 如下命名活動，然後按一下 [完成]。 

    ![GettingStartAndroidNewProject4](../Images/AndroidNewProject4.png)

7. 開啟 **activity_main.xml**，您應該會看到此活動的配置編輯器。
8. 檢視文字來源檔，然後編輯活動配置，如下所示：

    ```xml
    <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools" android:layout_width="match_parent"
        android:layout_height="match_parent" android:paddingLeft="@dimen/activity_horizontal_margin"
        android:paddingRight="@dimen/activity_horizontal_margin"
        android:paddingTop="@dimen/activity_vertical_margin"
        android:paddingBottom="@dimen/activity_vertical_margin" tools:context=".MainActivity">
     
        <ImageView
            android:layout_width="match_parent"
            android:layout_height="fill_parent"
            android:id="@+id/imageView1"
            android:layout_above="@+id/button1" />
    
        <Button
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="Browse"
            android:id="@+id/button1"
            android:layout_alignParentBottom="true" />
    </RelativeLayout>
    ```  

9. 開啟 **MainActivity.java**，然後在檔案開頭插入下列 import 指示詞：

    ```java
    import java.io.*; 
    import android.app.*; 
    import android.content.*; 
    import android.net.*; 
    import android.os.*; 
    import android.view.*; 
    import android.graphics.*; 
    import android.widget.*; 
    import android.provider.*;
    ```
      
    其次，修改類別，如下所示：  
    
    ```java
    private final int PICK_IMAGE = 1;
    private ProgressDialog detectionProgressDialog;
         
    @Override
    protected void onCreate(Bundle savedInstanceState) {
           super.onCreate(savedInstanceState);
           setContentView(R.layout.activity_main);
           Button button1 = (Button)findViewById(R.id.button1);
           button1.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View v) {
                Intent gallIntent = new Intent(Intent.ACTION_GET_CONTENT);
                gallIntent.setType("image/*");
                startActivityForResult(Intent.createChooser(gallIntent, "Select Picture"), PICK_IMAGE);
            }
        });
         
        detectionProgressDialog = new ProgressDialog(this);
    }
    
    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if (requestCode == PICK_IMAGE && resultCode == RESULT_OK && data != null && data.getData() != null) {
            Uri uri = data.getData();
            try {
                Bitmap bitmap = MediaStore.Images.Media.getBitmap(getContentResolver(), uri);
                ImageView imageView = (ImageView) findViewById(R.id.imageView1);
                imageView.setImageBitmap(bitmap);
                } catch (IOException e) {
                e.printStackTrace();
                }
        }
    }
    ```

您的應用程式現在可以瀏覽資源庫中的相片，並在與下圖類似的視窗中顯示它：

![GettingStartAndroidUI](../Images/android_getstarted1.1.PNG)

## <a name="step3"></a>步驟 3：設定臉部 API 用戶端程式庫

臉部 API 是您可使用 HTTPS 要求所叫用的雲端 API。 針對在 .NET 平台應用程式中使用臉部 API 的更方便方式，也會提供用戶端程式庫來封裝 Web 要求。 在此範例中，我們使用用戶端程式庫來簡化工作。 

請遵循下列指示，設定用戶端程式庫： 

1. 從範例中顯示的 [專案] 面板，找到您專案的最上層 **build.gradle** 檔案。 請注意，專案樹狀目錄中有數個其他 **build.gradle** 檔案，而且您需要先開啟最上層 **build.gradle** 檔案。
2. 將 **mavenCentral()** 新增至您專案的存放庫。 您也可以使用為 Android Studio 預設存放庫的 jcenter()，因為 jcenter() 是 mavenCentral() 的超集。  

```
    allprojects {
        repositories {
            ...
            mavenCentral()
        }
    }
```

3. 開啟 'app' 專案中的 **build.gradle** 檔案。
4. 新增 Maven 中央存放庫中所儲存用戶端程式庫的相依性：

```
    dependencies {  
        ...  
        implementation 'com.microsoft.projectoxford:face:1.4.3'  
    }
```

5. 開啟 'app' 專案中的 **MainActivity.java**，然後插入下列 import 指示詞： 
    
    ```java
    import com.microsoft.projectoxford.face.*;  
    import com.microsoft.projectoxford.face.contract.*;  
    ```
    
   然後，在類別中插入下列程式碼：

    ```java
    private FaceServiceClient faceServiceClient = new FaceServiceRestClient("your API endpoint", "<Subscription Key>");
    ```

   將上面的第一個參數取代為步驟 1 中指派給您金鑰的 API 端點。 例如︰
   
        https://eastus2.api.cognitive.microsoft.com/face/v1.0
   
   將第二個參數取代為您在步驟 1 取得的訂用帳戶金鑰。
   
6. 開啟 'app' 專案中的 **AndroidManifest.xml** 檔案。 將下列項目插入為 **manifest** 項目的子系：  

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />  
    ```

7. 現在您已準備好從應用程式呼叫臉部 API。 

## <a name="step4"></a>步驟 4：上傳影像以偵測臉部

偵測臉部的最直接方式是直接上傳影像來呼叫 [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) (臉部 - 偵測) API。 使用用戶端程式庫時，做法是使用 **FaceServiceClient** 類別的非同步方法 **DetectAsync**。 每個傳回的臉部都會包含矩形來指出其位置，以及一系列的選擇性臉部屬性。 在本範例中，我們只需要擷取臉部位置。 在這裡，我們需要將方法插入至臉部偵測的 **MainActivity** 類別： 

```java

    // Detect faces by uploading face images
    // Frame faces after detection
    
    private void detectAndFrame(final Bitmap imageBitmap)
    {
        ByteArrayOutputStream outputStream = new ByteArrayOutputStream();
        imageBitmap.compress(Bitmap.CompressFormat.JPEG, 100, outputStream);
        ByteArrayInputStream inputStream = 
            new ByteArrayInputStream(outputStream.toByteArray());
        AsyncTask<InputStream, String, Face[]> detectTask =
            new AsyncTask<InputStream, String, Face[]>() {
                @Override
                protected Face[] doInBackground(InputStream... params) {
                    try {
                        publishProgress("Detecting...");
                        Face[] result = faceServiceClient.detect(
                                params[0], 
                                true,         // returnFaceId
                                false,        // returnFaceLandmarks
                                null           // returnFaceAttributes: a string like "age, gender"
                /* If you want value of FaceAttributes, try adding 4th argument like below.
                            new FaceServiceClient.FaceAttributeType[] {
                    FaceServiceClient.FaceAttributeType.Age,
                    FaceServiceClient.FaceAttributeType.Gender }
                */              
                        );
                        if (result == null)
                        {
                            publishProgress("Detection Finished. Nothing detected");
                            return null;
                        }
                        publishProgress(
                                String.format("Detection Finished. %d face(s) detected",
                                        result.length));
                        return result;
                    } catch (Exception e) {
                        publishProgress("Detection failed");
                        return null;
                    }
                }
                @Override
                protected void onPreExecute() {
                    //TODO: show progress dialog
                }
                @Override
                protected void onProgressUpdate(String... progress) {
                    //TODO: update progress
                }
                @Override
                protected void onPostExecute(Face[] result) {
                    //TODO: update face frames
                }
            };
        detectTask.execute(inputStream);
    }
```

## <a name="step5"></a>步驟 5：標示影像中的臉部

在這個最後一個步驟中，我們將上述所有步驟結合在一起，並使用框標示影像中偵測到的臉部。 首先，開啟 **MainActivity.java**，然後插入協助程式方法來繪製矩形： 

```java
    private static Bitmap drawFaceRectanglesOnBitmap(Bitmap originalBitmap, Face[] faces) {
        Bitmap bitmap = originalBitmap.copy(Bitmap.Config.ARGB_8888, true);
        Canvas canvas = new Canvas(bitmap);
        Paint paint = new Paint();
        paint.setAntiAlias(true);
        paint.setStyle(Paint.Style.STROKE);
        paint.setColor(Color.RED);
        int stokeWidth = 2;
        paint.setStrokeWidth(stokeWidth);
        if (faces != null) {
            for (Face face : faces) {
                FaceRectangle faceRectangle = face.faceRectangle;
                canvas.drawRect(
                        faceRectangle.left,
                        faceRectangle.top,
                        faceRectangle.left + faceRectangle.width,
                        faceRectangle.top + faceRectangle.height,
                        paint);
            }
        }
        return bitmap;
    }
```

現在在 **detectAndFrame** 方法中完成 TODO 部分，以框出臉部並報告狀態。

```java
    @Override
    protected void onPreExecute() {
        detectionProgressDialog.show();
    }
    @Override
    protected void onProgressUpdate(String... progress) {
        detectionProgressDialog.setMessage(progress[0]);
    }
    @Override
    protected void onPostExecute(Face[] result) {
        detectionProgressDialog.dismiss();
        if (result == null) return;
        ImageView imageView = (ImageView)findViewById(R.id.imageView1);
        imageView.setImageBitmap(drawFaceRectanglesOnBitmap(imageBitmap, result));
        imageBitmap.recycle();
    }
```
 
最後，從 **onActivityResult** 方法新增 **detectAndFrame** 方法呼叫，如下所示。 

```java
    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if (requestCode == PICK_IMAGE && resultCode == RESULT_OK && data != null && data.getData() != null) {
            Uri uri = data.getData();
            try {
                Bitmap bitmap = MediaStore.Images.Media.getBitmap(getContentResolver(), uri);
                ImageView imageView = (ImageView) findViewById(R.id.imageView1);
                imageView.setImageBitmap(bitmap);
     
                // This is the new addition.
                // detectAndFrame(bitmap);
     
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
```

執行此應用程式，並瀏覽包含臉部的影像。 請等候幾秒，讓雲端 API 回應。 之後，您將會取得與下圖類似的結果： 

![GettingStartAndroid](../Images/android_getstarted2.1.PNG)

## <a name="summary"></a> 摘要

在本教學課程中，您學習到使用臉部 API 的基本程序，並建立應用程式來顯示影像中的臉部表情。 如需臉部 API 的詳細資訊，請參閱操作說明和 [API Reference](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) (API 參考)。 

## <a name="related"></a> 相關教學課程

- [在 CSharp 中開始使用臉部 API 教學課程](FaceAPIinCSharpTutorial.md)
- [在 Python 中開始使用臉部 API 教學課程](FaceAPIinPythonTutorial.md)
