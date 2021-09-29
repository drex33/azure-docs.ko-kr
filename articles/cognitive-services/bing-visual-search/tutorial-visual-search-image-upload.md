---
title: '자습서: Bing Visual Search API를 사용하여 이미지를 업로드하는 방법'
titleSuffix: Azure Cognitive Services
description: Bing에 이미지를 업로드하고, 이에 대한 인사이트를 얻고, 응답을 표시하는 방법을 알아봅니다.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 03/31/2020
ms.custom: devx-track-js
ms.openlocfilehash: 0f568f8b2aaf64d390a71dcb4b010dc399603706
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128663234"
---
# <a name="tutorial-upload-images-to-the-bing-visual-search-api"></a>자습서: Bing Visual Search API에 이미지 업로드

> [!WARNING]
> Bing Search API는 Cognitive Services에서 Bing Search Services로 이동합니다. **2020년 10월 30일** 부터 Bing Search의 모든 새 인스턴스는 [여기](/bing/search-apis/bing-web-search/create-bing-search-service-resource)에 설명된 프로세스에 따라 프로비저닝되어야 합니다.
> Cognitive Services를 사용하여 프로비저닝된 Bing Search API는 향후 3년 동안 또는 기업계약이 종료될 때까지(둘 중 먼저 도래할 때까지) 지원됩니다.
> 마이그레이션 지침은 [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource)를 참조하세요.

Bing Visual Search API를 사용하여 업로드하는 이미지와 유사한 이미지에 대한 웹을 검색할 수 있습니다. 이 자습서를 사용하여 이미지를 API에 보낼 수 있는 웹 애플리케이션을 만들고, 웹 페이지 내에 반환하는 인사이트를 표시합니다. 이 애플리케이션은 API를 사용하기 위해 모든 [Bing 사용 및 표시 요구 사항](../bing-web-search/use-display-requirements.md)을 준수하지 않습니다.

추가 오류 처리 및 주석이 포함된 이 샘플에 대한 전체 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchUploadImage.html)에서 찾을 수 있습니다.

자습서 앱은 다음 방법을 보여 줍니다.

> [!div class="checklist"]
> * Bing Visual Search API에 이미지 업로드
> * 웹 애플리케이션에 이미지 검색 결과 표시
> * API에서 제공되는 다양한 인사이트 살펴보기

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-structure-the-webpage"></a>웹 페이지 만들기 및 구조화

이미지를 Bing Visual Search API로 보내고, 인사이트를 받고, 이를 표시하는 HTML 페이지를 만듭니다. 즐겨찾는 편집기 또는 IDE에서 uploaddemo.html이라는 파일을 만듭니다. 이 파일에 다음 기본 HTML 구조를 추가합니다.

```html
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
    <head>
        <title>Visual Search Upload Demo</title>
    </head>

    <body>
    </body>
</html>
```

페이지를 요청 섹션과 응답 섹션으로 나눕니다. 요청 섹션에서는 사용자가 요청에 필요한 모든 정보를 제공하고, 응답 섹션에서는 인사이트가 표시됩니다. 다음 `<div>` 태그를 `<body>`에 추가합니다. `<hr>` 태그는 요청 섹션과 응답 섹션을 시각적으로 구분합니다.

```html
<div id="requestSection"></div>
<hr />
<div id="responseSection"></div>
```

애플리케이션에 대한 JavaScript가 포함되도록 `<script>` 태그를 `<head>` 태그에 추가합니다.

```html
<script>
<\script>
```

## <a name="get-the-upload-file"></a>업로드 파일 가져오기

애플리케이션에서는 사용자가 업로드할 이미지를 선택할 수 있도록 하기 위해 형식 특성을 `file`로 설정한 `<input>` 태그를 사용합니다. UI는 애플리케이션에서 Bing을 사용하여 검색 결과를 얻는다는 사실을 명확히 나타내야 합니다.

다음 `<div>`를 `requestSection` `<div>`에 추가합니다. 파일 입력은 형식(예:.jpg, .gif, .png)에 관계없이 단일 이미지 파일을 허용합니다. `onchange` 이벤트는 사용자가 파일을 선택할 때 호출되는 처리기를 지정합니다.

