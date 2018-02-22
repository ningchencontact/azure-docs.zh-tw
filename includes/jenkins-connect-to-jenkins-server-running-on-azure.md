1. 在瀏覽器中瀏覽至您的 Jenkins 虛擬機器。 由於無法透過不安全的 HTTP 存取 Jenkins 儀表板，所以會顯示訊息，指出您需要使用 SSH 通向虛擬機器。

    ![Jenkins 提供資訊，說明如何使用 SSH 連線到 Jenkins 虛擬機器。](./media/jenkins-connect-to-jenkins-server-running-on-azure/jenkins-ssh-instructions.png)

1. 開啟 bash 或終端機視窗，具有 SSH 存取權。

1. 輸入下列 `ssh` 命令，將 **&lt;username>** 和 **&lt;domain>** 預留位置取代為建立 Jenkins 虛擬機器時指定的值：

    ```bash
    ssh -L 127.0.0.1:8080:localhost:8080 <username>@<domain>.eastus.cloudapp.azure.com
    ```

1. 遵循 `ssh` 提示以登入 Jenkins 虛擬機器。

1. 輸入下列命令以擷取用來解除鎖定 Jenkins 的初始密碼。

    ```bash
    sudo cat /var/lib/jenkins/secrets/initialAdminPassword
    ```

1. 將 bash 或終端機視窗中顯示的密碼複製到剪貼簿。

1. 在瀏覽器中，瀏覽至 `http://localhost:8080`。

1. 將先前擷取的密碼貼至 [系統管理密碼] 欄位，然後選取 [繼續]。

    ![Jenkins 會儲存初始密碼，您必須在第一次連線到 Jenkins 虛擬機器時使用該密碼來解除鎖定它。](./media/jenkins-connect-to-jenkins-server-running-on-azure/jenkins-unlock.png)

1. 選取 [安裝建議的外掛程式]。

    ![Jenkins 可讓您輕鬆地在一開始安裝建議的外掛程式集合](./media/jenkins-connect-to-jenkins-server-running-on-azure/jenkins-customize.png)

1. 在 [建立第一個管理使用者] 分頁中，為 Jenkins 儀表板建立系統管理使用者和密碼，然後選取 [儲存後結束]。

1. 在 [Jenkins 已就緒！] 分頁上，選取 [開始使用 Jenkins]。

    ![一旦 Jenkins 安裝並設定，您就可以開始使用它來建立並建置作業。](./media/jenkins-connect-to-jenkins-server-running-on-azure/jenkins-ready.png)