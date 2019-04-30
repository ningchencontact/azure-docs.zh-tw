---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: ba2985b8b6c92e299e8ab378263c9b4c062561d5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61489028"
---
#### <a name="to-mount-initialize-and-format-a-volume"></a>掛接、初始化及格式化磁碟區
1. 启动 Microsoft iSCSI 发起程序。
2. 在 [iSCSI 啟動器屬性] 視窗的 [探索] 索引標籤上，按一下 [探索入口網站]。
3. 在 [探索目標入口網站] 對話方塊中，提供已啟用 iSCSI 網路介面的 IP 位址，然後按一下 [確定]。 
4. 在 [iSCSI 啟動器屬性] 視窗的 [目標] 索引標籤上，找到 [探索到的目標]。 设备状态应显示为“非活动”。
5. 選取目標裝置，然後按一下 [連接]。 设备连接后，状态应更改为“已连接”。 (如需有關如何使用 Microsoft iSCSI 啟動器的詳細資訊，請參閱[安裝和設定 Microsoft iSCSI 啟動器][1])。
6. 在 Windows 主机上，按 Windows 徽标键 + X，然后单击“运行”。 
7. 在 [執行] 對話方塊中，輸入 **Diskmgmt.msc**。 按一下 [確定]，隨即會出現 [磁碟管理] 對話方塊。 右窗格將會顯示主機上的磁碟區。
8. 在 [磁碟管理]  視窗中，將會出現掛接的磁碟區，如下圖所示。 右键单击发现的卷（单击磁盘名称），然后单击“联机”。
   
     ![初始化格式化磁碟區](./media/storsimple-8000-mount-initialize-format-volume/step7initializeformatvolume.png) 
9. 再次以滑鼠右鍵按一下磁碟區 (按一下磁碟名稱)，然後按一下 [初始化] 。
10. 若要格式化簡單磁碟區，請執行下列步驟：
    
    1. 選取磁碟區、以滑鼠右鍵按一下該磁碟區 (按一下正確的區域)，然後按一下 [新增簡單磁碟區] 。
    2. 在 [新增簡單磁碟區] 精靈中，指定磁碟區大小和磁碟機代號，並將磁碟區設定為 NTFS 檔案系統。
    3. 指定 64 KB 分配单元大小。 這個配置單位大小適用於 StorSimple 解決方案中所使用的重複資料刪除演算法。
    4. 執行快速格式化。

<!--Link references-->
[1]: https://technet.microsoft.com/library/ee338480(WS.10).aspx