`<output>` 태그는 선택한 이미지의 미리 보기를 표시하는 데 사용됩니다.

```html
<div>
    <p>Select image to get insights from Bing:
        <input type="file" accept="image/*" id="uploadImage" name="files[]" size=40 onchange="handleFileSelect('uploadImage')" />
    </p>

    <output id="thumbnail"></output>
</div>
```

## <a name="create-a-file-handler"></a>파일 처리기 만들기

업로드하려는 이미지에서 읽을 수 있는 처리기 함수를 만듭니다. `FileList` 개체에서 파일을 반복하는 동안 처리기는 선택한 파일이 이미지 파일이며 해당 크기가 1MB 이하인지 확인해야 합니다. 이미지가 큰 경우 크기를 줄여서 업로드해야 합니다. 마지막으로, 처리기는 이미지의 미리 보기를 표시합니다.

```javascript
function handleFileSelect(selector) {

    var files = document.getElementById(selector).files; // A FileList object

    for (var i = 0, f; f = files[i]; i++) {

        // Ensure the file is an image file.
        if (!f.type.match('image.*')) {
            alert("Selected file must be an image file.");
            document.getElementById("uploadImage").value = null;
            continue;
        }

        // Image must be <= 1 MB and should be about 1500px.
        if (f.size > 1000000) {
            alert("Image must be less than 1 MB.");
            document.getElementById("uploadImage").value = null;
            continue;
        }

        var reader = new FileReader();

        // Capture the file information.
        reader.onload = (function(theFile) {
            return function(e) {
                var fileOutput = document.getElementById('thumbnail');

                if (fileOutput.childElementCount > 0) {
                    fileOutput.removeChild(fileOutput.lastChild);  // Remove the current pic, if it exists
                }

                // Render thumbnail.
                var span = document.createElement('span');
                span.innerHTML = ['<img class="thumb" src="', e.target.result,
                                    '" title="', escape(theFile.name), '"/>'].join('');
                fileOutput.insertBefore(span, null);
            };
        })(f);

        // Read in the image file as a data URL.
        reader.readAsDataURL(f);
    }
}
```

## <a name="add-and-store-a-subscription-key"></a>구독 키 추가 및 저장

애플리케이션에는 Bing Visual Search API를 호출하기 위해 구독 키가 필요합니다. 이 자습서의 경우 UI에서 제공합니다. 다음 `<input>` 태그(type 특성이 text로 설정됨)를 파일의 `<output>` 태그 바로 아래에 있는 `<body>`에 추가합니다.

```html
    <div>
        <p>Subscription key: 
            <input type="text" id="key" name="subscription" size=40 maxlength="32" />
        </p>
    </div>
```

이미지와 구독 키를 사용하여 Bing Visual Search를 호출하여 이미지에 대한 인사이트를 얻을 수 있습니다. 이 자습서에서는 호출에서 기본 시장(`en-us`)과 안전 검색 값(`moderate`)을 사용합니다.

이 애플리케이션에는 이러한 값을 변경하는 옵션이 있습니다. 다음 `<div>`를 `<div>` 구독 키 아래에 추가합니다. 애플리케이션은 `<select>` 태그를 사용하여 시장 및 안전 검색 값에 대한 드롭다운 목록을 제공합니다. 두 목록 모두 기본값을 표시합니다.

