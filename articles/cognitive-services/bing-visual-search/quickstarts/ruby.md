---
title: '빠른 시작: REST API 및 Ruby를 사용하여 이미지 인사이트 가져오기 - Bing Visual Search'
titleSuffix: Azure Cognitive Services
description: Bing Visual Search API 및 Ruby를 사용하여 이미지를 업로드한 다음, 이미지에 대한 인사이트를 가져오는 방법을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 05/22/2020
ms.author: aahi
ms.custom: mode-other
ms.openlocfilehash: 8c4b04221e4819a192d0248fb5f23023de4208f8
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133046655"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-ruby"></a>빠른 시작: Bing Visual Search REST API 및 Ruby를 사용하여 이미지 인사이트 가져오기

> [!WARNING]
> Bing Search API는 Cognitive Services에서 Bing Search Services로 이동합니다. **2020년 10월 30일** 부터 Bing Search의 모든 새 인스턴스는 [여기](/bing/search-apis/bing-web-search/create-bing-search-service-resource)에 설명된 프로세스에 따라 프로비저닝되어야 합니다.
> Cognitive Services를 사용하여 프로비저닝된 Bing Search API는 향후 3년 동안 또는 기업계약이 종료될 때까지(둘 중 먼저 도래할 때까지) 지원됩니다.
> 마이그레이션 지침은 [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource)를 참조하세요.

이 빠른 시작을 통해 Ruby 프로그래밍 언어를 사용하여 Bing Visual Search API를 처음 호출합니다. POST 요청은 API 엔드포인트에 이미지를 업로드합니다. 결과는 업로드된 이미지와 비슷한 이미지의 URL과 설명 정보를 포함합니다.

## <a name="prerequisites"></a>사전 요구 사항

