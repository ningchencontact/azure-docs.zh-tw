---
title: 包含檔案
description: 包含檔案
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 0ed42e4ace17db1e681152589cc46d82c26dddff
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2018
ms.locfileid: "32202662"
---
## <a name="set-up-your-project"></a>設定專案

想要改為下載此範例的 Android Studio 專案嗎？ [下載專案](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip)並跳至[設定步驟](#register-your-application)，以在執行之前先設定程式碼範例。

### <a name="create-a-new-project"></a>建立新專案 
1.  開啟 Visual Studio，然後選取 [檔案] > [新增] > [新增專案]。
2.  為您的應用程式名稱，然後選取 [下一步]。
3.  選取 [API 21 或更新版本 (Android 5.0)]，然後選取 [下一步]。
4.  將 [空白活動] 保留原狀，選取 [下一步]，然後選取 [完成]。


### <a name="add-msal-to-your-project"></a>將 MSAL 新增至您的專案
1.  在 Android Studio 中，選取 [Gradle 指令碼] >  [build.gradle (模組: 應用程式)]。
2.  在 [相依性] 之下，貼上下列程式碼：

    ```ruby  
    compile ('com.microsoft.identity.client:msal:0.1.+') {
        exclude group: 'com.android.support', module: 'appcompat-v7'
    }
    compile 'com.android.volley:volley:1.0.0'
    ```

<!--start-collapse-->
### <a name="about-this-package"></a>關於此套件

上述程式碼中的套件會安裝 Microsoft Authentication Library。 MSAL 會處理使用者權杖的取得、快取及重新整理作業，而這些權杖用來存取受 Azure Active Directory v2 端點保護的 API。
<!--end-collapse-->

## <a name="create-the-application-ui"></a>建立應用程式 UI

1. 移至 [res] > [配置]，然後開啟 **activity_main.xml**。 
2. 將活動配置從 `android.support.constraint.ConstraintLayout` 或其他配置變更為 `LinearLayout`。
3. 將 `android:orientation="vertical"` 屬性新增至 `LinearLayout` 節點。
4. 將下列程式碼貼到 `LinearLayout` 節點中，並取代目前的內容：

    ```xml
    <TextView
        android:text="Welcome, "
        android:textColor="#3f3f3f"
        android:textSize="50px"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginLeft="10dp"
        android:layout_marginTop="15dp"
        android:id="@+id/welcome"
        android:visibility="invisible"/>

    <Button
        android:id="@+id/callGraph"
        android:text="Call Microsoft Graph"
        android:textColor="#FFFFFF"
        android:background="#00a1f1"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="200dp"
        android:textAllCaps="false" />

    <TextView
        android:text="Getting Graph Data..."
        android:textColor="#3f3f3f"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"
        android:id="@+id/graphData"
        android:visibility="invisible"/>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="0dip"
        android:layout_weight="1"
        android:gravity="center|bottom"
        android:orientation="vertical" >

        <Button
            android:text="Sign Out"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginBottom="15dp"
            android:textColor="#FFFFFF"
            android:background="#00a1f1"
            android:textAllCaps="false"
            android:id="@+id/clearCache"
            android:visibility="invisible" />
    </LinearLayout>
    ```