```html
<div>
    <p><a href="#" onclick="expandCollapse(options.id)">Query options</a></p>

    <div id="options" style="display:none">
        <p style="margin-left: 20px">Market: 
            <select id="mkt">
                <option value="es-AR">Argentina (Spanish)</option>
                <option value="en-AU">Australia (English)</option>
                <option value="de-AT">Austria (German)</option>
                <option value="nl-BE">Belgium (Dutch)</option>
                <option value="fr-BE">Belgium (French)</option>
                <option value="pt-BR">Brazil (Portuguese)</option>
                <option value="en-CA">Canada (English)</option>
                <option value="fr-CA">Canada (French)</option>
                <option value="es-CL">Chile (Spanish)</option>
                <option value="da-DK">Denmark (Danish)</option>
                <option value="fi-FI">Finland (Finnish)</option>
                <option value="fr-FR">France (French)</option>
                <option value="de-DE">Germany (German)</option>
                <option value="zh-HK">Hong Kong SAR(Traditional Chinese)</option>
                <option value="en-IN">India (English)</option>
                <option value="en-ID">Indonesia (English)</option>
                <option value="it-IT">Italy (Italian)</option>
                <option value="ja-JP">Japan (Japanese)</option>
                <option value="ko-KR">Korea (Korean)</option>
                <option value="en-MY">Malaysia (English)</option>
                <option value="es-MX">Mexico (Spanish)</option>
                <option value="nl-NL">Netherlands (Dutch)</option>
                <option value="en-NZ">New Zealand (English)</option>
                <option value="no-NO">Norway (Norwegian)</option>
                <option value="zh-CN">People's Republic of China (Chinese)</option>
                <option value="pl-PL">Poland (Polish)</option>
                <option value="pt-PT">Portugal (Portuguese)</option>
                <option value="en-PH">Philippines (English)</option>
                <option value="ru-RU">Russia (Russian)</option>
                <option value="ar-SA">Saudi Arabia (Arabic)</option>
                <option value="en-ZA">South Africa (English)</option>
                <option value="es-ES">Spain (Spanish)</option>
                <option value="sv-SE">Sweden (Swedish)</option>
                <option value="fr-CH">Switzerland (French)</option>
                <option value="de-CH">Switzerland (German)</option>
                <option value="zh-TW">Taiwan (Traditional Chinese)</option>
                <option value="tr-TR">Turkey (Turkish)</option>
                <option value="en-GB">United Kingdom (English)</option>
                <option value="en-US" selected>United States (English)</option>
                <option value="es-US">United States (Spanish)</option>
            </select>
        </p>
        <p style="margin-left: 20px">Safe search: 
            <select id="safesearch">
                <option value="moderate" selected>Moderate</option>
                <option value="strict">Strict</option>
                <option value="off">off</option>
            </select>
        </p>
    </div>
</div>
```

## <a name="add-search-options-to-the-webpage"></a>웹 페이지에 검색 옵션 추가

애플리케이션은 쿼리 옵션 링크로 제어되는 접을 수 있는 `<div>`에 목록을 숨깁니다. 쿼리 옵션 링크를 클릭하면 `<div>`가 펼쳐져 쿼리 옵션을 보고 수정할 수 있습니다. 쿼리 옵션 링크를 다시 클릭하면 `<div>`가 접혀 숨겨집니다. 다음 코드 조각에서는 쿼리 옵션 링크의 `onclick` 처리기를 보여 줍니다. 이 처리기는 `<div>` 펼치기 또는 접기 여부를 제어합니다. 이 처리기를 `<script>` 섹션에 추가합니다. 처리기는 데모의 접을 수 있는 모든 `<div>` 섹션에서 사용됩니다.

```javascript
// Contains the toggle state of divs.
var divToggleMap = {};  // divToggleMap['foo'] = 0;  // 1 = show, 0 = hide


// Toggles between showing and hiding the specified div.
function expandCollapse(divToToggle) {
    var div = document.getElementById(divToToggle);

    if (divToggleMap[divToToggle] == 1) {   // if div is expanded
        div.style.display = "none";
        divToggleMap[divToToggle] = 0;
    }
    else {                                  // if div is collapsed
        div.style.display = "inline-block";
        divToggleMap[divToToggle] = 1;
    }
}
```

## <a name="call-the-onclick-handler"></a>`onclick` 처리기 호출

다음 `"Get insights"` 단추를 본문의 `<div>` 옵션 아래에 추가합니다. 단추를 사용하면 호출을 시작할 수 있습니다. 단추를 클릭하면 커서가 회전하는 대기 커서로 변경되고 `onclick` 처리기가 호출됩니다.

