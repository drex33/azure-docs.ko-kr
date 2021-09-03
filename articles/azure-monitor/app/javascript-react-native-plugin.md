---
title: Application Insights JavaScript SDK용 React Native 플러그 인
description: Application Insights JavaScript SDK용 React Native 플러그 인을 설치하고 사용하는 방법입니다.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 6b407fdc0614d94862257ce3bebc0f58f971f9d7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528686"
---
# <a name="react-native-plugin-for-application-insights-javascript-sdk"></a>Application Insights JavaScript SDK용 React Native 플러그 인

Application Insights JavaScript SDK용 React Native 플러그 인은 디바이스 정보를 수집합니다. 이 플러그 인이 기본적으로 자동 수집하는 정보는 다음과 같습니다.

- **고유한 디바이스 ID**(설치 ID라고도 함)
- **디바이스 모델 이름**(예: iPhone X, Samsung Galaxy Fold, Huawei P30 Pro 등)
- **디바이스 유형**(예: 송수화기, 태블릿 등)

## <a name="requirements"></a>요구 사항

`@microsoft/applicationinsights-web`의 2.0.0 버전 이상을 사용해야 합니다. 해당 플러그 인은 원시 앱에만 반응하여 작동합니다. 해당 작업은 [Expo 프레임워크를 사용하는 앱](https://docs.expo.io/)에서는 작동하지 않으므로 Create React Native App에서는 작동하지 않습니다.

## <a name="getting-started"></a>시작

[반응-원시-디바이스-정보](https://www.npmjs.com/package/react-native-device-info) 패키지를 설치하고 연결합니다. 앱을 사용하여 최신 디바이스 이름을 수집하려면 `react-native-device-info` 패키지를 최신 상태로 유지하세요.

```zsh

npm install --save @microsoft/applicationinsights-react-native @microsoft/applicationinsights-web
npm install --save react-native-device-info
react-native link react-native-device-info

```

## <a name="initializing-the-plugin"></a>플러그 인 초기화

해당 플러그 인을 사용하려면 플러그 인을 구성하고 `extension`을 기존 Application Insights 인스턴스에 추가해야 합니다.

```typescript
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ReactNativePlugin } from '@microsoft/applicationinsights-react-native';

var RNPlugin = new ReactNativePlugin();
var appInsights = new ApplicationInsights({
    config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [RNPlugin]
    }
});
appInsights.loadAppInsights();

```

## <a name="next-steps"></a>다음 단계

- JavaScript SDK에 대한 보다 자세한 정보는 [Application Insights JavaScript SDK 설명서](javascript.md)를 참조하세요.
- Kusto 쿼리 언어 및 Log Analytics에서 데이터를 쿼리하는 방법에 대한 자세한 내용은 [로그 쿼리 개요](../../azure-monitor/logs/log-query-overview.md)를 참조하세요.
