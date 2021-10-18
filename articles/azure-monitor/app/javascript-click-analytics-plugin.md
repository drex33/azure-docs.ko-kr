---
title: Application Insights JavaScript SDK용 클릭 분석 자동 수집 플러그 인
description: Application Insights JavaScript SDK용 클릭 분석 자동 수집 플러그 인을 설치하고 사용하는 방법을 알아봅니다.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/14/2021
author: mattmccleary
ms.author: mmcc
ms.openlocfilehash: e1a0ab71ce8d5970b1239f70a058ee7a663117da
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130132705"
---
# <a name="click-analytics-auto-collection-plugin-for-application-insights-javascript-sdk"></a>Application Insights JavaScript SDK용 클릭 분석 자동 수집 플러그 인

이 플러그 인은 자동으로 웹 페이지의 클릭 이벤트를 추적하고 HTML 요소의 data-* 특성을 사용하여 이벤트 원격 분석을 채웁니다.

## <a name="getting-started"></a>시작

사용자는 npm을 통해 클릭 분석 자동 수집 플러그 인을 설정할 수 있습니다.

### <a name="npm-setup"></a>NPM 설정

npm 패키지를 설치합니다.

```bash
npm install --save @microsoft/applicationinsights-clickanalytics-js @microsoft/applicationinsights-web
```

```js

import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ClickAnalyticsPlugin } from '@microsoft/applicationinsights-clickanalytics-js';

const clickPluginInstance = new ClickAnalyticsPlugin();
// Click Analytics configuration
const clickPluginConfig = {
  autoCapture: true
};
// Application Insights Configuration
const configObj = {
  instrumentationKey: "YOUR INSTRUMENTATION KEY",
  extensions: [clickPluginInstance],
  extensionConfig: {
    [clickPluginInstance.identifier]: clickPluginConfig
  },
};

const appInsights = new ApplicationInsights({ config: configObj });
appInsights.loadAppInsights();
```

## <a name="snippet-setup-ignore-if-using-npm-setup"></a>조각 설치(NPM 설치를 사용하는 경우 무시)

```html
<script type="text/javascript" src="https://js.monitor.azure.com/scripts/b/ext/ai.clck.2.6.2.min.js"></script>
<script type="text/javascript">
  var clickPluginInstance = new Microsoft.ApplicationInsights.ClickAnalyticsPlugin();
  // Click Analytics configuration
  var clickPluginConfig = {
    autoCapture : true,
    dataTags: {
      useDefaultContentNameOrId: true
    }
  }
  // Application Insights Configuration
  var configObj = {
    instrumentationKey: "YOUR INSTRUMENTATION KEY",
    extensions: [
      clickPluginInstance
    ],
    extensionConfig: {
      [clickPluginInstance.identifier] : clickPluginConfig
    },
  };
  // Application Insights Snippet code
  !function(T,l,y){<!-- Removed the Snippet code for brevity -->}(window,document,{
    src: "https://js.monitor.azure.com/scripts/b/ai.2.min.js",
    crossOrigin: "anonymous",
    cfg: configObj
  });
</script>
```

## <a name="how-to-effectively-use-the-plugin"></a>플러그 인을 효과적으로 사용하는 방법

1. 클릭 이벤트에서 생성된 원격 분석 데이터는 Azure Portal의 Application Insights 섹션에 `customEvents`로 저장됩니다.
2. 다음 규칙에 따라 customEvent `name`이 채워집니다.
    1.  `data-*-id`에 제공된 `id`는 customEvent 이름으로 사용됩니다. 예를 들어 클릭한 HTML 요소에 “data-sample-id”=“button1” 특성이 있으면 “button1”이 customEvent 이름이 됩니다.
    2. 해당 특성이 없는 경우 및 구성에서 `useDefaultContentNameOrId`가 `true`로 설정된 경우 클릭한 요소의 HTML 특성 `id` 또는 해당 요소의 콘텐츠 이름이 customEvent 이름으로 사용됩니다. `id` 및 콘텐츠 이름이 둘 다 있는 경우 `id`가 우선됩니다.
    3. `useDefaultContentNameOrId`가 false면 customEvent 이름이 “not_specified”가 됩니다.

    > [!TIP]
    > 의미 있는 데이터를 생성하기 위해서는 `useDefaultContentNameOrId`를 true로 설정하는 것이 좋습니다.  