```html
<p><input type="button" id="query" value="Get insights" onclick="document.body.style.cursor='wait'; handleQuery()" /></p>
```

단추의 `onclick` 처리기인 `handleQuery()`를 `<script>` 태그에 추가합니다.

## <a name="handle-the-query"></a>쿼리 처리

`handleQuery()` 처리기는 구독 키가 있고, 길이가 32자이며, 이미지가 선택되어 있는지 확인합니다. 또한 이전 쿼리의 모든 인사이트를 지웁니다. 이후에 `sendRequest()` 함수를 호출하여 호출을 수행합니다.

```javascript
function handleQuery() {
    var subscriptionKey = document.getElementById('key').value;

    // Make sure user provided a subscription key and image.
    // For this demo, the user provides the key but typically you'd
    // get it from secured storage.
    if (subscriptionKey.length !== 32) {
        alert("Subscription key length is not valid. Enter a valid key.");
        document.getElementById('key').focus();
        return;
    }

    var imagePath = document.getElementById('uploadImage').value;

    if (imagePath.length === 0)
    {
        alert("Please select an image to upload.");
        document.getElementById('uploadImage').focus();
        return;
    }

    var responseDiv = document.getElementById('responseSection');

    // Clear out the response from the last query.
    while (responseDiv.childElementCount > 0) {
        responseDiv.removeChild(responseDiv.lastChild);
    }

    // Send the request to Bing to get insights about the image.
    var f = document.getElementById('uploadImage').files[0];
    sendRequest(f, subscriptionKey);
}
```

## <a name="send-the-search-request"></a>검색 요청 보내기

`sendRequest()` 함수는 엔드포인트 URL의 형식을 지정하고, `Ocp-Apim-Subscription-Key` 헤더를 구독 키로 설정하고, 업로드할 이미지의 이진 파일을 추가하고, 응답 처리기를 지정하고, 호출을 수행합니다.

```javascript
function sendRequest(file, key) {
    var market = document.getElementById('mkt').value;
    var safeSearch = document.getElementById('safesearch').value;
    var baseUri = `https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch?mkt=${market}&safesearch=${safeSearch}`;

    var form = new FormData();
    form.append("image", file);

    var request = new XMLHttpRequest();

    request.open("POST", baseUri);
    request.setRequestHeader('Ocp-Apim-Subscription-Key', key);
    request.addEventListener('load', handleResponse);
    request.send(form);
}
```

## <a name="get-and-handle-the-api-response"></a>API 응답 가져오기 및 처리

`handleResponse()` 함수는 Bing Visual Search 호출에서 얻은 응답을 처리합니다. 호출이 성공하면 JSON 응답을 인사이트가 포함된 개별 태그로 구문 분석합니다. 다음으로 검색 결과를 페이지에 추가합니다. 그런 다음, 애플리케이션에서 표시되는 데이터의 양을 관리하기 위해 각 태그에 대해 접을 수 있는 `<div>`를 만듭니다. 처리기를 `<script>` 섹션에 추가합니다.

```javascript
function handleResponse() {
    if(this.status !== 200){
        alert("Error calling Bing Visual Search. See console log for details.");
        console.log(this.responseText);
        return;
    }

    var tags = parseResponse(JSON.parse(this.responseText));
    var h4 = document.createElement('h4');
    h4.textContent = 'Bing internet search results';
    document.getElementById('responseSection').appendChild(h4);
    buildTagSections(tags);

    document.body.style.cursor = 'default'; // reset the wait cursor set by query insights button
}
```

### <a name="parse-the-response"></a>응답 구문 분석

`parseResponse` 함수는 `json.tags`를 반복하여 JSON 응답을 사전 개체로 변환합니다.

```javascript
function parseResponse(json) {
    var dict = {};

    for (var i =0; i < json.tags.length; i++) {
        var tag = json.tags[i];

        if (tag.displayName === '') {
            dict['Default'] = JSON.stringify(tag);
        }
        else {
            dict[tag.displayName] = JSON.stringify(tag);
        }
    }

    return(dict);
}
```

