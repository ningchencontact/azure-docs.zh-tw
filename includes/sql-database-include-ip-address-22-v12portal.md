
<!--
includes/sql-database-include-ip-address-22-v12portal.md

Latest Freshness check:  2016-03-21 , daleche.

As of circa 2015-09-04, the following topics might include this include:
articles/sql-database/sql-database-configure-firewall-settings.md
articles/sql-database/sql-database-connect-query.md


## Server-level firewall rules

### Add a server-level firewall rule through the new Azure portal
-->


1. 登入 [Azure 入口網站](https://portal.azure.com/)。

2. 在左側清單中選取**瀏覽**。 

3. 捲動並選取**SQL 伺服器**。 
   
    ![在入口網站中尋找您的 Azure SQL Database 伺服器][b21-FindServerInPortal]
4. 為了方便起見，最小化**瀏覽**刀鋒視窗。

5. 在篩選文字方塊中開始輸入您的伺服器名稱。 即會顯示您的資料列。

6. 選取您的伺服器的資料列。 即會顯示您伺服器的刀鋒視窗。

7. 在您伺服器刀鋒視窗中，選取**設定**。 

8. 選取**防火牆**。 
   
    ![選取 [設定] > 防火牆][b31-SettingsFirewallNavig]
9. 選取**新增用戶端 IP**。 第一個文字方塊中輸入新規則的名稱。

10. 輸入低和高 IP 位址值作為要啟用的範圍。
    
    * 可以是很方便具有最低值結尾**.0**和結尾的最高值**.255**。
    
    ![將一組 IP 位址範圍加入為允許][b41-AddRange]
11. 選取 [ **儲存**]。

<!-- Image references. -->

[b21-FindServerInPortal]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b21-v12portal-findsvr.png

[b31-SettingsFirewallNavig]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b31-v12portal-settingsfirewall.png

[b41-AddRange]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b41-v12portal-addrange.png



<!--
These includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-ip-address-22-v12portal.md
? includes/sql-database-include-ip-address-*.md
-->
