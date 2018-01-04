## <a name="set-up-the-development-environment"></a>設定開發環境

本節逐步解說設定開發環境，包括建立 ASP.NET MVC 應用程式、 新增連線服務連接、 加入控制器，並指定必要的命名空間指示詞。

### <a name="create-an-aspnet-mvc-app-project"></a>建立 ASP.NET MVC 應用程式專案

1. 開啟 Visual Studio。

1. 從主功能表選取 [檔案] -> [新增] -> [專案]

1. 在 [新增專案] 對話方塊上，指定下圖中醒目提示的選項︰

    ![建立 ASP.NET 專案](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-1.png)

1. 選取 [確定] 。

1. 在 [新增 ASP.NET 專案] 對話方塊上，指定下圖中醒目提示的選項︰

    ![指定 MVC](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-2.png)

1. 選取 [確定] 。

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>使用已連接的服務連接到 Azure 儲存體帳戶

1. 在 [方案總管] 中，用滑鼠右鍵按一下專案，然後從內容功能表中選取 [新增] > [已連接的服務]。

1. 在**加入已連接服務**對話方塊中，選取**雲端儲存體與 Azure 儲存體**，然後選取**設定**。

    ![已連接的服務對話方塊](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-3.png)

1. 在**Azure 儲存體**對話方塊中，選取要用於此教學課程中的 Azure 儲存體帳戶。  若要建立新的 Azure 儲存體帳戶，請按一下**建立新的儲存體帳戶**和完成的表單。  在選取的現有儲存體帳戶，或建立一個新之後, 按**新增**。  Visual Studio 會針對 Azure 儲存體的儲存體連接字串和安裝的 NuGet 套件**Web.config**。

> [!TIP]
> 若要了解如何建立儲存體帳戶與[Azure 入口網站](https://portal.azure.com)，請參閱[建立儲存體帳戶](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account)。
>
> Azure 儲存體帳戶也可以建立使用[Azure PowerShell](../articles/storage/common/storage-powershell-guide-full.md)， [Azure CLI](../articles/storage/common/storage-azure-cli.md)，或[Azure 雲端殼層](../articles/cloud-shell/overview.md)。

