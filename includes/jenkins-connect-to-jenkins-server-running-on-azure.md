1. 在瀏覽器中瀏覽至您的 Jenkins 虛擬機器。 由於無法透過不安全的 HTTP 存取 Jenkins 儀表板，會顯示訊息，指出您需要使用 SSH 通道中將虛擬機器。

    ![Jenkins 提供說明如何使用 SSH 連接到 Jenkins 虛擬機器的資訊。](./media/jenkins-connect-to-jenkins-server-running-on-azure/jenkins-ssh-instructions.png)

1. 開啟 bash 或終端機視窗，SSH 存取。

1. 輸入下列`ssh`命令時，取代**&lt;使用者名稱 >**和**&lt;網域 >**預留位置建立 Jenkins 時指定的值取代虛擬機器：

    ```bash
    ssh -L 127.0.0.1:8080:localhost:8080 <username>@<domain>.eastus.cloudapp.azure.com
    ```

1. 請遵循`ssh`提示使用者登入 Jenkins 虛擬機器。

1. 輸入下列命令，以擷取解除鎖定 Jenkins 初始密碼。

    ```bash
    sudo cat /var/lib/jenkins/secrets/initialAdminPassword
    ```

1. Bash 或終端機視窗中會顯示密碼複製到剪貼簿。

1. 在瀏覽器中，瀏覽至 `http://localhost:8080`。

1. 先前擷取到的密碼貼入**系統管理密碼**欄位，然後選取**繼續**。

    ![Jenkins 會儲存您必須使用您連接到它的第一次解除鎖定 Jenkins 虛擬機器的初始密碼。](./media/jenkins-connect-to-jenkins-server-running-on-azure/jenkins-unlock.png)

1. 選取**安裝建議的外掛程式**。

    ![Jenkins 可讓您輕鬆地安裝建議的外掛程式初始項目時的集合](./media/jenkins-connect-to-jenkins-server-running-on-azure/jenkins-customize.png)

1. 在**建立第一位系統管理員使用者**頁面上，建立系統管理使用者和密碼 Jenkins 儀表板，然後選取**儲存並完成**。

1. 在**Jenkins 就緒 ！** 頁面上，選取**開始使用 Jenkins**。

    ![一旦 Jenkins 安裝並設定權限，您可以開始使用它來建立並建置工作。](./media/jenkins-connect-to-jenkins-server-running-on-azure/jenkins-ready.png)