### <a name="build-a-tag-section"></a>태그 섹션 빌드

`buildTagSections()` 함수는 구문 분석된 JSON 태그를 반복하고, `buildDiv()` 함수를 호출하여 각 태그에 대해 `<div>`를 빌드합니다. 각 태그는 링크로 표시됩니다. 링크를 클릭하면 태그가 확장되면서 태그와 연결된 인사이트가 표시됩니다. 링크를 다시 클릭하면 섹션이 접혀집니다.

```javascript
function buildTagSections(tags) {
    for (var tag in tags) {
        if (tags.hasOwnProperty(tag)) {
            var tagSection = buildDiv(tags, tag);
            document.getElementById('responseSection').appendChild(tagSection);
        }
    }  
}

function buildDiv(tags, tag) {
    var tagSection = document.createElement('div');
    tagSection.setAttribute('class', 'subSection');

    var link = document.createElement('a');
    link.setAttribute('href', '#');
    link.setAttribute('style', 'float: left;')
    link.text = tag;
    tagSection.appendChild(link);

    var contentDiv = document.createElement('div');
    contentDiv.setAttribute('id', tag);
    contentDiv.setAttribute('style', 'clear: left;')
    contentDiv.setAttribute('class', 'section');
    tagSection.appendChild(contentDiv);

    link.setAttribute('onclick', `expandCollapse("${tag}")`);
    divToggleMap[tag] = 0;  // 1 = show, 0 = hide

    addDivContent(contentDiv, tag, tags[tag]);

    return tagSection;
}
```

## <a name="display-the-search-results-in-the-webpage"></a>웹 페이지에 검색 결과 표시

`buildDiv()` 함수는 `addDivContent` 함수를 호출하여 각 태그의 접을 수 있는 `<div>` 콘텐츠를 빌드합니다.

태그의 콘텐츠에는 태그에 대한 응답에서 가져온 JSON이 포함됩니다. 처음에는 JSON의 처음 100개 문자만 표시되지만, JSON 문자열을 클릭하여 모든 JSON을 표시할 수 있습니다. 다시 클릭하면 JSON 문자열이 다시 100자로 축소됩니다.

다음으로, 태그에 있는 작업 형식을 추가합니다. 각 작업 유형에 대해 적절한 함수를 호출하여 해당 인사이트를 추가합니다.

```javascript
function addDivContent(div, tag, json) {

    // Adds the first 100 characters of the json that contains
    // the tag's data. The user can click the text to show the
    // full json. They can click it again to collapse the json.
    var para = document.createElement('p');
    para.textContent = String(json).substr(0, 100) + '...';
    para.setAttribute('title', 'click to expand');
    para.setAttribute('style', 'cursor: pointer;')
    para.setAttribute('data-json', json);
    para.addEventListener('click', function(e) {
        var json = e.target.getAttribute('data-json');

        if (e.target.textContent.length <= 103) {  // 100 + '...'
            e.target.textContent = json;
            para.setAttribute('title', 'click to collapse');
        }
        else {
            para.textContent = String(json).substr(0, 100) + '...';
            para.setAttribute('title', 'click to expand');
        }
    });
    div.appendChild(para); 

    var parsedJson = JSON.parse(json);

    // Loop through all the actions in the tag and display them.
    for (var j = 0; j < parsedJson.actions.length; j++) {
        var action = parsedJson.actions[j];

        var subSectionDiv = document.createElement('div');
        subSectionDiv.setAttribute('class', 'subSection');
        div.appendChild(subSectionDiv);

        var h4 = document.createElement('h4');
        h4.innerHTML = action.actionType;
        subSectionDiv.appendChild(h4);

        if (action.actionType === 'ImageResults') {
            addImageWithWebSearchUrl(subSectionDiv, parsedJson.image, action);
        }
        else if (action.actionType === 'DocumentLevelSuggestions') {
            addRelatedSearches(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'RelatedSearches') {
            addRelatedSearches(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'PagesIncluding') {
            addPagesIncluding(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'VisualSearch') {
            addRelatedImages(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'Recipes') {
            addRecipes(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'ShoppingSources') {
            addShopping(subSectionDiv, action.data.offers);
        }
        else if (action.actionType === 'ProductVisualSearch') {
            addProducts(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'TextResults') {
            addTextResult(subSectionDiv, action);
        }
        else if (action.actionType === 'Entity') {
            addEntity(subSectionDiv, action);
        }
    }
}
```

