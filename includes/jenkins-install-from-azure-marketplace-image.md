1. 在瀏覽器中開啟[適用於 Jenkins 的 Azure Marketplace 映像](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.jenkins?tab=Overview)。

1. 選取 [立即取得]。

    ![選取 [立即取得] 以開始 Jenkins Marketplace 映像的安裝程序。](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-get-it-now.png)

1. 檢閱定價詳細資料和條款資訊之後，選取 [繼續]。

    ![Jenkins Marketplace 映像定價和條款資訊。](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-pricing-and-terms.png)

1. 選取 [建立] 以在 Azure 入口網站中設定 Jenkins 伺服器。 

    ![安裝 Jenkins Marketplace 映像。](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-create.png)

1. 在 [基本] 索引標籤中，指定下列值：

    - **Name** (名稱)：輸入 `Jenkins`。
    - **使用者** - 輸入當登入 Jenkins 執行所在的虛擬機器時要使用的使用者名稱。
    - **驗證類型** - 選取 [密碼]。
    - **密碼** - 輸入當登入 Jenkins 執行所在的虛擬機器時要使用的密碼。
    - **確認密碼** - 重新輸入當登入 Jenkins 執行所在的虛擬機器時要使用的密碼。
    - **Jenkins 發行類型** - 選取 [LTS]。
    - **訂用帳戶** - 選取您要在其中安裝 Jenkins 的 Azure 訂用帳戶。
    - **資源群組** - 選取 [新建]，然後為資源群組 (作為組成 Jenkins 安裝之資源集合的邏輯容器) 輸入名稱。
    - **位置** - 選取 [美國東部]。

    ![在 [基本] 索引標籤中輸入 Jenkins 的驗證和資源群組資訊。](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-basic.png)

1. 選取 [確定] 以繼續前往 [設定] 索引標籤。 

1. 在 [設定] 索引標籤中，指定下列值：

    - **大小** - 為您的 Jenkins 虛擬機器選取適當大小選項。
    - **VM 磁碟類型** - 指定 HDD (硬碟) 或 SSD (固態硬碟)，以表示 Jenkins 虛擬機器可使用哪種儲存磁碟類型。
    - **公用 IP 位址** - IP 位址名稱預設為您在上一頁中以尾碼為 -IP 指定的 Jenkins 名稱。 您可以選取選項來變更該預設值。
    - **網域名稱標籤** - 為 Jenkins 虛擬機器指定完整 URL 值。

    ![在 [設定] 索引標籤中輸入 Jenkins 的虛擬機器設定。](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-settings.png)

1. 選取 [確定] 以繼續前往 [摘要] 索引標籤。

1. 當 [摘要] 索引標籤顯示時，就會驗證輸入的資訊。 一旦您看到 [通過驗證] 訊息，請選取 [確定]。 

    ![[摘要] 索引標籤會顯示並驗證您選取的選項。](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-summary.png)

1. 當 [建立] 索引標籤顯示時，請選取 [建立] 以建立 Jenkins 虛擬機器。 當您的伺服器準備就緒時，會在 Azure 入口網站中顯示通知。

    ![Jenkins 已就緒通知。](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-notification.png)