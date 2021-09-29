---
title: Application Insights JavaScript SDK에 대한 React 플러그 인
description: Application Insights JavaScript SDK에 대한 React 플러그 인을 설치하고 사용하는 방법입니다.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: c95e408f116190bf90228f773a7d7c1530c6537e
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129217848"
---
# <a name="react-plugin-for-application-insights-javascript-sdk"></a>Application Insights JavaScript SDK에 대한 React 플러그 인

Application Insights JavaScript SDK에 대한 React 플러그 인은 다음을 지원합니다.

- 라우터 변경 추적
- React 구성 요소 사용 통계

## <a name="getting-started"></a>시작

npm 패키지 설치:

```bash

npm install @microsoft/applicationinsights-react-js @microsoft/applicationinsights-web --save

```

## <a name="basic-usage"></a>기본적인 사용 방법

Application Insights에 대한 연결을 초기화합니다.

```javascript
// AppInsights.js
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ReactPlugin } from '@microsoft/applicationinsights-react-js';
import { createBrowserHistory } from 'history';

const browserHistory = createBrowserHistory({ basename: '' });
const reactPlugin = new ReactPlugin();
const appInsights = new ApplicationInsights({
    config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [reactPlugin],
        extensionConfig: {
          [reactPlugin.identifier]: { history: browserHistory }
        }
    }
});
appInsights.loadAppInsights();
export { reactPlugin, appInsights };
```

구성 요소를 고차 구성 요소 함수로 래핑하여 Application Insights를 사용하도록 설정합니다.

```javascript
import React from 'react';
import { withAITracking } from '@microsoft/applicationinsights-react-js';
import { reactPlugin, appInsights } from './AppInsights';

// To instrument various React components usage tracking, apply the `withAITracking` higher-order
// component function.

class MyComponent extends React.Component {
    ...
}

// withAITracking takes 4 parameters ( reactPlugin, Component, ComponentName, className) 
// the first two are required and the other two are optional.

export default withAITracking(reactPlugin, MyComponent);
```

## <a name="configuration"></a>구성