## <a name="display-insights-for-different-actions"></a>다양한 작업에 대한 인사이트 표시

다음 함수는 다양한 작업에 대한 인사이트를 표시합니다. 함수는 클릭 가능한 이미지 또는 이미지에 대한 자세한 정보가 포함된 웹 페이지로 이동되는 클릭 가능한 링크를 제공합니다. 이 페이지는 Bing.com 또는 이미지의 원래 웹 사이트에서 호스트됩니다. 인사이트의 데이터 중 일부만 이 애플리케이션에 표시됩니다. 인사이트에 사용할 수 있는 모든 필드를 보려면 [이미지 - Visual Search](/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch) 참고 자료를 참조하세요.

> [!NOTE]
> 페이지에 표시해야 하는 최소한의 인사이트 정보 양이 있습니다. 자세한 내용은 [Bing Search API 사용 및 표시 요구 사항](../bing-web-search/use-display-requirements.md)을 참조하세요.

### <a name="relatedimages-insights"></a>RelatedImages 인사이트

`addRelatedImages()` 함수는 `RelatedImages` 작업의 목록을 반복하고, 각각에 대해 `<img>` 태그를 `<div>` 외부에 추가하여 관련 이미지를 호스팅하는 각 웹 사이트에 대한 제목을 만듭니다.

```javascript
    function addRelatedImages(div, images) {
        var length = (images.length > 10) ? 10 : images.length;

        // Set the title to the website that hosts the image. The title displays
        // when the user hovers over the image.

        // Make the image clickable. If the user clicks the image, they're taken
        // to the image in Bing.com.

        for (var j = 0; j < length; j++) {
            var img = document.createElement('img');
            img.setAttribute('src', images[j].thumbnailUrl + '&w=120&h=120');
            img.setAttribute('style', 'margin: 20px 20px 0 0; cursor: pointer;');
            img.setAttribute('title', images[j].hostPageDisplayUrl);
            img.setAttribute('data-webSearchUrl', images[j].webSearchUrl)

            img.addEventListener('click', function(e) {
                var url = e.target.getAttribute('data-webSearchUrl');
                window.open(url, 'foo');
            })

            div.appendChild(img);
        }
    }
```

### <a name="pagesincluding-insights"></a>PagesIncluding 인사이트

`addPagesIncluding()` 함수는 `PagesIncluding` 작업의 목록을 반복하고, 각각에 대해 `<img>` 태그를 `<div>` 외부에 추가하여 업로드된 이미지를 호스팅하는 각 웹 사이트에 대한 링크를 만듭니다.

```javascript

    // Display links to the first 5 webpages that include the image.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addPagesIncluding(div, pages) {
        var length = (pages.length > 5) ? 5 : pages.length;

        for (var j = 0; j < length; j++) {
            var page = document.createElement('a');
            page.text = pages[j].name;
            page.setAttribute('href', pages[j].hostPageUrl);
            page.setAttribute('style', 'margin: 20px 20px 0 0');
            page.setAttribute('target', '_blank')
            div.appendChild(page);

            div.appendChild(document.createElement('br'));
        }
    }
```

### <a name="relatedsearches-insights"></a>RelatedSearches 인사이트

`addRelatedSearches()` 함수는 `RelatedSearches` 작업의 목록을 반복하고, 각각에 대해 `<img>` 태그를 `<div>` 외부에 추가하여 이미지를 호스팅하는 웹 사이트에 대한 링크를 만듭니다.

