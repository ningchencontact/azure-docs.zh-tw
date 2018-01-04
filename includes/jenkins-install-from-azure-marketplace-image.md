1. 在瀏覽器中開啟[Azure Marketplace 映像的 Jenkins](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.jenkins?tab=Overview)。

1. 選取**取得 IT 現在**。

    ![選取 GIT IT 現在開始 Jenkins Marketplace 映像的安裝程序。](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-get-it-now.png)

1. 檢閱定價的詳細資料和詞彙資訊之後，選取**繼續**。

    ![Jenkins Marketplace 映像價格及使用規定資訊。](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-pricing-and-terms.png)

1. 選取**建立**在 Azure 入口網站中設定 Jenkins 伺服器。 

    ![安裝 Jenkins Marketplace 映像。](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-create.png)

1. 在**基本概念**索引標籤上，指定下列值：

    - **名稱**-輸入`Jenkins`。
    - **使用者**-輸入要登入 Jenkins 執行所在的虛擬機器時使用的使用者名稱。
    - **驗證類型**-選取**密碼**。
    - **密碼**-輸入要登入 Jenkins 執行所在的虛擬機器時使用的密碼。
    - **確認密碼**-請重新輸入登入 Jenkins 執行所在的虛擬機器時使用的密碼。
    - **Jenkins 發行類型**-選取**LTS**。
    - **訂用帳戶**-選取您要安裝 Jenkins Azure 訂用帳戶。
    - **資源群組**-選取**建立新**，然後輸入做為組成 Jenkins 安裝的資源集合的邏輯容器的資源群組的名稱。
    - **位置**-選取**美國東部**。

    ![在 [基本] 索引標籤中輸入 Jenkins 的驗證和資源群組的資訊。](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-basic.png)

1. 選取**確定**繼續前往**設定** 索引標籤。 

1. 在**設定**索引標籤上，指定下列值：

    - **大小**-選取您 Jenkins 的虛擬機器的適當調整大小選項。
    - **VM 磁碟類型**-指定任一個 （硬碟磁碟機） 的 HDD 或 SSD （固態磁碟機），表示哪些儲存磁碟類型所允許之 Jenkins 虛擬機器。
    - **公用 IP 位址**-Jenkins 名稱且尾碼為 IP 先前頁面中指定的 IP 位址名稱預設值。 您可以選取選項來變更該預設值。
    - **網域名稱標籤**-Jenkins 虛擬機器的完整 url 中指定的值。

    ![在 [設定] 索引標籤中輸入 Jenkins 虛擬機器的設定。](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-settings.png)

1. 選取**確定**繼續前往**摘要** 索引標籤。

1. 當**摘要**索引標籤會顯示，驗證輸入的資訊。 一旦您看到**驗證傳遞**訊息中，選取**確定**。 

    ![[摘要] 索引標籤會顯示，並驗證您選取的選項。](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-summary.png)

1. 當**建立**索引標籤會顯示，請選取**建立**建立 Jenkins 虛擬機器。 準備您的伺服器時，通知會顯示在 Azure 入口網站。

    ![Jenkins 是就緒的通知。](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-notification.png)