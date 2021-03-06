---
title: Bing 影像搜尋單頁 Web 應用程式 | Microsoft Docs
description: 說明如何在單頁 Web 應用程式中使用 Bing 影像搜尋 API。
services: cognitive-services
author: v-jerkin
manager: ehansen
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 10/04/2017
ms.author: v-jerkin
ms.openlocfilehash: d0e1dc24513c8fc3a405cf1c18f531a0c58fad13
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2018
ms.locfileid: "35369987"
---
# <a name="tutorial-single-page-web-app"></a>教學課程：單頁 Web 應用程式

Bing 影像搜尋 API 可讓您搜尋網頁，並取得與搜尋查詢相關的結果。 在本教學課程中，我們會建置單頁 Web 應用程式，以使用 Bing 影像搜尋 API 直接在頁面中顯示搜尋結果。 該應用程式包含 HTML、CSS 和 JavaScript 元件。

<!-- Remove until we can sanitize images
![[Single-page Bing Image Search app]](media/cognitive-services-bing-images-api/image-search-spa-demo.png)
-->

> [!NOTE]
> 點按時，頁面底部的 JSON 和 HTTP 標題會顯示 JSON 回應和 HTTP 要求資訊。 這些詳細資料有助於探索服務。

本教學課程應用程式說明如何：

> [!div class="checklist"]
> * 在 JavaScript 中執行 Bing 影像搜尋 API 呼叫
> * 將搜尋選項傳遞給 Bing 影像搜尋 API
> * 顯示搜尋結果
> * 逐頁檢視搜尋結果
> * 處理 Bing 用戶端識別碼和 API 訂用帳戶金鑰
> * 處理可能會發生的錯誤

本教學課程頁面完全獨立，不會使用任何外部架構、樣式表甚或影像檔案。 其中僅使用廣受支援的 JavaScript 語言功能，並且與所有主要網頁瀏覽器的目前版本搭配使用。

在本教學課程中，我們只會討論原始程式碼的特定部分。 完整的原始程式碼位於[另一個頁面上](tutorial-bing-image-search-single-page-app-source.md)。 將此程式碼複製並貼到文字編輯器，並儲存為 `bing.html`。

> [!NOTE]
> 本教學課程與[單頁 Bing Web 搜尋應用程式教學課程](../Bing-Web-Search/tutorial-bing-web-search-single-page-app.md)非常類似，但只會處理影像搜尋結果。

## <a name="app-components"></a>應用程式元件

如同任何單頁 Web 應用程式，本教學課程應用程式包含三個部分：

> [!div class="checklist"]
> * HTML - 定義頁面的結構和內容
> * CSS - 定義頁面的外觀
> * JavaScript - 定義頁面的行為

本教學課程並未詳細說明大部分的 HTML 或 CSS，因為這些元件相當簡單。

HTML 包含搜尋表單，使用者可在其中輸入查詢並選擇搜尋選項。 該表單會透過 `<form>` 標籤的 `onsubmit` 屬性，連線至實際執行搜尋的 JavaScript：

```html
<form name="bing" onsubmit="return newBingImageSearch(this)">
```

`onsubmit` 處理常式會傳回 `false`，這可防止表單提交至伺服器。 JavaScript 程式碼會實際從表單收集所需的資訊，並執行搜尋。

HTML 也包含顯示搜尋結果的區域 (HTML `<div>` 標籤)。

## <a name="managing-subscription-key"></a>管理訂用帳戶金鑰

為了避免必須在程式碼中包含 Bing 搜尋 API 訂用帳戶金鑰，我們使用瀏覽器的永續性儲存體來儲存金鑰。 若未儲存金鑰，我們會提示使用者提供金鑰，並加以儲存供後續使用。 如果 API 稍後拒絕金鑰，我們會讓儲存的金鑰失效，以再次提示使用者。

我們會定義 `storeValue` 和 `retrieveValue` 函式，以使用 `localStorage` 物件 (如果瀏覽器支援的話) 或 Cookie。 我們的 `getSubscriptionKey()` 函式會使用這些函式來儲存及擷取使用者的金鑰。

