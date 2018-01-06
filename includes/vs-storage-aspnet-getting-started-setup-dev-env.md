## <a name="set-up-the-development-environment"></a>設定開發環境

本節逐步引導開發環境設定。 這包括建立 ASP.NET MVC 應用程式、 新增已連線的服務連接、 加入控制器，並指定必要的命名空間指示詞。

### <a name="create-an-aspnet-mvc-app-project"></a>建立 ASP.NET MVC 應用程式專案

1. 開啟 Visual Studio。

1. 從主功能表中，選取**檔案** > **新增** > **專案**。

1. 在**新專案**對話方塊中，選取**Web** > **ASP.NET Web 應用程式 (.NET Framework)**。 在**名稱**欄位中，指定**StorageAspNet**。 選取 [確定] 。

    ![新專案的螢幕擷取畫面 對話方塊](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-1.png)

1. 在**新的 ASP.NET Web 應用程式**對話方塊中，選取**MVC**，然後選取**確定**。

    ![螢幕擷取畫面的新 ASP.NET Web 應用程式 對話方塊](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-2.png)

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>使用已連線的服務連接到 Azure 儲存體帳戶

1. 在 [方案總管] 中，於專案上按一下滑鼠右鍵。

2. 從內容功能表中，選取**新增** > **已連接服務**。

1. 在**已連接服務**對話方塊中，選取**雲端儲存體與 Azure 儲存體**，然後選取**設定**。

    ![對話方塊中的 已連接服務的螢幕擷取畫面](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-3.png)

1. 在**Azure 儲存體**對話方塊中，選取要用於此教學課程中的 Azure 儲存體帳戶。 若要建立新的 Azure 儲存體帳戶，請選取**建立新的儲存體帳戶**，並完成表單。 在選取的現有儲存體帳戶，或建立一個新之後, 選取**新增**。 Visual Studio 安裝 NuGet 封裝的儲存體連接字串和 Azure 儲存體**Web.config**。

> [!TIP]
> 若要了解如何建立儲存體帳戶與[Azure 入口網站](https://portal.azure.com)，請參閱[建立儲存體帳戶](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account)。
>
> 您也可以建立儲存體帳戶使用[Azure PowerShell](../articles/storage/common/storage-powershell-guide-full.md)， [Azure CLI](../articles/storage/common/storage-azure-cli.md)，或[Azure 雲端殼層](../articles/cloud-shell/overview.md)。

