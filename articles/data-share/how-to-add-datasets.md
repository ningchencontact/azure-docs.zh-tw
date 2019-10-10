---
title: 將資料集新增至現有的 Azure 資料共用預覽
description: 瞭解如何將資料集新增至 Azure 資料共用中的現有資料共用，並與相同的收件者共用。
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 8cc61307ae6664889f60469cc3ca65c840c43b86
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72169202"
---
# <a name="how-to-add-datasets-to-an-existing-share-in-azure-data-share-preview"></a>如何在 Azure 資料共用預覽中將資料集新增至現有的共用

本文說明如何使用 Azure 資料共用預覽，將資料集新增至預先存在的資料共用。 這可讓您與相同的收件者共用更多資料，而不需要建立新的共用。

如需有關如何在建立共用時加入資料集的詳細資訊，請參閱[共用資料](share-your-data.md)教學課程。

## <a name="navigate-to-a-sent-data-share"></a>流覽至已傳送的資料共用

在 Azure 資料共用預覽中，流覽至您已傳送的共用，然後選取 [**資料集**] 索引標籤。按一下 [ **+ 新增資料集**] 按鈕，以加入更多資料集。

![新增資料集](./media/how-to/how-to-add-datasets/add-datasets.png)

在右側面板中，選取您想要新增的資料集類型，然後按 **[下一步]** 。 選取您想要新增之資料的 [訂用帳戶] 和 [資源群組]。 使用下拉式箭號，尋找並核取要加入之資料旁的方塊。

![新增資料集](./media/how-to/how-to-add-datasets/add-datasets-side.png)

一旦您按一下 [**新增資料集**]，資料集會新增至您的共用。 注意：您的取用者必須觸發快照集，才能讓它們看到新的資料集。 如果已設定快照集設定，取用者會在下一個排程的快照集完成後，看到新的資料集。 若未設定快照集設定，取用者必須手動觸發資料的完整或增量複本，以接收更新。 如需快照集的詳細資訊，請參閱[快照](terminology.md)集。

## <a name="next-steps"></a>後續步驟
深入瞭解如何將收件者[新增至現有的資料共用](how-to-add-recipients.md)。