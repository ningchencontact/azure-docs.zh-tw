
1. 在 [Azure 入口網站](https://portal.azure.com/)中，選取 [瀏覽全部] > [應用程式服務]。 然後選取您的 Mobile Apps 後端。 在 [設定] 下，選取 [App Service 推播]。 然後選取您的通知中樞名稱。
2. 移至 **Windows (WNS)**。 然後輸入您從線上服務網站取得的 [安全性金鑰] (用戶端秘密) 和 [封裝 SID]。 接下來，選取 [儲存]。

    ![在入口網站中設定 WNS 金鑰](./media/app-service-mobile-configure-wns/mobile-push-wns-credentials.png)

您的後端現在已設定成使用 WNS 傳送來傳送推播通知。