```javascript
// cookie names for data we store
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";

BING_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/images/search";

// ... omitted definitions of storeValue() and retrieveValue()

// get stored API subscription key, or prompt if it's not found
function getSubscriptionKey() {
    var key = retrieveValue(API_KEY_COOKIE);
    while (key.length !== 32) {
        key = prompt("Enter Bing Search API subscription key:", "").trim();
    }
    // always set the cookie in order to update the expiration date
    storeValue(API_KEY_COOKIE, key);
    return key;
}
```

HTML `<form>` 標籤 `onsubmit` 會呼叫 `bingWebSearch` 函式來傳回搜尋結果。 `bingWebSearch` 會使用 `getSubscriptionKey` 來驗證每個查詢。 如先前的定義所示，如果尚未輸入金鑰，`getSubscriptionKey` 就會提示使用者輸入金鑰。 其後應儲存金鑰，以供應用程式繼續使用。

```html
<form name="bing" onsubmit="this.offset.value = 0; return bingWebSearch(this.query.value, 
    bingSearchOptions(this), getSubscriptionKey())">
```

## <a name="selecting-search-options"></a>選取搜尋選項

![[Bing 影像搜尋表單]](media/cognitive-services-bing-images-api/image-search-spa-form.png)

HTML 表單包含下列控制項：

| | |
|-|-|
|`where`|可供選取市場 (位置和語言) 以用於搜尋的下拉式功能表。|
|`query`|要在其中輸入搜尋字詞的文字欄位。|
|`aspect`|選項按鈕用於選擇找到的映像比例：大致是方形、寬或高。|
|`color`|選取彩色或黑白，或主要的顏色。
|`when`|可選擇性地將搜尋限定為最近一天、一週或一個月的下拉式功能表。|
|`safe`|指出是否要使用 Bing 的安全搜尋功能來篩選掉「成人」結果的核取方塊。|
|`count`|隱藏的欄位。 毎個要求要傳回的搜尋結果數目。 變更為每頁顯示更多或更少結果。|
|`offset`|隱藏的欄位。 要求中第一個搜尋結果的位移；用於分頁。 它會在新要求執行時重設為 `0`。|
|`nextoffset`|隱藏的欄位。 收到搜尋結果後，此欄位會設為回應中的 `nextOffset` 值。 使用此欄位可避免連續頁面上的重複結果。|
|`stack`|隱藏的欄位。 JSON 編碼之上一頁搜尋結果的位移清單，用於導覽回上一個頁面。|

> [!NOTE]
> Bing 影像搜尋提供更多查詢參數。 我們在此僅使用其中一些參數。

我們的 JavaScript 函式 `bingSearchOptions()` 會將這些欄位轉換成 Bing 搜尋 API 所需格式的部分查詢字串。

```javascript
// build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));
    if (form.when.value.length) options.push("freshness=" + form.when.value);
    var aspect = "all";
    for (var i = 0; i < form.aspect.length; i++) {
        if (form.aspect[i].checked) {
            aspect = form.aspect[i].value;
            break;
        }
    }
    options.push("aspect=" + aspect);
    if (form.color.value) options.push("color=" + form.color.value);
    options.push("count=" + form.count.value);
    options.push("offset=" + form.offset.value);
    return options.join("&");
}
```

例如，安全搜尋功能可以是 `strict`、`moderate` 或 `off`，預設為 `moderate`。 但我們的表單使用只有兩種狀態的核取方塊。 JavaScript 程式碼會將此設定轉換成 `strict` 或 `off` (我們不使用 `moderate`)。

## <a name="performing-the-request"></a>執行要求

假設有查詢、選項字串和 API 金鑰，`BingImageSearch`　函式會使用 `XMLHttpRequest` 物件向 Bing 影像搜尋端點提出要求。

