---
title: Application Insights JavaScript SDK에 대한 Angular 플러그 인
description: Application Insights JavaScript SDK에 대한 Angular 플러그 인을 설치하고 사용하는 방법입니다.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/07/2020
author: mattmccleary
ms.author: mmcc
ms.openlocfilehash: dc5b7e3f1e452bbbf4e65442cbf683b4cb1a8816
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130129817"
---
# <a name="angular-plugin-for-application-insights-javascript-sdk"></a>Application Insights JavaScript SDK에 대한 Angular 플러그 인

Application Insights JavaScript SDK에 대한 Angular 플러그 인은 다음을 지원합니다.

- 라우터 변경 추적
- 확인되지 않은 예외 추적

> [!WARNING]
> Angular 플러그 인은 ES3(ECMAScript 3)과 호환되지 않습니다.

## <a name="getting-started"></a>시작

npm 패키지 설치:

```bash
npm install @microsoft/applicationinsights-angularplugin-js
```

## <a name="basic-usage"></a>기본적인 사용 방법

앱의 항목 구성 요소에서 Application Insights의 인스턴스를 설정합니다.

```js
import { Component } from '@angular/core';
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { AngularPlugin } from '@microsoft/applicationinsights-angularplugin-js';
import { Router } from '@angular/router';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
    constructor(
        private router: Router
    ){
        var angularPlugin = new AngularPlugin();
        const appInsights = new ApplicationInsights({ config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [angularPlugin],
        extensionConfig: {
            [angularPlugin.identifier]: { router: this.router }
        }
        } });
        appInsights.loadAppInsights();
    }
}
```

확인되지 않은 예외를 추적하려면 `app.module.ts`에서 ApplicationinsightsAngularpluginErrorService를 설정합니다.

```js
import { ApplicationinsightsAngularpluginErrorService } from '@microsoft/applicationinsights-angularplugin-js';

@NgModule({
  ...
  providers: [
    {
      provide: ErrorHandler,
      useClass: ApplicationinsightsAngularpluginErrorService
    }
  ]
  ...
})
export class AppModule { }
```

## <a name="next-steps"></a>다음 단계

- JavaScript SDK에 대해 자세히 알아보려면 [Application Insights JavaScript SDK 설명서](javascript.md)를 참조하세요.
- [GitHub의 Angular 플러그 인](https://github.com/microsoft/ApplicationInsights-JS/tree/master/extensions/applicationinsights-angularplugin-js)