3. `parentDataTag`는 다음 두 가지 작업을 수행합니다.
    1. 이 태그가 있는 경우 플러그 인은 클릭한 요소의 모든 부모 HTML 요소에서 `data-*` 특성 및 값을 페치합니다.
    2. 효율성을 높이기 위해 플러그 인은 이 태그를 플래그로 사용합니다. 이 경우 DOM(문서 개체 모델)을 더 이상 처리하지 않습니다.
    
    > [!CAUTION]
    > `parentDataTag`가 사용되면 SDK는 이 태그를 사용한 HTML 요소뿐만 아니라 전체 애플리케이션에서 부모 태그를 검색하기 시작합니다.
4. 사용자가 제공한 `customDataPrefix`는 항상 `data-`로 시작해야 합니다(예: `data-sample-`). HTML에서 `data-*` 전역 특성은 사용자 지정 데이터 특성이라고 하는 특성 클래스를 구성합니다. 이 특성을 통해 HTML과 스크립트의 DOM 표시 간에 독점 정보를 교환할 수 있습니다. 이전 브라우저(Internet Explorer, Safari)는 `data-`로 시작하지 않는 한 인식되지 않는 특성을 삭제합니다.

    `data-*`의 `*`는 [XML 이름 생성 규칙](https://www.w3.org/TR/REC-xml/#NT-Name)에 따라 임의의 이름으로 바꿀 수 있으며 다음과 같은 제한 사항이 있습니다.
    - 이름은 대문자인지 소문자인지에 관계없이 “xml”로 시작하지 않아야 합니다.
    - 이름에 세미콜론(U+003A)이 없어야 합니다.
    - 이름에 대문자가 없어야 합니다.

## <a name="what-data-does-the-plugin-collect"></a>플러그 인에서 수집하는 데이터

플러그 인을 사용하는 경우 기본적으로 캡처되는 주요 속성 중 몇 가지는 다음과 같습니다.

### <a name="custom-event-properties"></a>사용자 지정 이벤트 속성
| 이름                  | 설명                            | 예제          |
| --------------------- | ---------------------------------------|-----------------|
| name                  | customEvent의 `name`입니다. 이 속성을 채우는 방법에 대한 자세한 내용은 [여기](#how-to-effectively-use-the-plugin)에 있습니다.| 정보              |
| itemType              | 이벤트 유형입니다.                                      | customEvent      |
|sdkVersion             | 클릭 플러그 인과 함께 사용하는 Application Insights SDK 버전입니다.|javascript:2.6.2_ClickPlugin2.6.2|

### <a name="custom-dimensions"></a>사용자 지정 차원
| 이름                  | 설명                            | 예제          |
| --------------------- | ---------------------------------------|-----------------|
| actionType            | 클릭 이벤트를 발생시킨 동작 유형입니다. 마우스 왼쪽 단추를 클릭할 수도 있고 오른쪽 단추를 클릭할 수도 있습니다. | CL              |
| baseTypeSource        | 사용자 지정 이벤트의 기본 유형 원본입니다.                                      | ClickEvent      |
| clickCoordinates      | 클릭 이벤트가 트리거되는 위치를 조정합니다.                            | 659X47          |
| 콘텐츠               | 추가 `data-*` 특성 및 값을 저장하는 자리 표시자입니다.            | [{sample1:value1, sample2:value2}] |
| pageName              | 클릭 이벤트가 트리거되는 페이지의 제목입니다.                      | 샘플 제목    |
| parentId              | 부모 요소의 ID 또는 이름입니다.                                           | navbarContainer |

### <a name="custom-measurements"></a>사용자 지정 측정입니다.
| 이름                  | 설명                            | 예제          |
| --------------------- | ---------------------------------------|-----------------|
| timeToAction          | 초기 페이지 로드 이후 사용자가 요소를 클릭하는 데 걸린 시간(밀리초)입니다. | 87407              |

## <a name="configuration"></a>구성

| 이름                  | Type                               | 기본값 | Description                                                                                                                              |
| --------------------- | -----------------------------------| --------| ---------------------------------------------------------------------------------------------------------------------------------------- |
| autoCapture           | boolean                            | true    | 자동 캡처 구성입니다.                                |
| 콜백(callback)              | [IValueCallback](#ivaluecallback)  | null    | 콜백 구성입니다.                               |
| pageTags              | 문자열                             | null    | 페이지 태그입니다.                                             |
| dataTags              | [ICustomDataTags](#icustomdatatags)| null    | 클릭 데이터를 캡처하는 데 사용되는 기본 태그를 재정의하도록 제공된 사용자 지정 데이터 태그입니다. |
| urlCollectHash        | boolean                            | false   | URL의 “#” 문자 뒤에 있는 값 로깅을 사용하도록 설정합니다.                |
| urlCollectQuery       | boolean                            | false   | URL의 쿼리 문자열 로깅을 사용하도록 설정합니다.                            |
| behaviorValidator     | 함수                           | null  | `data-*-bhvr` 값 유효성 검사에 사용할 콜백 함수입니다. 자세한 내용을 보려면 [behaviorValidator 섹션](#behaviorvalidator)으로 이동하세요.|
| defaultRightClickBhvr | 문자열 또는 숫자                 | ''      | 마우스 오른쪽 단추 클릭 이벤트가 발생하는 경우의 기본 동작 값입니다. 요소에 `data-*-bhvr` 특성이 있으면 이 값이 재정의됩니다. |
| dropInvalidEvents     | boolean                            | false   | 유용한 클릭 데이터가 없는 이벤트를 삭제하기 위한 플래그입니다.                                                                                   |

### <a name="ivaluecallback"></a>IValueCallback

| 이름               | Type     | 기본값 | 설명                                                                             |
| ------------------ | -------- | ------- | --------------------------------------------------------------------------------------- |
| pageName           | 함수 | null    | 기본 pageName 캡처 동작을 재정의하는 함수입니다.                           |
| pageActionPageTags | 함수 | null    | pageAction 이벤트 중에 수집되는 기본 pageTags를 늘리는 콜백 함수입니다.  |
| contentName        | 함수 | null    | 사용자 지정된 contentName을 채우는 콜백 함수입니다.                                 |

### <a name="icustomdatatags"></a>ICustomDataTags

| 이름                      | Type    | 기본값   | HTML에 사용할 기본 태그 |   Description                                                                                |
|---------------------------|---------|-----------|-------------|----------------------------------------------------------------------------------------------|
| useDefaultContentNameOrId | boolean | false     | 해당 없음         |특정 요소가 기본 customDataPrefix로 태그 지정되지 않은 경우 또는 사용자가 customDataPrefix를 제공하지 않은 경우 contentName의 표준 HTML 특성을 수집합니다. |
| customDataPrefix          | 문자열  | `data-`   | `data-*`| 제공된 접두사로 태그 지정된 요소의 콘텐츠 이름과 값을 자동으로 캡처합니다. 예를 들어 `data-*-id`, `data-<yourcustomattribute>`가 HTML 태그에 사용될 수 있습니다.   |
| aiBlobAttributeTag        | 문자열  | `ai-blob` |  `data-ai-blob`| 플러그 인이 개별 `data-*` 특성 대신 JSON blob 특성을 지원합니다. |
| metaDataPrefix            | 문자열  | null      | 해당 없음  | 캡처 시 제공된 접두사를 사용하여 HTML 헤드의 메타 요소 이름 및 콘텐츠를 자동으로 캡처합니다. 예를 들어 `custom-`가 HTML Meta 태그에 사용될 수 있습니다. |
| captureAllMetaDataContent | boolean | false     | 해당 없음   | 모든 HTML 헤드의 메타 요소 이름 및 콘텐츠를 자동으로 캡처합니다. 기본값은 false입니다. 사용하도록 설정하면 제공된 metaDataPrefix를 재정의합니다. |
| parentDataTag             | 문자열  | null      |  해당 없음  | 이 태그가 사용된 경우 요소의 콘텐츠 이름 및 값을 캡처하는 DOM 트래버스를 중지합니다. 예를 들어, `data-<yourparentDataTag>`가 HTML 태그에 사용될 수 있습니다.|
| dntDataTag                | 문자열  | `ai-dnt`  |  `data-ai-dnt`| 이 특성이 사용된 HTML 요소는 원격 분석 데이터를 캡처하는 플러그 인에서 무시됩니다.|

### <a name="behaviorvalidator"></a>behaviorValidator

behaviorValidator 함수는 코드의 태그 지정된 동작이 미리 정의된 목록에 부합하는지 자동으로 확인합니다. 이를 통해 태그 지정된 동작이 기업에서 설정한 분류와 일관성을 가지도록 할 수 있습니다. 대부분의 Azure Monitor 고객에게는 이 함수 사용이 필수가 아니거나 필요하지 않지만 고급 시나리오의 경우 사용할 수 있습니다. 이 확장의 일부로 공개되는 behaviorValidator 콜백 함수는 세 가지가 있습니다. 하지만 공개된 함수로 요구 사항을 해결하지 못하는 경우 사용자가 고유한 콜백 함수를 사용할 수 있습니다. 고유한 동작 데이터 구조를 가져오도록 하기 위해 데이터 태그에서 동작을 추출하는 동안 플러그 인은 이 유효성 검사기 함수를 사용합니다.

| 이름                   | Description                                                                        |
| ---------------------- | -----------------------------------------------------------------------------------|
| BehaviorValueValidator | 동작 데이터 구조가 문자열 배열인 경우 이 콜백 함수를 사용합니다.|
| BehaviorMapValidator   | 동작 데이터 구조가 사전인 경우 이 콜백 함수를 사용합니다.       |
| BehaviorEnumValidator  | 동작 데이터 구조가 열거형인 경우 이 콜백 함수를 사용합니다.            |

#### <a name="sample-usage-with-behaviorvalidator"></a>behaviorValidator 샘플 사용

```js
var clickPlugin = Microsoft.ApplicationInsights.ClickAnalyticsPlugin;
var clickPluginInstance = new clickPlugin();

// Behavior enum values
var behaviorMap = {
  UNDEFINED: 0, // default, Undefined

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Page Experience [1-19]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  NAVIGATIONBACK: 1, // Advancing to the previous index position within a webpage
  NAVIGATION: 2, // Advancing to a specific index position within a webpage
  NAVIGATIONFORWARD: 3, // Advancing to the next index position within a webpage
  APPLY: 4, // Applying filter(s) or making selections
  REMOVE: 5, // Applying filter(s) or removing selections
  SORT: 6, // Sorting content
  EXPAND: 7, // Expanding content or content container
  REDUCE: 8, // Sorting content
  CONTEXTMENU: 9, // Context Menu
  TAB: 10, // Tab control
  COPY: 11, // Copy the contents of a page
  EXPERIMENTATION: 12, // Used to identify a third party experimentation event
  PRINT: 13, // User printed page
  SHOW: 14, //  Displaying an overlay
  HIDE: 15, //  Hiding an overlay
  MAXIMIZE: 16, //  Maximizing an overlay
  MINIMIZE: 17, // Minimizing an overlay
  BACKBUTTON: 18, //  Clicking the back button

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Scenario Process [20-39]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  STARTPROCESS: 20, // Initiate a web process unique to adopter
  PROCESSCHECKPOINT: 21, // Represents a checkpoint in a web process unique to adopter
  COMPLETEPROCESS: 22, // Page Actions that complete a web process unique to adopter
  SCENARIOCANCEL: 23, // Actions resulting from cancelling a process/scenario

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Download [40-59]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  DOWNLOADCOMMIT: 40, // Initiating an unmeasurable off-network download
  DOWNLOAD: 41, // Initiating a download

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Search [60-79]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  SEARCHAUTOCOMPLETE: 60, // Auto-completing a search query during user input
  SEARCH: 61, // Submitting a search query
  SEARCHINITIATE: 62, // Initiating a search query
  TEXTBOXINPUT: 63, // Typing or entering text in the text box

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Commerce [80-99]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  VIEWCART: 82, // Viewing the cart
  ADDWISHLIST: 83, // Adding a physical or digital good or services to a wishlist
  FINDSTORE: 84, // Finding a physical store
  CHECKOUT: 85, // Before you fill in credit card info
  REMOVEFROMCART: 86, // Remove an item from the cart
  PURCHASECOMPLETE: 87, // Used to track the pageView event that happens when the CongratsPage or Thank You page loads after a successful purchase
  VIEWCHECKOUTPAGE: 88, // View the checkout page
  VIEWCARTPAGE: 89, // View the cart page
  VIEWPDP: 90, // View a PDP
  UPDATEITEMQUANTITY: 91, // Update an item's quantity
  INTENTTOBUY: 92, // User has the intent to buy an item
  PUSHTOINSTALL: 93, // User has selected the push to install option

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Authentication [100-119]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  SIGNIN: 100, // User sign-in
  SIGNOUT: 101, // User sign-out

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Social [120-139]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  SOCIALSHARE: 120, // "Sharing" content for a specific social channel
  SOCIALLIKE: 121, // "Liking" content for a specific social channel
  SOCIALREPLY: 122, // "Replying" content for a specific social channel
  CALL: 123, // Click on a "call" link
  EMAIL: 124, // Click on an "email" link
  COMMUNITY: 125, // Click on a "community" link

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Feedback [140-159]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  VOTE: 140, // Rating content or voting for content
  SURVEYCHECKPOINT: 145, // reaching the survey page/form

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Registration, Contact [160-179]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  REGISTRATIONINITIATE: 161, // Initiating a registration process
  REGISTRATIONCOMPLETE: 162, // Completing a registration process
  CANCELSUBSCRIPTION: 163, // Canceling a subscription
  RENEWSUBSCRIPTION: 164, // Renewing a subscription
  CHANGESUBSCRIPTION: 165, // Changing a subscription
  REGISTRATIONCHECKPOINT: 166, // Reaching the registration page/form

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Chat [180-199]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  CHATINITIATE: 180, // Initiating a chat experience
  CHATEND: 181, // Ending a chat experience

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Trial [200-209]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  TRIALSIGNUP: 200, // Signing-up for a trial
  TRIALINITIATE: 201, // Initiating a trial

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Signup [210-219]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  SIGNUP: 210, // Signing-up for a notification or service
  FREESIGNUP: 211, // Signing-up for a free service

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Referals [220-229]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  PARTNERREFERRAL: 220, // Navigating to a partner's web property

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Intents [230-239]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  LEARNLOWFUNNEL: 230, // Engaging in learning behavior on a commerce page (ex. "Learn more click")
  LEARNHIGHFUNNEL: 231, // Engaging in learning behavior on a non-commerce page (ex. "Learn more click")
  SHOPPINGINTENT: 232, // Shopping behavior prior to landing on a commerce page

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Video [240-259]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  VIDEOSTART: 240, // Initiating a video
  VIDEOPAUSE: 241, // Pausing a video
  VIDEOCONTINUE: 242, // Pausing or resuming a video.
  VIDEOCHECKPOINT: 243, // Capturing predetermined video percentage complete.
  VIDEOJUMP: 244, // Jumping to a new video location.
  VIDEOCOMPLETE: 245, // Completing a video (or % proxy)
  VIDEOBUFFERING: 246, // Capturing a video buffer event
  VIDEOERROR: 247, // Capturing a video error
  VIDEOMUTE: 248, // Muting a video
  VIDEOUNMUTE: 249, // Unmuting a video
  VIDEOFULLSCREEN: 250, // Making a video full screen
  VIDEOUNFULLSCREEN: 251, // Making a video return from full screen to original size
  VIDEOREPLAY: 252, // Making a video replay
  VIDEOPLAYERLOAD: 253, // Loading the video player
  VIDEOPLAYERCLICK: 254, //  Click on a button within the interactive player
  VIDEOVOLUMECONTROL: 255, //  Click on video volume control
  VIDEOAUDIOTRACKCONTROL: 256, // Click on audio control within a video
  VIDEOCLOSEDCAPTIONCONTROL: 257, //  Click on the closed caption control
  VIDEOCLOSEDCAPTIONSTYLE: 258, //  Click to change closed caption style
  VIDEORESOLUTIONCONTROL: 259, //  Click to change resolution

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  //    Advertisement Engagement [280-299]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  ADBUFFERING: 283, // Ad is buffering
  ADERROR: 284, // Ad error
  ADSTART: 285, // Ad start
  ADCOMPLETE: 286, // Ad complete
  ADSKIP: 287, // Ad skipped
  ADTIMEOUT: 288, // Ad timed-out
  OTHER: 300 // Other
};

// Application Insights Configuration
var configObj = {
  instrumentationKey: "YOUR INSTRUMENTATION KEY",
  extensions: [clickPluginInstance],
  extensionConfig: {
    [clickPluginInstance.identifier]: {
      behaviorValidator: Microsoft.ApplicationInsights.BehaviorMapValidator(behaviorMap),
      defaultRightClickBhvr: 9
    },
  },
};
var appInsights = new Microsoft.ApplicationInsights.ApplicationInsights({
  config: configObj
});
appInsights.loadAppInsights();
```

## <a name="sample-app"></a>샘플 앱

[클릭 분석 자동 수집 플러그 인을 사용하도록 설정된 간단한 웹앱](https://go.microsoft.com/fwlink/?linkid=2152871).

## <a name="next-steps"></a>다음 단계

- 클릭 분석 자동 수집 플러그 인에 대한 [GitHub 리포지토리](https://github.com/microsoft/ApplicationInsights-JS/tree/master/extensions/applicationinsights-clickanalytics-js) 및 [NPM 패키지](https://www.npmjs.com/package/@microsoft/applicationinsights-clickanalytics-js)를 확인합니다.
- [사용 환경의 이벤트 분석](usage-segmentation.md)을 사용하여 사용 가능한 차원별 상위 클릭 및 슬라이스를 분석합니다.
- [Log Analytics](../logs/log-analytics-tutorial.md#write-a-query)의 CustomEvents 테이블에 있는 customDimensions 특성 내 콘텐츠 필드에서 데이터 클릭을 찾습니다. 추가 지침은 [샘플 앱](https://go.microsoft.com/fwlink/?linkid=2152871)을 참조하세요.
- [통합 문서](../visualize/workbooks-overview.md)를 빌드하거나 [Power BI로 내보내서](../logs/log-powerbi.md#integrating-queries) 클릭 데이터에 대한 사용자 지정 시각화를 만듭니다.
