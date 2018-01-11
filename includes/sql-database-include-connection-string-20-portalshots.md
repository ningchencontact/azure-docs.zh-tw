
<!--
includes/sql-database-include-connection-string-20-portalshots.md

Latest Freshness check:  2015-09-02 , GeneMi.

## Connection string
-->


### <a name="obtain-the-connection-string-from-the-azure-portal"></a>從 Azure 入口網站取得連接字串
使用[Azure 入口網站](https://portal.azure.com/)取得連接字串所需的用戶端程式與 Azure SQL Database 互動。 

1. 選取**瀏覽所有** > **SQL 資料庫**。

2. 在左上角附近的 [篩選] 文字方塊中輸入您的資料庫名稱**SQL 資料庫**刀鋒視窗。

3. 選取您的資料庫的資料列。

4. 刀鋒視窗中出現為您的資料庫，以便進行 select 視覺上的方便性之後**最小化**摺疊您用來瀏覽和篩選資料庫刀鋒視窗的按鈕。 
   
    ![篩選以隔離您的資料庫][10-FilterDatabase]
5. 在您的資料庫刀鋒視窗中，選取**顯示資料庫的連接字串**。

6. 如果您想要使用 ADO.NET 連線庫，請複製標示為 **ADO**的字串。 
   
    ![複製資料庫的 ADO 連接字串][20-CopyAdoConnectionString]
7. 以其中一種格式，將連接字串資訊貼入您的用戶端程式碼中。

如需詳細資訊，請參閱[連接字串和組態檔](http://msdn.microsoft.com/library/ms254494.aspx)。

<!-- Image references. -->

[10-FilterDatabase]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-a.png

[20-CopyAdoConnectionString]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-b.png


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->
