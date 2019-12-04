---
title: Azure AD Connect 雲端布建轉換
description: 本檔描述如何使用轉換來改變預設屬性對應。
author: billmath
ms.author: billmath
manager: davba
ms.date: 12/02/2019
ms.topic: article
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: 0d37fdb4ad0d385914aecd4ca62be498c5c0e7c5
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794467"
---
# <a name="transformations"></a>轉換

轉換可讓您變更屬性與使用雲端布建之 Azure AD 同步處理方式的預設行為。  

若要執行這項工作，您需要編輯架構，然後透過 web 要求重新提交。

如需雲端布建屬性的詳細資訊，請參閱[瞭解 Azure AD 架構](concept-attributes.md)。


## <a name="retrieve-the-schema"></a>取出架構
若要取出架構，請使用[查看架構](concept-attributes.md#viewing-the-schema)中所述的步驟。 


## <a name="custom-attribute-mapping"></a>自訂屬性對應
若要加入自訂屬性對應，請使用下列程式。

1. 將架構複製到文字或程式碼編輯器，例如[Visual Studio Code](https://code.visualstudio.com/)。  
2. 在架構中找出您想要更新的物件 ![](media/how-to-transformation/transform1.png)</br>
3. 找出使用者物件下的**ExtensionAttribute3**程式碼。

    ```
                            {
                                "defaultValue": null,
                                "exportMissingReferences": false,
                                "flowBehavior": "FlowWhenChanged",
                                "flowType": "Always",
                                "matchingPriority": 0,
                                "targetAttributeName": "ExtensionAttribute3",
                                "source": {
                                    "expression": "Trim([extensionAttribute3])",
                                    "name": "Trim",
                                    "type": "Function",
                                    "parameters": [
                                        {
                                            "key": "source",
                                            "value": {
                                                "expression": "[extensionAttribute3]",
                                                "name": "extensionAttribute3",
                                                "type": "Attribute",
                                                "parameters": []
                                            }
                                        }
                                    ]
                                }
                            },
    ```
 4.  編輯程式碼，使 company 屬性對應至 ExtensionAttribute3。
    ```
                            {
                                "defaultValue": null,
                                "exportMissingReferences": false,
                                "flowBehavior": "FlowWhenChanged",
                                "flowType": "Always",
                                "matchingPriority": 0,
                                "targetAttributeName": "ExtensionAttribute3",
                                "source": {
                                    "expression": "Trim([company])",
                                    "name": "Trim",
                                    "type": "Function",
                                    "parameters": [
                                        {
                                            "key": "source",
                                            "value": {
                                                "expression": "[company]",
                                                "name": "company",
                                                "type": "Attribute",
                                                "parameters": []
                                            }
                                        }
                                    ]
                                }
                            },
    ```
 5. 將架構複製回 [圖形瀏覽器]，將要求類型變更為 [PUT] 和 [**執行查詢**]。  
 ![](media/how-to-transformation/transform2.png)</br>
 6.  現在，在 Azure 入口網站中，流覽至雲端布建設定，然後**重新開機**布建。
 ![](media/how-to-transformation/transform3.png)</br>
 7.  稍待一段時間之後，請在 Graph Explorer 中執行下列查詢來確認屬性是否已填入： `https://graph.microsoft.com/beta/users/{Azure AD user UPN}`。
 8.  您現在應該會看到此值。
 ![](media/how-to-transformation/transform4.png)</br>

## <a name="custom-attribute-mapping-with-function"></a>使用 function 的自訂屬性對應
如需更先進的對應，您可以使用可讓您運算元據的函式，並建立屬性的值以符合您的組織需求。

若要執行這項工作，只需遵循上述步驟，然後編輯用來建立最終值的函數。

如需運算式語法和範例的詳細資訊，請參閱[在 Azure Active Directory 中撰寫屬性對應的運算式。](reference-expressions.md)


## <a name="next-steps"></a>後續步驟 

- [什麼是布建？](what-is-provisioning.md)
- [什麼是 Azure AD Connect 雲端布建？](what-is-cloud-provisioning.md)