```javascript
// perform a search given query, options string, and API key
function bingImageSearch(query, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("results", "related", "_json", "_http", "paging1", "paging2", "error");

    var request = new XMLHttpRequest();
    var queryurl = BING_ENDPOINT + "?q=" + encodeURIComponent(query) + "&" + options;

    // open the request
    try {
        request.open("GET", queryurl);
    } 
    catch (e) {
        renderErrorMessage("Bad request (invalid URL)\n" + queryurl);
        return false;
    }

    // add request headers
    request.setRequestHeader("Ocp-Apim-Subscription-Key", key);
    request.setRequestHeader("Accept", "application/json");
    var clientid = retrieveValue(CLIENT_ID_COOKIE);
    if (clientid) request.setRequestHeader("X-MSEdge-ClientID", clientid);
    
    // event handler for successful response
    request.addEventListener("load", handleBingResponse);
    
    // event handler for erorrs
    request.addEventListener("error", function() {
        renderErrorMessage("Error completing request");
    });

    // event handler for aborted request
    request.addEventListener("abort", function() {
        renderErrorMessage("Request aborted");
    });

    // send the request
    request.send();
    return false;
}
```

HTTP 要求成功完成時，JavaScript 會呼叫我們的 `load` 事件處理常式 `handleBingResponse()` 函式，來處理成功傳遞至 API 的 HTTP GET 要求。 

```javascript
// handle Bing search request results
function handleBingResponse() {
    hideDivs("noresults");

    var json = this.responseText.trim();
    var jsobj = {};

    // try to parse JSON results
    try {
        if (json.length) jsobj = JSON.parse(json);
    } catch(e) {
        renderErrorMessage("Invalid JSON response");
    }

    // show raw JSON and HTTP request
    showDiv("json", preFormat(JSON.stringify(jsobj, null, 2)));
    showDiv("http", preFormat("GET " + this.responseURL + "\n\nStatus: " + this.status + " " + 
        this.statusText + "\n" + this.getAllResponseHeaders()));

    // if HTTP response is 200 OK, try to render search results
    if (this.status === 200) {
        var clientid = this.getResponseHeader("X-MSEdge-ClientID");
        if (clientid) retrieveValue(CLIENT_ID_COOKIE, clientid);
        if (json.length) {
            if (jsobj._type === "Images") {
                if (jsobj.nextOffset) document.forms.bing.nextoffset.value = jsobj.nextOffset;
                renderSearchResults(jsobj);
            } else {
                renderErrorMessage("No search results in JSON response");
            }
        } else {
            renderErrorMessage("Empty response (are you sending too many requests too quickly?)");
        }
    }

    // Any other HTTP response is an error
    else {
        // 401 is unauthorized; force re-prompt for API key for next request
        if (this.status === 401) invalidateSubscriptionKey();

        // some error responses don't have a top-level errors object, so gin one up
        var errors = jsobj.errors || [jsobj];
        var errmsg = [];

        // display HTTP status code
        errmsg.push("HTTP Status " + this.status + " " + this.statusText + "\n");

        // add all fields from all error responses
        for (var i = 0; i < errors.length; i++) {
            if (i) errmsg.push("\n");
            for (var k in errors[i]) errmsg.push(k + ": " + errors[i][k]);
        }

        // also display Bing Trace ID if it isn't blocked by CORS
        var traceid = this.getResponseHeader("BingAPIs-TraceId");
        if (traceid) errmsg.push("\nTrace ID " + traceid);

        // and display the error message
        renderErrorMessage(errmsg.join("\n"));
    }
}
```

> [!IMPORTANT]
> 成功的 HTTP 要求*不*一定表示搜尋本身成功。 如果搜尋作業中發生錯誤，Bing 影像搜尋 API 會傳回非 200 HTTP 狀態碼，並在 JSON 回應中包含錯誤資訊。 此外，如果要求速率受到限制，API 會傳回空白回應。

上述兩個函式中的大部分程式碼都是專用於錯誤處理。 下列階段可能會發生錯誤：

|階段|可能的錯誤|處理者|
|-|-|-|
|建置 JavaScript 要求物件|無效的 URL|`try`/`catch` 區塊|
|提出要求|網路錯誤、已中止連線|`error` 和 `abort` 事件處理常式|
|執行搜尋|無效的要求、無效的 JSON、速率限制|`load` 事件處理常式中的測試|

錯誤是透過呼叫 `renderErrorMessage()` 來處理，並提供有關錯誤的任何已知詳細資料。 如果回應通過錯誤測試的所有挑戰，我們會呼叫 `renderSearchResults()` 在頁面中顯示搜尋結果。

