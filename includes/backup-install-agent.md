## <a name="download-install-and-register-the-azure-backup-agent"></a>下載、安裝和註冊 Azure 備份代理程式
在建立之後復原服務保存庫，安裝用來將資料備份至 Azure Backup agent 每部 Windows 電腦 （Windows Server、 Windows 用戶端、 System Center Data Protection Manager 伺服器或 Azure 備份伺服器的電腦） 上。

1. 開啟您訂用帳戶[Azure 入口網站](https://ms.portal.azure.com/)。
2. 在左側功能表中，選取 [所有服務]，然後在服務清單中鍵入**復原服務**。 按一下 [復原服務保存庫] 。

   ![開啟復原服務保存庫](../articles/backup/media/tutorial-backup-windows-server-to-azure/full-browser-open-rs-vault_2.png)
3. 在 [快速啟動] 頁面上，按一下 [下載代理程式] 下方的 [適用於 Windows Server 或 System Center Data Protection Manager 或 Windows 用戶端] 選項。 按一下 [ **儲存** ] 將其複製到本機電腦。
   
    ![儲存代理程式](./media/backup-install-agent/agent.png)
4. 代理程式安裝之後，按兩下 MARSAgentInstaller.exe 啟動 Azure 備份代理程式的安裝。 選擇安裝資料夾和代理程式所需要的暫存資料夾。 指定的快取位置當做可用空間應該至少 5%的備份資料。
5. 若您使用 Proxy 伺服器連接至網際網路，請在 [ **Proxy 組態** ] 畫面上，輸入 Proxy 伺服器詳細資料。 若您使用已驗證的 Proxy，請在此畫面中輸入使用者名稱和密碼的詳細資料。
6. Azure 備份代理程式會安裝 .NET Framework 4.5 和 Windows PowerShell (若尚未安裝) 來完成安裝。
7. 安裝代理程式之後，按一下 [ **前往註冊** ] 按鈕以繼續與工作流程。
   
   ![註冊](./media/backup-install-agent/register.png)
8. 在 保存庫認證 畫面中，選取 下載保存庫認證檔案。
   
    ![保存庫認證](./media/backup-install-agent/vc.png)
   
    保存庫認證檔僅於 48 小時內有效 (從入口網站下載後起算)。 如果您在此畫面中遇到任何錯誤 (例如「提供的保存庫認證檔已過期」)，請登入 Azure 入口網站，並再次下載保存庫認證檔。
   
    請安裝應用程式可以存取保存庫認證檔案。 如果您遇到與存取相關的錯誤，將保存庫認證檔案複製到本機電腦上的暫存位置，然後再次嘗試操作。
   
    如果您遇到無效的保存庫認證錯誤，例如"提供無效的保存庫認證 」，在保存庫認證檔案已損毀或不具有最新的認證相關聯的復原服務。 從入口網站下載新的保存庫認證檔後重試作業。 這個錯誤通常會發生在使用者按一下時**下載保存庫認證**中快速且連續的 Azure 入口網站中的選項。 在這種情況下，只有第二個保存庫認證檔案無效。
9. 在 [ **加密設定** ] 畫面中，您可以產生複雜密碼或提供複雜密碼 (最少 16 個字元)。 請記得將複雜密碼存放在安全的位置。
   
    ![加密](./media/backup-install-agent/encryption.png)
   
   > [!WARNING]
   > 如果遺失或忘記複雜密碼，Microsoft 將無法協助您復原備份資料。 加密複雜密碼為使用者所有，Microsoft 將不會看到使用者所使用的複雜密碼。 視需要復原作業期間，將檔案儲存在安全的位置。
   > 
   > 
10. 一旦您按一下 [ **完成** ] 按鈕，電腦即成功註冊至保存庫，且現在您已準備好開始備份至 Microsoft Azure。
11. 在使用 Microsoft Azure 備份獨立，您可以修改即可註冊工作流程期間指定的設定**變更屬性**Azure 備份 MMC 嵌入式管理單元中的選項。
    
    ![變更屬性](./media/backup-install-agent/change.png)
    
    或者，使用 Data Protection Manager 時，您可以修改註冊工作流程期間所指定的設定，方法是選取 [管理] 索引標籤下的 [線上]，接著按一下 [設定] 選項。
    
    ![設定 Azure 備份](./media/backup-install-agent/configure.png)

