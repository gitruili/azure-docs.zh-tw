---
title: 使用模式來改善 LUIS 預測的教學課程 - Azure | Microsoft Docs
titleSuffix: Cognitive Services
description: 在本教學課程中，您將針對意圖使用模式來改善 LUIS 意圖和實體預測。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 07/30/2018
ms.author: diberry
ms.openlocfilehash: 355c1edd4fa7433e68a9c0e903f4f782203326fe
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2018
ms.locfileid: "39365873"
---
# <a name="tutorial-improve-app-with-patterns"></a>教學課程：使用模式來改善應用程式

在本教學課程中，您將使用模式來提升意圖和實體預測準確性。  

> [!div class="checklist"]
* 如何識別模式是否可協助您的應用程式
* 如何建立模式
* 如何確認模式預測改進情況

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>開始之前

如果您沒有[批次測試](luis-tutorial-batch-testing.md)教學課程中的人力資源應用程式，請將 JSON [匯入](luis-how-to-start-new-app.md#import-new-app)到 [LUIS](luis-reference-regions.md#luis-website) 網站中的新應用程式。 您可以在 [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-batchtest-HumanResources.json) GitHub 存放庫中找到要匯入的應用程式。

如果您想要保留原始的「人力資源」應用程式，請在 [[設定](luis-how-to-manage-versions.md#clone-a-version)] 頁面上複製該版本，並將其命名為 `patterns`。 複製是一個既可測試各種 LUIS 功能又不影響原始版本的絕佳方式。 

## <a name="patterns-teach-luis-common-utterances-with-fewer-examples"></a>模式使用較少的範例來教導 LUIS 常見的語句

由於人力資源定義域的本質，因此有一些常見的方式來詢問組織中的員工關係。 例如︰

|表達方式|
|--|
|Jill Jones 的上司是誰？|
|Jill Jones 是誰的上司？|

這些語句太相似，以致若不提供許多語句範例，便無法判斷各個語句中內容相關的唯一性。 藉由為意圖新增模式，無須提供許多語句範例，LUIS 便可學習意圖的常見語句模式。 

此意圖的範例範本語句包括：

|範本語句的範例|
|--|
|{Employee} 的上司是誰？|
|{Employee} 是誰的上司？|

模式可透過範本語句範例的方式來提供，其中包含用來識別實體及可忽略文字的語法。 模式是規則運算式比對與機器學習的組合。  範本語句範例搭配意圖語句，可讓 LUIS 更容易理解哪些語句符合意圖。

為了讓模式與語句進行比對，語句內的實體必須先比對範本語句中的實體。 不過，此範本無助於預測實體，只適用於意圖。 

**當模式可讓您提供較少的範例語句時，如果偵測不到實體，模式就不會進行比對。**

請記住，員工是在[清單實體教學課程](luis-quickstart-intent-and-list-entity.md)中建立的。

## <a name="create-new-intents-and-their-utterances"></a>建立新意圖及其語句

新增兩個新意圖：`OrgChart-Manager` 和 `OrgChart-Reports`。 當 LUIS 將預測傳回用戶端應用程式之後，意圖名稱就可用來作為用戶端應用程式中的函式名稱，而員工實體可用來作為該函式的參數。

```Javascript
OrgChart-Manager(employee){
    ///
}
```

1. 請確定您人力資源應用程式位於 LUIS 的 [建置] 區段。 選取右上方功能表列中的 [Build] \(建置\)，即可變更至此區段。 

2. 在 [意圖] 頁面上，選取 [建立新意圖]。 

3. 在快顯對話方塊方塊中輸入 `OrgChart-Manager`，然後選取 [完成]。

    ![建立新的訊息快顯視窗](media/luis-tutorial-pattern/hr-create-new-intent-popup.png)

4. 將語句範例新增至意圖。

    |範例語句|
    |--|
    |誰是 John W.Smith 的部屬？|
    |John W. Smith 的上司是誰？|
    |John W. Smith 的經理是誰？|
    |Jill Jones 的直屬上司是誰？|
    |Jill Jones 的主管是誰？|

    [![LUIS 將新語句新增至意圖的螢幕擷取畫面](media/luis-tutorial-pattern/hr-orgchart-manager-intent.png "LUIS 將新語句新增至意圖的螢幕擷取畫面")](media/luis-tutorial-pattern/hr-orgchart-manager-intent.png#lightbox)

    如果是在意圖的語句 (而不是員工實體的語句) 中標示 keyPhrase 實體，也沒關係。 這兩者都可在端點的 [測試] 窗格中正確預測。 

5. 在左側導覽中，選取 [意圖]。

6. 選取 [Create new intent] \(建立新意圖\)。 

7. 在快顯對話方塊方塊中輸入 `OrgChart-Reports`，然後選取 [完成]。

8. 將語句範例新增至意圖。

    |範例語句|
    |--|
    |John W.Smith 的部屬有哪些員工？|
    |John W. Smith 是誰的上司？|
    |John W. Smith 要管理誰？|
    |Jill Jones 是哪些員工的直屬上司？|
    |Jill Jones 是誰的主管？|

## <a name="caution-about-example-utterance-quantity"></a>關於範例語句數量的注意事項

在這些意圖中，範例語句的數量不足以正確地將 LUIS 定型。 在真實世界的應用程式中，每個意圖至少應該有 15 個語句，並具備各種不同的文字選擇和語句長度。 這幾個語句是特別選取來醒目提示模式。 

## <a name="train-the-luis-app"></a>進行 LUIS 應用程式定型

[!include[LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-get-the-endpoint-url"></a>發佈應用程式以取得端點 URL

[!include[LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-with-a-different-utterance"></a>使用不同的語句來查詢端點

1. [!include[LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. 移至位址中的 URL 結尾並輸入 `Who is the boss of Jill Jones?`。 最後一個 querystring 參數是 `q`，也就是 **query** 語句。 

    ```JSON
    {
        "query": "who is the boss of jill jones?",
        "topScoringIntent": {
            "intent": "OrgChart-Manager",
            "score": 0.353984952
        },
        "intents": [
            {
                "intent": "OrgChart-Manager",
                "score": 0.353984952
            },
            {
                "intent": "OrgChart-Reports",
                "score": 0.214128986
            },
            {
                "intent": "EmployeeFeedback",
                "score": 0.08434003
            },
            {
                "intent": "MoveEmployee",
                "score": 0.019131
            },
            {
                "intent": "GetJobInformation",
                "score": 0.004819009
            },
            {
                "intent": "Utilities.Confirm",
                "score": 0.0043958663
            },
            {
                "intent": "Utilities.StartOver",
                "score": 0.00312064588
            },
            {
                "intent": "Utilities.Cancel",
                "score": 0.002265454
            },
            {
                "intent": "Utilities.Help",
                "score": 0.00133465114
            },
            {
                "intent": "None",
                "score": 0.0011388344
            },
            {
                "intent": "Utilities.Stop",
                "score": 0.00111166481
            },
            {
                "intent": "FindForm",
                "score": 0.0008900076
            },
            {
                "intent": "ApplyForJob",
                "score": 0.0007836131
            }
        ],
        "entities": [
            {
                "entity": "jill jones",
                "type": "Employee",
                "startIndex": 19,
                "endIndex": 28,
                "resolution": {
                    "values": [
                        "Employee-45612"
                    ]
                }
            },
            {
                "entity": "boss of jill jones",
                "type": "builtin.keyPhrase",
                "startIndex": 11,
                "endIndex": 28
            }
        ]
    }
    ```

這個查詢成功了嗎？ 對於此定型週期而言並未成功。 最前面兩個意圖的分數非常接近。 因為 LUIS 定型每次都不會完全一樣，會有一些變化，所以，這兩個分數可能會在下一個定型週期中反轉。 結果可能會傳回錯誤的意圖。 

使用模式來讓正確意圖的分數大幅提高 (以百分比表示)，並拉大與下一個最高分數的差距。 

讓這裡的第二個瀏覽器視窗保持開啟。 您稍後會在本教學課程中使用到它。 

## <a name="add-the-template-utterances"></a>新增範本語句

1. 在上方功能表中，選取 [建置]。

2. 在左導覽窗格中的 [Improve app performance] \(改善應用程式效能\) 底下，選取 [Patterns] \(模式\)。

3. 選取 [OrgChart-Manager] 意圖，然後以一次一句的方式輸入下列範本語句，在每個範本語句之後選取 Enter：

    |範本語句|
    |:--|
    |誰是 {Employee} 的部屬[？]|
    |{Employee} 的上司是誰[？]|
    |{Employee}[的]經理是誰[？]|
    |{Employee} 的直屬上司是誰[？]|
    |{Employee}[的]主管是誰[？]|
    |{Employee} 的老闆是誰[？]|

    `{Employee}` 語法不僅會標示是哪一個實體，也會標示實體在範本語句內的位置。 

    含有角色的實體會使用包含角色名稱的語法，並在[角色的個別教學課程](luis-tutorial-pattern-roles.md)中加以說明。 

    選擇性的語法 `[]` 會標記選擇性的單字或標點符號。 LUIS 會比對語句，並略過括號內的選擇性文字。

    如果您輸入範本語句，LUIS 會在您輸入左大括號 `{` 時，協助您填入實體。

    [![為意圖輸入範本語句的螢幕擷取畫面](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png)](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png#lightbox)

4. 選取 [OrgChart-Reports] 意圖，然後以一次一句的方式輸入下列範本語句，在每個範本語句之後選取 Enter：

    |範本語句|
    |:--|
    |{Employee}[的]部署有哪些員工[？]|
    |{Employee} 是誰的上司[？]|
    |{Employee} 要管理誰[？]|
    |{Employee} 的直屬部署有哪些員工[？]|
    |{Employee} 是誰的主管[？]|
    |{Employee} 是誰的老闆[？]|

## <a name="query-endpoint-when-patterns-are-used"></a>使用模式時查詢端點

1. 再次將應用程式定型並發佈。

2. 將瀏覽器索引標籤切換回 [端點 URL] 索引標籤。

3. 移至位址中的 URL 結尾並輸入 `Who is the boss of Jill Jones?` 作為語句。 最後一個 querystring 參數是 `q`，也就是 **query** 語句。 

    ```JSON
    {
        "query": "who is the boss of jill jones?",
        "topScoringIntent": {
            "intent": "OrgChart-Manager",
            "score": 0.9999989
        },
        "intents": [
            {
                "intent": "OrgChart-Manager",
                "score": 0.9999989
            },
            {
                "intent": "OrgChart-Reports",
                "score": 7.616303E-05
            },
            {
                "intent": "EmployeeFeedback",
                "score": 7.84204349E-06
            },
            {
                "intent": "GetJobInformation",
                "score": 1.20674213E-06
            },
            {
                "intent": "MoveEmployee",
                "score": 7.91245157E-07
            },
            {
                "intent": "None",
                "score": 3.875E-09
            },
            {
                "intent": "Utilities.StartOver",
                "score": 1.49E-09
            },
            {
                "intent": "Utilities.Confirm",
                "score": 1.34545453E-09
            },
            {
                "intent": "Utilities.Help",
                "score": 1.34545453E-09
            },
            {
                "intent": "Utilities.Stop",
                "score": 1.34545453E-09
            },
            {
                "intent": "Utilities.Cancel",
                "score": 1.225E-09
            },
            {
                "intent": "FindForm",
                "score": 1.123077E-09
            },
            {
                "intent": "ApplyForJob",
                "score": 5.625E-10
            }
        ],
        "entities": [
            {
                "entity": "jill jones",
                "type": "Employee",
                "startIndex": 19,
                "endIndex": 28,
                "resolution": {
                    "values": [
                        "Employee-45612"
                    ]
                },
                "role": ""
            },
            {
                "entity": "boss of jill jones",
                "type": "builtin.keyPhrase",
                "startIndex": 11,
                "endIndex": 28
            }
        ]
    }
    ```

現在會大幅提高意圖預測。

## <a name="working-with-optional-text-and-prebuilt-entities"></a>使用選用文字和預先建置的實體

本教學課程中的前幾個模式範本語句有一些選用文字範例，例如使用字母 s 的所有格用法 (`'s`)，以及問號用法 (`?`)。 假設端點語句要說明經理和人力資源代表正在尋找歷史資料，以及公司內未來要發生的員工異動計畫。

範例語句如下：

|意圖|包含選用文字和預先建置實體的語句範例|
|:--|:--|
|OrgChart-Manager|`Who was Jill Jones manager on March 3?`|
|OrgChart-Manager|`Who is Jill Jones manager now?`|
|OrgChart-Manager|`Who will be Jill Jones manager in a month?`|
|OrgChart-Manager|`Who will be Jill Jones manager on March 3?`|

這裡的每句範例都使用動詞時態 `was`、`is`、`will be`，以及日期 `March 3`、`now` 和 `in a month`，而 LUIS 必須正確地預測這些內容。 請注意，最後兩個範例中，除了 `in` 和 `on` 之外，其他幾乎都使用相同的文字。

範本語句的範例：
|意圖|包含選用文字和預先建置實體的語句範例|
|:--|:--|
|OrgChart-Manager|`who was {Employee}['s] manager [[on]{datetimeV2}?`]|
|OrgChart-Manager|`who is {Employee}['s] manager [[on]{datetimeV2}?]`|
|OrgChart-Manager|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
|OrgChart-Manager|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

使用方括號 `[]`的選用語法可讓此選用文字輕鬆地加入範本語句，並且能以巢狀方式 `[[]]` 增加至第二個層級，然後包含實體或文字。

**問題：為什麼無法將最後兩個範例語句結合成單一範本語句？** 模式範本不支援 OR 語法。 若要同時擷取到 `in` 版本和 `on` 版本，每個版本都必須是個別的範本語句。

**問題：為什麼所有 `w` 字母 (每個範本語句的第一個字母) 都是小寫？不能選擇大寫或小寫嗎？** 用戶端應用程式提交至查詢端點的語句會轉換成小寫。 範本語句可以是大寫或小寫，而端點語句也可以是大寫或小寫。 但比對一律會在轉換成小寫之後完成。

**問題：如果 March 3 (3 月 3 日) 會預測為數字 `3` 及日期 `March 3`，那為什麼預先建置的號碼不是範例語句的一部份？** 根據上下文，此範本語句使用的是日期，日期可以是常值 (`March 3`) 或抽象表示 (`in a month`)。 日期可以包含數字，但數字不一定會被視為日期。 每次使用實體時，該實體應要最能代表要在預測 JSON 結果中傳回的類型。  

**問題：為什麼剖析如 `Who will {Employee}['s] manager be on March 3?` 的語句時，效果很差。** 文法上，不同動詞時態必須是新的範本語句 (例如此處的 `will` 和 `be` 是分開的)。 現有範本語句並不符合此原則。 雖然語句的意圖沒有變更，但字組在語句中的位置已變更。 此變更會影響 LUIS 中的預測。

**請記住：系統會先找出實體，然後比對模式。**

## <a name="edit-the-existing-pattern-template-utterance"></a>編輯現有的模式範本語句

1. 在 LUIS 網站上，選取頂端功能表中的 [建置]，然後在左側功能表中選取 [模式]。 

2. 尋找現有的範本語句 `Who is {Employee}['s] manager[?]`，然後選取右側的省略符號 (...)。 

3. 從快顯功能表中選取 [編輯]。 

4. 將範本語句變更為：`who is {Employee}['s] manager [[on]{datetimeV2}?]]`

## <a name="add-new-pattern-template-utterances"></a>新增模式範本語句

1. 當您還在 [建置] 的 [模式] 中時，可以新增數個模式範本語句。 從意圖下拉式功能表中選取 [OrgChart Manager]，並輸入以下每個範本語句：

    |意圖|包含選用文字和預先建置實體的語句範例|
    |--|--|
    |OrgChart-Manager|`who was {Employee}['s] manager [[on]{datetimeV2}?]`|
    |OrgChart-Manager|`who is {Employee}['s] manager [[on]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

2. 將應用程式定型。

3. 在面板頂端選取 [測試]，以開啟測試面板。 

4. 輸入數個測試語句，以驗證模式符合，而且意圖分數相當高。 

    輸入第一個語句之後，請選取結果下方的 [檢查]，然後您就可以看到所有預測結果。

    |語句|
    |--|
    |Who will be Jill Jones manager (誰將會是 Jill Jones 經理)|
    |who will be jill jones's manager (誰將會是 jill jones 的經理)|
    |Who will be Jill Jones's manager? (誰將會是 Jill Jones 的經理？)|
    |who will be Jill jones manager on March 3 (在 3 月 3 日時，誰將會是 Jill jones 經理)|
    |Who will be Jill Jones manager next Month (下個月誰將會是 Jill jones 經理)|
    |Who will be Jill Jones manager in a month? (一個月後誰將會是 Jill jones 經理)|

所有這些語句中都可找到實體，因此這些語句皆符合相同模式，並且具有很高的預測分數。

## <a name="clean-up-resources"></a>清除資源

[!include[LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [了解如何將角色與模式搭配使用](luis-tutorial-pattern-roles.md)