## <a name="displaying-search-results"></a>顯示搜尋結果

用於顯示搜尋結果的主要函式是 `renderSearchResults()`。 此函式會接受 Bing 影像搜尋服務所傳回的 JSON，並轉譯影像和相關的搜尋 (如果有的話)。

```javascript
function renderSearchResults(results) {

    // add Prev / Next links with result count
    var pagingLinks = renderPagingLinks(results);
    showDiv("paging1", pagingLinks);
    showDiv("paging2", pagingLinks);
    
    showDiv("results", renderImageResults(results.value));
    if (results.relatedSearches)
        showDiv("sidebar", renderRelatedItems(results.relatedSearches));
}
```

主要的影像搜尋結果會傳回成 JSON 回應中的最上層 `value` 物件。 我們將這些結果傳遞給函式 `renderImageResults()`，它會逐一查看這些結果，並呼叫個別的函式將每個項目轉譯成 HTML。 產生的 HTML 將傳回給 `renderSearchResults()`，在其中它會插到頁面中的 `results` 區域。

```javascript
function renderImageResults(items) {
    var len = items.length;
    var html = [];
    if (!len) {
        showDiv("noresults", "No results.");
        hideDivs("paging1", "paging2");
        return "";
    }
    for (var i = 0; i < len; i++) {
        html.push(searchItemRenderers.images(items[i], i, len));
    }
    return html.join("\n\n");
}
```

Bing 影像搜尋 API 最多傳回四種不同的相關結果，每個都出現在它自己的最上層物件中。 如下：

|||
|-|-|
|`pivotSuggestions`|將原始搜尋中的樞紐字組取代為不同樞紐字組的查詢。 比方說，如果您搜尋「紅色花卉」，樞紐字組可能是「紅色」，而樞紐建議可能是「黃色花卉」。|
|`queryExpansions`|藉由新增多個字詞以縮小原始搜尋範圍的查詢。 比方說，如果您搜尋 "Microsoft Surface"，可能是查詢擴充可能是 "Microsoft Surface Pro"。|
|`relatedSearches`|也已由輸入原始搜尋的其他使用者輸入的查詢。 比方說，如果您搜尋「雷尼爾山」，相關搜尋可能是「 聖海倫山」。|
|`similarTerms`|與原始搜尋的意義類似的查詢。 例如，如果您搜尋「小貓」，類似的字詞可能是「可愛」。|

如 `renderSearchResults()` 先前所示，我們只會轉譯 `relatedItems` 建議，並將產生的連結置於頁面的資訊看板中。

## <a name="rendering-result-items"></a>轉譯結果項目

在我們的 JavaScript 程式碼中有一個物件 `searchItemRenderers`，其中包含 *renderers:* 函式，可為每種搜尋結果產生 HTML。

```javascript
searchItemRenderers = { 
    images: function(item, index, count) { ... },
    relatedSearches: function(item) { ... }
}
```

轉譯器函式可接受下列參數：

| | |
|-|-|
|`item`|JavaScript 物件，其中包含項目的屬性，例如其 URL 及其描述。|
|`index`|集合內結果項目的索引。|
|`count`|搜尋結果項目集合中的項目數目。|

`index` 和 `count` 參數可用來編號結果、為集合開頭或結尾產生特殊 HTML、在特定數量的項目之後插入分行符號等。 如果轉譯器不需要此功能，則不需要接受這兩個參數。

讓我們仔細看看 `images` 轉譯器：

```javascript
    images: function (item, index, count) {
        var height = 120;
        var width = Math.max(Math.round(height * item.thumbnail.width / item.thumbnail.height), 120);
        var html = [];
        if (index === 0) html.push("<p class='images'>");
        var title = escape(item.name) + "\n" + getHost(item.hostPageDisplayUrl);
        html.push("<p class='images' style='max-width: " + width + "px'>");
        html.push("<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width + 
            "' height=" + height + " width=" + width + "'>");
        html.push("<br>");
        html.push("<nobr><a href='" + item.contentUrl + "'>Image</a> - ");
        html.push("<a href='" + item.hostPageUrl + "'>Page</a></nobr><br>");
        html.push(title.replace("\n", " (").replace(/([a-z0-9])\.([a-z0-9])/g, "$1.<wbr>$2") + ")</p>");
        return html.join("");
    }, // relatedSearches renderer omitted
```