```javascript

    // Display the first 10 related searches. Include a link with the image
    // that when clicked, takes the user to Bing.com and displays the 
    // related search results.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addRelatedSearches(div, relatedSearches) {
        var length = (relatedSearches.length > 10) ? 10 : relatedSearches.length;

        for (var j = 0; j < length; j++) {
            var childDiv = document.createElement('div');
            childDiv.setAttribute('class', 'stackLink');
            div.appendChild(childDiv);

            var img = document.createElement('img');
            img.setAttribute('src', relatedSearches[j].thumbnail.url + '&w=120&h=120');
            img.setAttribute('style', 'margin: 20px 20px 0 0;');
            childDiv.appendChild(img);

            var relatedSearch = document.createElement('a');
            relatedSearch.text = relatedSearches[j].displayText;
            relatedSearch.setAttribute('href', relatedSearches[j].webSearchUrl);
            relatedSearch.setAttribute('target', '_blank');
            childDiv.appendChild(relatedSearch);

        }
    }
```

### <a name="recipes-insights"></a>Recipes 인사이트

`addRecipes()` 함수는 `Recipes` 작업의 목록을 반복하고, 각각에 대해 `<img>` 태그를 `<div>` 외부에 추가하여 반환되는 각 레시피에 대한 링크를 만듭니다.

```javascript
    // Display links to the first 10 recipes. Include the recipe's rating,
    // if available.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addRecipes(div, recipes) {
        var length = (recipes.length > 10) ? 10 : recipes.length;

        for (var j = 0; j < length; j++) {
            var para = document.createElement('p');

            var recipe = document.createElement('a');
            recipe.text = recipes[j].name;
            recipe.setAttribute('href', recipes[j].url);
            recipe.setAttribute('style', 'margin: 20px 20px 0 0');
            recipe.setAttribute('target', '_blank')
            para.appendChild(recipe);

            if (recipes[j].hasOwnProperty('aggregateRating')) {
                var span = document.createElement('span');
                span.textContent = 'rating: ' + recipes[j].aggregateRating.text;
                para.appendChild(span);
            }

            div.appendChild(para);
        }
    }
```

### <a name="shopping-insights"></a>Shopping 인사이트

`addShopping()` 함수는 `RelatedImages` 작업의 목록을 반복하고, 각각에 대해 `<img>` 태그를 `<div>` 외부에 추가하여 반환되는 모든 쇼핑 결과에 대한 링크를 만듭니다.

```javascript
    // Display links for the first 10 shopping offers.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addShopping(div, offers) {
        var length = (offers.length > 10) ? 10 : offers.length;

        for (var j = 0; j < length; j++) {
            var para = document.createElement('p');

            var offer = document.createElement('a');
            offer.text = offers[j].name;
            offer.setAttribute('href', offers[j].url);
            offer.setAttribute('style', 'margin: 20px 20px 0 0');
            offer.setAttribute('target', '_blank')
            para.appendChild(offer);

            var span = document.createElement('span');
            span.textContent = 'by ' + offers[j].seller.name + ' | ' + offers[j].price + ' ' + offers[j].priceCurrency;
            para.appendChild(span);

            div.appendChild(para);
        }
    }
```

### <a name="products-insights"></a>Products 인사이트

`addProducts()` 함수는 `Products` 작업의 목록을 반복하고, 각각에 대해 `<img>` 태그를 `<div>` 외부에 추가하여 반환되는 모든 제품 결과에 대한 링크를 만듭니다.