| 속성    | 기본값 | Description                                                                                                    |
|---------|---------|----------------------------------------------------------------------------------------------------------------|
| history | null    | React 라우터 기록입니다. 자세한 내용은 [react-router 패키지 설명서](https://reactrouter.com/web/api/history)를 참조하세요. 구성 요소 외부에서 기록 개체에 액세스하는 방법을 알아보려면 [React-router FAQ](https://github.com/ReactTraining/react-router/blob/master/FAQ.md#how-do-i-access-the-history-object-outside-of-components)를 참조하세요.    |

### <a name="react-components-usage-tracking"></a>React 구성 요소 사용 추적

다양한 React 구성 요소 사용 추적을 계측하려면 고차 구성 요소 함수인 `withAITracking`을 적용합니다.

`ComponentDidMount` 이벤트부터 `ComponentWillUnmount` 이벤트까지의 시간을 측정합니다. 그러나 이를 보다 정확하게 측정하기 위해 사용자가 유휴 상태였던 시간을 뺍니다. `React Component Engaged Time = ComponentWillUnmount timestamp - ComponentDidMount timestamp - idle time`

Azure Portal에서 이 메트릭을 보려면 Application Insights 리소스로 이동한 후 "메트릭" 탭을 선택하고 사용자 지정 메트릭 이름 "React 구성 요소 작동 시간(초)"을 표시하도록 빈 차트를 구성하고 메트릭의 집계(합계, 평균 등)를 선택한 다음, "구성 요소 이름"별 분할을 적용합니다.

!["구성 요소 이름"별로 분할된 사용자 지정 메트릭 "React 구성 요소 작동 시간(초)"을 표시하는 차트 스크린샷](./media/javascript-react-plugin/chart.png)

사용자 지정 쿼리를 통해 Application Insights 데이터를 구분하여 요구 사항에 따라 보고서 및 시각화를 생성할 수도 있습니다. Azure Portal에서 Application Insights 리소스로 이동한 후 개요 탭의 위쪽 메뉴에서 "분석"을 선택하고 쿼리를 실행합니다.

![사용자 지정 메트릭 쿼리 결과 스크린샷](./media/javascript-react-plugin/query.png)

> [!NOTE]
> 새 사용자 지정 메트릭이 Azure Portal에 표시되는 데 최대 10분이 걸릴 수 있습니다.

## <a name="using-react-hooks"></a>React Hooks 사용

[React Hooks](https://reactjs.org/docs/hooks-reference.html)는 클래스 기반 React 구성 요소에 의존하지 않는 React 애플리케이션의 상태 및 수명 주기 관리 접근 방식입니다. Application Insights React 플러그 인은 고차 구성 요소 접근 방식과 비슷한 방식으로 작동하는 여러 Hooks 통합을 제공합니다.

### <a name="using-react-context"></a>React Context 사용

Application Insights용 React Hooks는 [React Context](https://reactjs.org/docs/context.html)를 포함하는 측면으로 사용하도록 설계되었습니다. Context를 사용하려면 위와 같이 Application Insights를 초기화한 다음, Context 개체를 가져옵니다.

```javascript
import React from "react";
import { AppInsightsContext } from "@microsoft/applicationinsights-react-js";
import { reactPlugin } from "./AppInsights";

const App = () => {
    return (
        <AppInsightsContext.Provider value={reactPlugin}>
            /* your application here */
        </AppInsightsContext.Provider>
    );
};
```

이 Context Provider는 Application Insights를 모든 자식 구성 요소 내에서 `useContext` Hook로 사용할 수 있도록 합니다.

```javascript
import React from "react";
import { useAppInsightsContext } from "@microsoft/applicationinsights-react-js";

const MyComponent = () => {
    const appInsights = useAppInsightsContext();
    const metricData = {
        average: engagementTime,
        name: "React Component Engaged Time (seconds)",
        sampleCount: 1
      };
    const additionalProperties = { "Component Name": 'MyComponent' };
    appInsights.trackMetric(metricData, additionalProperties);
    
    return (
        <h1>My Component</h1>
    );
}
export default MyComponent;
```

### `useTrackMetric`

`useTrackMetric` Hook는 구성 요소 구조에 구성 요소를 더 추가하지 않고도 `withAITracking` 고차 구성 요소의 기능을 복제합니다. Hook는 두 개의 인수를 사용합니다. 첫 번째는 Application Insights 인스턴스(`useAppInsightsContext` Hook에서 가져올 수 있음)이고, 다른 하나는 추적을 위한 구성 요소의 식별자(예: 이름)입니다.

```javascript
import React from "react";
import { useAppInsightsContext, useTrackMetric } from "@microsoft/applicationinsights-react-js";

const MyComponent = () => {
    const appInsights = useAppInsightsContext();
    const trackComponent = useTrackMetric(appInsights, "MyComponent");
    
    return (
        <h1 onHover={trackComponent} onClick={trackComponent}>My Component</h1>
    );
}
export default MyComponent;
```

고차 구성 요소처럼 작동하지만 구성 요소 수명 주기가 아닌 Hook 수명 주기 이벤트에 응답합니다. 특정 상호 작용에서 실행해야 하는 경우 사용자 이벤트에 Hook를 명시적으로 제공해야 합니다.

### `useTrackEvent`

`useTrackEvent` Hook는 단추 클릭 또는 기타 API 호출과 같이 애플리케이션에서 추적해야 할 수 있는 사용자 지정 이벤트를 추적하는 데 사용됩니다. 다음 4개의 인수를 사용합니다.
-   Application Insights 인스턴스(`useAppInsightsContext` Hook에서 가져올 수 있음)
-   이벤트의 이름
-   추적해야 하는 변경 내용을 캡슐화하는 이벤트 데이터 개체
-   초기화 시 `trackEvent` 호출을 건너뛰기 위한 skipFirstRun(선택 사항) 플래그. 기본값은 `true`로 설정됩니다.

```javascript
import React, { useState, useEffect } from "react";
import { useAppInsightsContext, useTrackEvent } from "@microsoft/applicationinsights-react-js";

const MyComponent = () => {
    const appInsights = useAppInsightsContext();
    const [cart, setCart] = useState([]);
    const trackCheckout = useTrackEvent(appInsights, "Checkout", cart);
    const trackCartUpdate = useTrackEvent(appInsights, "Cart Updated", cart);
    useEffect(() => {
        trackCartUpdate({ cartCount: cart.length });
    }, [cart]);
    
    const performCheckout = () => {
        trackCheckout();
        // submit data
    };
    
    return (
        <div>
            <ul>
                <li>Product 1 <button onClick={() => setCart([...cart, "Product 1"])}>Add to Cart</button></li>
                <li>Product 2 <button onClick={() => setCart([...cart, "Product 2"])}>Add to Cart</button></li>
                <li>Product 3 <button onClick={() => setCart([...cart, "Product 3"])}>Add to Cart</button></li>
                <li>Product 4 <button onClick={() => setCart([...cart, "Product 4"])}>Add to Cart</button></li>
            </ul>
            <button onClick={performCheckout}>Checkout</button>
        </div>
    );
}

export default MyComponent;
```

Hook를 사용하면 데이터 페이로드를 제공하여 Application Insights에 저장될 때 이벤트에 데이터를 더 추가할 수 있습니다.

## <a name="react-error-boundaries"></a>React 오류 경계

[오류 경계](https://reactjs.org/docs/error-boundaries.html)는 React 애플리케이션 내에서 발생하는 예외를 정상적으로 처리하는 방법을 제공하며, 이러한 오류가 발생하면 예외를 기록해야 할 수 있습니다. Application Insights용 React 플러그 인은 발생하는 오류를 자동으로 기록하는 오류 경계 구성 요소를 제공합니다.

```javascript
import React from "react";
import { reactPlugin } from "./AppInsights";
import { AppInsightsErrorBoundary } from "@microsoft/applicationinsights-react-js";

const App = () => {
    return (
        <AppInsightsErrorBoundary onError={() => <h1>I believe something went wrong</h1>} appInsights={reactPlugin}>
            /* app here */
        </AppInsightsErrorBoundary>
    );
};
```

`AppInsightsErrorBoundary`에는 두 개의 속성, 즉 애플리케이션용으로 만들어진 `ReactPlugin` 인스턴스와 오류가 발생할 때 렌더링할 구성 요소를 전달해야 합니다. 처리되지 않은 오류가 발생하면 오류 경계에 제공된 정보를 사용하여 `trackException`이 호출되고 `onError` 구성 요소가 표시됩니다.

## <a name="sample-app"></a>샘플 앱

[Application Insights React 데모](https://github.com/Azure-Samples/application-insights-react-demo)를 확인하세요.

## <a name="next-steps"></a>다음 단계

- JavaScript SDK에 대한 보다 자세한 정보는 [Application Insights JavaScript SDK 설명서](javascript.md)를 참조하세요.
- Kusto 쿼리 언어 및 Log Analytics에서 데이터를 쿼리하는 방법에 대한 자세한 내용은 [로그 쿼리 개요](../../azure-monitor/logs/log-query-overview.md)를 참조하세요.