影像轉譯器函式會：

> [!div class="checklist"]
> * 計算影像縮圖大小 (寬度變動，最小值為 120 像素，而高度固定為 90 像素)。
> * 建置 HTML `<img>` 標籤，以顯示影像縮圖。 
> * 建置 HTML `<a>` 標籤，以連結至影像和內含影像的頁面。
> * 建置描述，以顯示影像及執行所在網站的相關資訊。

我們會測試 `index` 變數，以便在第一個影像結果之前插入 `<p>` 標籤。 否則縮圖會在瀏覽器視窗中視需要彼此接合並圍繞。

縮圖大小用於這兩個 `<img>` 標籤，以及縮圖 URL 中的 `h` 和 `w` 欄位。 [Bing 縮圖服務](resize-and-crop-thumbnails.md)接著會提供完全符合該大小的縮圖。

## <a name="persisting-client-id"></a>保存用戶端識別碼

來自 Bing 搜尋 API 的回應可能會在後續要求中包含應該傳回 API 的 `X-MSEdge-ClientID` 標頭。 如果使用多個 Bing 搜尋 API，請盡可能對所有 API 使用相同的用戶端識別碼。

提供 `X-MSEdge-ClientID` 標頭可讓 Bing API 建立所有使用者搜尋的關聯，這有兩個重要的優點。

首先，它可讓 Bing 搜尋引擎將過去內容套用至搜尋結果，以尋找更符合使用者的結果。 例如，如果使用者之前搜尋與航行相關的字詞，稍後搜尋「節」可能會優先傳回航行中所使用節數的相關資訊。

其次，Bing 可能會隨機選取使用者來體驗新功能，再廣泛提供這些功能。 在每個要求中提供相同的用戶端識別碼，可確保已選擇看到功能的使用者一律會看到功能。 若沒有用戶端識別碼，使用者可能會在其搜尋結果中看到功能出現並消失，似乎很隨機。

瀏覽器安全性原則 (CORS) 可防止將 `X-MSEdge-ClientID` 標頭提供給 JavaScript 使用。 當搜尋回應的來源與要求回應的頁面不同時，就會發生這項限制。 在生產環境中，您應該裝載伺服器端指令碼，在與網頁相同的網域上執行 API 呼叫，以處理此原則。 由於指令碼的來源與網頁相同，因此 `X-MSEdge-ClientID` 標頭會接著提供給 JavaScript 使用。

> [!NOTE]
> 在生產 Web 應用程式中，無論如何都應該執行要求伺服器端。 否則，您的 Bing 搜尋 API 金鑰必須包含在網頁中，以提供給檢視來源的任何人。 您會根據 API 訂用帳戶金鑰的所有使用量付費，即使是未經授權的合作對象所提出的要求，因此請務必不要公開您的金鑰。

若要進行開發，您可以透過 CORS Proxy 提出 Bing Web 搜尋 API 要求。 來自這類 Proxy 的回應包含 `Access-Control-Expose-Headers` 標頭，可將回應標頭列入白名單並提供給 JavaScript 使用。

您可以輕鬆地安裝 CORS Proxy，讓我們的教學課程應用程式存取用戶端識別碼標頭。 首先，請[安裝 Node.js](https://nodejs.org/en/download/) (如果尚未安裝)。 然後在命令視窗中發出下列命令：

    npm install -g cors-proxy-server

接下來，將 HTML 檔案中的 Bing Web 搜尋端點變更為：

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

最後，使用下列命令啟動 CORS Proxy：

    cors-proxy-server

當您使用教學課程應用程式時，請保持開啟命令視窗；關閉視窗會停止 Proxy。 在可展開之 [HTTP 標頭] 區段的搜尋結果下，您現在可以看到 `X-MSEdge-ClientID` 標頭 (及其他標頭)，並確認每個要求的此標頭都相同。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Bing 影像搜尋 API 參考](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) (英文)