```javascript

    // Display the first 10 related products. Display a clickable image of the
    // product that takes the user to Bing.com search results for the product.
    // If there are any offers associated with the product, provide links to the offers.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addProducts(div, products) {
        var length = (products.length > 10) ? 10 : products.length;

        for (var j = 0; j < length; j++) {
            var childDiv = document.createElement('div');
            childDiv.setAttribute('class', 'stackLink');
            div.appendChild(childDiv);

            var img = document.createElement('img');
            img.setAttribute('src', products[j].thumbnailUrl + '&w=120&h=120');
            img.setAttribute('title', products[j].name);
            img.setAttribute('style', 'margin: 20px 20px 0 0; cursor: pointer;');
            img.setAttribute('data-webSearchUrl', products[j].webSearchUrl)
            img.addEventListener('click', function(e) {
                var url = e.target.getAttribute('data-webSearchUrl');
                window.open(url, 'foo');
            })
            childDiv.appendChild(img);

            if (products[j].insightsMetadata.hasOwnProperty('aggregateOffer')) {
                if (products[j].insightsMetadata.aggregateOffer.offerCount > 0) {
                    var offers = products[j].insightsMetadata.aggregateOffer.offers;

                    // Show all the offers. Not all markets provide links to offers.
                    for (var i = 0; i < offers.length; i++) {  
                        var para = document.createElement('p');

                        var offer = document.createElement('a');
                        offer.text = offers[i].name;
                        offer.setAttribute('href', offers[i].url);
                        offer.setAttribute('style', 'margin: 20px 20px 0 0');
                        offer.setAttribute('target', '_blank')
                        para.appendChild(offer);

                        var span = document.createElement('span');
                        span.textContent = 'by ' + offers[i].seller.name + ' | ' + offers[i].price + ' ' + offers[i].priceCurrency;
                        para.appendChild(span);

                        childDiv.appendChild(para);
                    }
                }
                else {  // Otherwise, just show the lowest price that Bing found.
                    var offer = products[j].insightsMetadata.aggregateOffer;

                    var para = document.createElement('p');
                    para.textContent = `${offer.name} | ${offer.lowPrice} ${offer.priceCurrency}`; 

                    childDiv.appendChild(para);
                }
            }
        }
    }
```

### <a name="textresult-insights"></a>TextResult 인사이트

`addTextResult()` 함수는 이미지에서 인식된 모든 텍스트를 표시합니다.

```javascript

    function addTextResult(div, action) {
        var text = document.createElement('p');
        text.textContent = action.displayName;
        div.appendChild(text);
    }
```

`addEntity()` 함수는 검색된 경우 사용자를 Bing.com으로 안내하는 링크를 표시합니다. 여기서는 이미지의 엔터티 형식에 대한 세부 정보를 얻을 수 있습니다.

```javascript
    // If the image is of a person, the tag might include an entity
    // action type. Display a link that takes the user to Bing.com
    // where they can get details about the entity.
    function addEntity(div, action) {
        var entity = document.createElement('a');
        entity.text = action.displayName;
        entity.setAttribute('href', action.webSearchUrl);
        entity.setAttribute('style', 'margin: 20px 20px 0 0');
        entity.setAttribute('target', '_blank');
        div.appendChild(entity);
    }
```

`addImageWithWebSearchUrl()` 함수는 사용자를 Bing.com의 검색 결과로 안내하는 클릭 가능한 이미지를 `<div>`에 표시합니다.

```javascript
    function addImageWithWebSearchUrl(div, image, action) {
        var img = document.createElement('img');
        img.setAttribute('src', image.thumbnailUrl + '&w=120&h=120');
        img.setAttribute('style', 'margin: 20px 20px 0 0; cursor: pointer;');
        img.setAttribute('data-webSearchUrl', action.webSearchUrl);
        img.addEventListener('click', function(e) {
            var url = e.target.getAttribute('data-webSearchUrl');
            window.open(url, 'foo');
        })
        div.appendChild(img);
    }

```

## <a name="add-a-css-style"></a>CSS 스타일 추가

다음 `<style>` 섹션을 `<head>` 태그에 추가하여 웹 페이지의 레이아웃을 구성합니다.

```html
        <style>

            .thumb {
                height: 75px;
                border: 1px solid #000;
            }

            .stackLink {
                width:180px;
                min-height:210px;
                display:inline-block;
            }
            .stackLink a {
                float:left;
                clear:left;
            }

            .section {
                float:left;
                display:none;
            }

            .subSection {
                clear:left;
                float:left;
            }

        </style>
```

## <a name="next-steps"></a>다음 단계

>[!div class="nextstepaction"]
> [자습서: ImageInsightsToken을 사용하여 이전 검색에서 유사한 이미지 찾기](./tutorial-visual-search-insights-token.md)