* [Ruby 2.4 이상](https://www.ruby-lang.org/en/downloads/)을 설치합니다.
* 구독 키를 가져오세요.

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="project-and-required-modules"></a>프로젝트 및 필수 모듈

IDE 또는 편집기에서 새 Ruby 프로젝트를 만듭니다. `net/http`, `uri` 및 `json`을 가져와 결과의 JSON 텍스트를 처리합니다. 파일 이름 문자열을 인코딩하는 `base64` 라이브러리를 가져옵니다. 

```ruby
require 'net/https'
require 'uri'
require 'json'
require 'base64'
```

## <a name="define-variables"></a>변수 정의

다음 코드는 main 함수를 선언하고 필요한 변수를 할당합니다. 

1. 엔드포인트가 올바른지 확인하고 `accessKey` 값을 Azure 계정의 유효한 구독 키로 바꿉니다. 
2. `batchNumber`의 경우 POST 데이터의 선행 및 후행 경계에 필요한 GUID를 할당합니다. 
3. `fileName`의 경우 POST에 사용할 이미지 파일을 할당합니다. 
4. `if` 블록을 사용하여 유효한 구독 키를 테스트합니다.

```ruby
accessKey = "ACCESS-KEY"
uri  = "https://api.cognitive.microsoft.com"
path = "/bing/v7.0/images/visualsearch"
batchNumber = "dc05c75a-6b5e-4059-b556-0b7c079819a5"
fileName = "red-dress.jpg"

if accessKey.length != 32 then
    puts "Invalid Bing Search API subscription key!"
    puts "Please paste yours into the source code."
    abort
end

```

## <a name="form-data-for-post-request"></a>POST 요청에 대한 양식 데이터

1. 선행 및 후행 경계를 통해 이미지 데이터를 POST로 묶습니다. 다음 함수로 해당 경계를 설정합니다.

   ```ruby
   def BuildFormDataStart(batNum, fileName)
       startBoundary = "--batch_" + batNum
       return startBoundary + "\r\n" + "Content-Disposition: form-data; name=\"image\"; filename=" + "\"" + fileName + "\"" + "\r\n\r\n"    
   end

   def BuildFormDataEnd(batNum)
       return "\r\n\r\n" + "--batch_" + batNum + "--" + "\r\n"
   end
   ```

2. 엔드포인트 URI 및 POST 본문을 포함할 배열을 생성합니다. 이전 함수를 사용하여 시작 경계를 배열로 로드합니다. 이미지 파일을 배열로 읽은 다음, 끝 경계를 배열로 읽습니다.

   ```ruby
   uri = URI(uri + path)
   print uri
   print "\r\n\r\n"

   post_body = []

   post_body << BuildFormDataStart(batchNumber, fileName)

   post_body << File.read(fileName) #Base64.encode64(File.read(fileName))

   post_body << BuildFormDataEnd(batchNumber)
   ```

## <a name="create-the-http-request"></a>HTTP 요청 만들기

`Ocp-Apim-Subscription-Key` 헤더를 설정합니다. 요청을 만든 다음, 헤더 및 콘텐츠 형식을 할당합니다. 이전에 만든 POST 본문을 요청에 조인합니다.

```ruby
header = {'Ocp-Apim-Subscription-Key': accessKey}
request = Net::HTTP::Post.new(uri)  # , 'ImageKnowledge' => 'ImageKnowledge'

request['Ocp-Apim-Subscription-Key'] = accessKey
request.content_type = "multipart/form-data; boundary=batch_" + batchNumber  
request.body = post_body.join

```

## <a name="request-and-response"></a>요청 및 응답

Ruby는 요청을 보내고 다음 코드를 포함하는 응답을 가져옵니다.

```ruby
response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
   http.request(request)
end

```

## <a name="print-the-results"></a>결과 인쇄

응답의 헤더를 인쇄하고 JSON 라이브러리를 사용하여 출력의 서식을 지정합니다.

```ruby
puts "\nRelevant Headers:\n\n"
response.each_header do |key, value|
    if key.start_with?("bingapis-") or key.start_with?("x-msedge-") then
        puts key + ": " + value
    end
end

puts "\nJSON Response:\n\n"
puts JSON::pretty_generate(JSON(response.body))

```

## <a name="json-response"></a>JSON 응답

다음 JSON은 출력의 세그먼트입니다.

```JSON
Relevant Headers:

bingapis-traceid: 6E19E78D4FEC4A61AB4F85977EEDB8E6
x-msedge-clientid: 3928F9869390668A304CF49792DC6746
x-msedge-ref: Ref A: 6E19E78D4FEC4A61AB4F85977EEDB8E6 Ref B: BY3EDGE0310 Ref C: 2019-02-28T19:25:12Z

JSON Response:

{
  "_type": "ImageKnowledge",
  "instrumentation": {
    "_type": "ResponseInstrumentation",
    "pingUrlBase": "https://www.bingapis.com/api/ping?IG=3F6A656574B24F81A553485B4B3244EF&CID=3928F9869390668A304CF49792DC6746&ID=",
    "pageLoadPingUrl": "https://www.bingapis.com/api/ping/pageload?IG=3F6A656574B24F81A553485B4B3244EF&CID=3928F9869390668A304CF49792DC6746&Type=Event.CPT&DATA=0"
  },
  "tags": [
    {
      "displayName": "",
      "actions": [
        {
          "_type": "ImageModuleAction",
          "actionType": "PagesIncluding",
          "data": {
            "value": [
              {
                "webSearchUrl": "https://www.bing.com/images/search?view=detailv2&FORM=OIIRPO&id=6988549F8C0C0688972CCC9F6A5E5F70F17D6F1E&simid=608025573664556965",
                "webSearchUrlPingSuffix": "DevEx,5044.1",
                "name": "Peanut Butter & Banana Training Treats. | Loved By A Collie",
                "thumbnailUrl": "https://tse2.mm.bing.net/th?id=OIP.FyoKOORtLFGzzanAVfPt4QAAAA&pid=Api",
                "datePublished": "2014-02-04T12:00:00.0000000Z",
                "isFamilyFriendly": true,
                "contentUrl": "https://lovedbyacollie.lifeseven.com/wp-content/uploads/2014/02/PBBcookies.png",
                "contentUrlPingSuffix": "DevEx,5046.1",
                "hostPageUrl": "https://lovedbyacollie.lifeseven.com/2014/02/04/peanut-butter-banana-training-treats/",
                "hostPageUrlPingSuffix": "DevEx,5045.1",
                "contentSize": "197552 B",
                "encodingFormat": "png",
                "hostPageDisplayUrl": "lovedbyacollie.lifeseven.com/2014/02/04/peanut-butter-banana...",
                "width": 300,
                "height": 409,
                "hostPageFavIconUrl": "https://www.bing.com/th?id=AR_2ceefde49d9f981b6cab9e9bd0e6693b&pid=Api",
                "thumbnail": {
                  "width": 300,
                  "height": 409
                },
                "imageInsightsToken": "ccid_FyoKOORt*mid_6988549F8C0C0688972CCC9F6A5E5F70F17D6F1E*simid_608025573664556965*thid_OIP.FyoKOORtLFGzzanAVfPt4QAAAA",
                "insightsMetadata": {
                  "pagesIncludingCount": 2,
                  "availableSizesCount": 2
                },
                "imageId": "6988549F8C0C0688972CCC9F6A5E5F70F17D6F1E",
                "accentColor": "8A6E41"
              },
              {
                "webSearchUrl": "https://www.bing.com/images/search?view=detailv2&FORM=OIIRPO&id=B5B5C37666FB3DB326209F358A1AC9E7B0F484EB&simid=607993125239130030",
                "webSearchUrlPingSuffix": "DevEx,5050.1",
                "name": "RPG Maker Icons favourites by Leon-Murayami on DeviantArt",
                "thumbnailUrl": "https://tse4.mm.bing.net/th?id=OIP.vzeNdrscXihmLeTdDe6G6AHaKL&pid=Api",
                "datePublished": "2019-01-22T06:59:00.0000000Z",
                "isFamilyFriendly": true,
                "contentUrl": "https://orig00.deviantart.net/753e/f/2018/147/5/0/signs_metal_rusted_by_sarahyt-dccnku0.png",
                "contentUrlPingSuffix": "DevEx,5052.1",
                "hostPageUrl": "https://www.deviantart.com/leon-murayami/favourites/71077691/RPG-Maker-Icons",
                "hostPageUrlPingSuffix": "DevEx,5051.1",
                "contentSize": "569772 B",
                "encodingFormat": "png",
                "hostPageDisplayUrl": "https://www.deviantart.com/leon-murayami/favourites/71077691/RPG...",
                "width": 768,
                "height": 1056,
                "hostPageFavIconUrl": "https://www.bing.com/th?id=AR_b246060a4abab0d7111f5aa733205f06&pid=Api",
                "thumbnail": {
                  "width": 474,
                  "height": 651
                },
                "imageInsightsToken": "ccid_vzeNdrsc*mid_B5B5C37666FB3DB326209F358A1AC9E7B0F484EB*simid_607993125239130030*thid_OIP.vzeNdrscXihmLeTdDe6G6AHaKL",
                "insightsMetadata": {
                  "pagesIncludingCount": 3,
                  "availableSizesCount": 3
                },
                "imageId": "B5B5C37666FB3DB326209F358A1AC9E7B0F484EB",
                "accentColor": "936C38"
              }
            ]
          }
        },
        {
          "image": {
            "webSearchUrl": "https://www.bing.com/images/search?view=detailv2&FORM=OIIRPO",
            "webSearchUrlPingSuffix": "DevEx,5057.1",
            "name": "",
            "isFamilyFriendly": true,
            "contentSize": "572 B",
            "encodingFormat": "jpeg",
            "hostPageDisplayUrl": "",
            "width": 583,
            "height": 583,
            "thumbnail": {
              "width": 0,
              "height": 0
            },
            "visualWords": "0bfcc afa3e ac572 0aa3e b5daf 12d1a aabab 620ad 0c081 0a5a6 0c1d6 5ebfe ac579 ab7a3 0bff6 ab1eb b1fa0 61970 ac57d 0bfcf 5a8f00bf60e71d1c15a4c1586622845a8f22db5d2db98f058c6d7a112f5c17363ab964ac3f4f058b622795a8335a8f30bfcb"
          },
          "actionType": "MoreSizes"
        },
        {
          "_type": "ImageModuleAction",
          "actionType": "VisualSearch",
          "data": {
            "value": [
              {
                "webSearchUrl": "https://www.bing.com/images/search?view=detailv2&FORM=OIIRPO&id=21423B0D286BB2FCFB3B7090D59EA6F8A54AB7CB&simid=608005928522679185",
                "webSearchUrlPingSuffix": "DevEx,5064.1",
                "name": "Bemz review of IKEA Söderhamn sofa - Bemz",
                "thumbnailUrl": "https://tse4.mm.bing.net/th?id=OIP.v0zvOeP8dnNFPWQNSu80hwAAAA&pid=Api",
                "datePublished": "2017-09-08T02:27:00.0000000Z",
                "isFamilyFriendly": true,
                "contentUrl": "https://bemz.scene7.com/is/image/Bemz?obj=masks&src=ir%7bBemzRender%2fsq50v2%3f%26src%3dDG2130%26rs%3dQ5%26sharpen%3d1%26res%3d20%26wid%3d2400%26hei%3d2400%7d&resmode=sharp2&op_usm=1,1,8&wid=280&hei=280&scl=5",
                "contentUrlPingSuffix": "DevEx,5066.1",
                "hostPageUrl": "https://bemz.com/inspiration/popular-products/i-love-my-soderhamn/",
                "hostPageUrlPingSuffix": "DevEx,5065.1",
                "contentSize": "3000 B",
                "encodingFormat": "jpeg",
                "hostPageDisplayUrl": "bemz.com/inspiration/popular-products/i-love-my-soderhamn",
                "width": 280,
                "height": 280,
                "thumbnail": {
                  "width": 280,
                  "height": 280
                },
                "imageInsightsToken": "ccid_v0zvOeP8*mid_21423B0D286BB2FCFB3B7090D59EA6F8A54AB7CB*simid_608005928522679185*thid_OIP.v0zvOeP8dnNFPWQNSu80hwAAAA",
                "insightsMetadata": {
                  "pagesIncludingCount": 1,
                  "availableSizesCount": 1
                },
                "imageId": "21423B0D286BB2FCFB3B7090D59EA6F8A54AB7CB",
                "accentColor": "963555"
              },

              . . .

```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Bing Visual Search API란?](../overview.md)
> [Visual Search 단일 페이지 웹앱 빌드](../tutorial-bing-visual-search-single-page-app.md)
