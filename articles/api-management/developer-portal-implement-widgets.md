---
title: 개발자 포털에서 위젯 구현
titleSuffix: Azure API Management
description: 외부 API의 데이터를 사용하는 위젯을 구현하고 API Management 개발자 포털에 표시하는 방법을 알아봅니다.
author: dlepow
ms.author: danlep
ms.date: 04/15/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: af2ac28df5b41606d9199d6fa36c3f683e86c59d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128639910"
---
# <a name="implement-widgets-in-the-developer-portal"></a>개발자 포털에서 위젯 구현

이 자습서에서는 외부 API의 데이터를 사용하고 API Management 개발자 포털에 표시하는 위젯을 구현합니다.

위젯은 샘플 [컨퍼런스 API](https://conferenceapi.azurewebsites.net/?format=json)에서 세션 설명을 검색합니다. 세션 식별자는 지정된 위젯 편집기를 통해 설정됩니다.

개발 프로세스에 도움이 필요한 경우, API Management 개발자 포털 [GitHub 리포지토리](https://github.com/Azure/api-management-developer-portal/): `/examples/widgets/conference-session`의 `examples` 폴더에 있는 완성된 위젯을 참조하세요.

:::image type="content" source="media/developer-portal-implement-widgets/widget-published.png" alt-text="게시된 위젯의 스크린샷":::

## <a name="prerequisites"></a>사전 요구 사항

* 개발자 포털의 최신 릴리스에 맞는 [로컬 환경](developer-portal-self-host.md#step-1-set-up-local-environment)을 설정합니다.

* [Paperbits 위젯 구조](https://paperbits.io/wiki/widget-anatomy)에 대한 이해가 필요합니다.


## <a name="copy-the-scaffold"></a>스캐폴드 복사

`/scaffolds` 폴더의 `widget` 스캐폴드를 새 위젯을 빌드하기 위한 시작 지점으로 사용합니다.

1. `/scaffolds/widget` 폴더를 `/community/widgets`로 복사합니다.
1. 폴더 이름을 `conference-session`으로 바꿉니다.

## <a name="rename-exported-module-classes"></a>내보낸 모듈 클래스 이름 바꾸기

`Widget` 접두사를 다음 파일의 `ConferenceSession`로 바꿈으로써 내보낸 모듈 클래스의 이름을 바꿉니다.

- `widget.design.module.ts`

- `widget.publish.module.ts`

- `widget.runtime.module.ts`
    
예를 들어 `widget.design.module.ts` 파일에서 `WidgetDesignModule`을 `ConferenceSessionDesignModule`로 변경합니다.
    
```typescript
export class WidgetDesignModule implements IInjectorModule {
```
을 
    
```typescript
export class ConferenceSessionDesignModule implements IInjectorModule {
```
    
   
## <a name="register-the-widget"></a>위젯 등록

각 파일에 다음 줄을 추가하여 포털의 루트 모듈에 위젯 모듈을 등록합니다.

1. `src/apim.design.module.ts` - 디자인 타임 종속성을 등록하는 모듈입니다.

   ```typescript
   import { ConferenceSessionDesignModule } from "../community/widgets/conference-session/widget.design.module";
   
   ...
   injector.bindModule(new ConferenceSessionDesignModule());
   ```
1. `src/apim.publish.module.ts` - 게시 타임 종속성을 등록하는 모듈입니다.

   ```typescript
   import { ConferenceSessionPublishModule } from "../community/widgets/conference-session/widget.publish.module";

    ...

    injector.bindModule(new ConferenceSessionPublishModule());
    ```

1. `src/apim.runtime.module.ts` - 런타임 종속성입니다.

    ```typescript
    import { ConferenceSessionRuntimeModule } from "../community/widgets/conference-session/widget.runtime.module";

    ...

    injector.bindModule(new ConferenceSessionRuntimeModule());
    ```

## <a name="place-the-widget-in-the-portal"></a>포털에 위젯 넣기

이제 중복된 스캐폴드를 연결하고 개발자 포털에서 사용할 준비가 되었습니다.

1. `npm start` 명령을 실행합니다.

1. 애플리케이션이 로드되면 페이지에 새 위젯을 배치합니다. `Community` 위젯 선택기의 범주에 있는 `Your widget` 이름 아래에서 찾을 수 있습니다.

    :::image type="content" source="media/developer-portal-implement-widgets/widget-selector.png" alt-text="위젯 선택기의 스크린샷":::

1. **Ctrl**+**S**(또는 macOS에서 **⌘**+**S**)를 눌러 페이지를 저장합니다.

    > [!NOTE]
    > 디자인 타임에서 **Ctrl**(또는 **⌘**) 키를 누른 채로 계속해서 웹 사이트와 상호 작용할 수 있습니다.

## <a name="add-custom-properties"></a>사용자 지정 속성 추가

위젯이 세션 설명을 가져오려면 세션 식별자를 알고 있어야 합니다. `Session ID` 속성을 각 인터페이스와 클래스에 추가합니다.

위젯이 세션 설명을 가져오려면 세션 식별자를 알고 있어야 합니다. 세션 ID 속성을 각 인터페이스와 클래스에 추가합니다.

1. `widgetContract.ts` - 위젯 구성 유지 방법을 정의하는 데이터 계약(데이터 레이어)

    ```typescript
    export interface WidgetContract extends Contract {
        sessionNumber: string;
    }
    ```

1. `widgetModel.ts` - 모델(비즈니스 레이어). 시스템의 위젯 기본 표현입니다. 편집기로 업데이트되며 프레젠테이션 레이어에서 렌더링 됩니다.

    ```typescript
    export class WidgetModel {
        public sessionNumber: string;
    }
    ```

1. `ko/widgetViewModel.ts` - viewmodel(프레젠테이션 레이어). 개발자 포털이 HTML 템플릿을 사용하여 렌더링하는 UI 프레임 워크 관련 개체입니다.

    > [!NOTE]
    > 이 파일에서는 아무것도 변경할 필요가 없습니다.

## <a name="configure-binders"></a>바인더 구성

데이터 원본에서 위젯 프레젠테이션으로의 `sessionNumber` 흐름을 사용하도록 설정합니다. `ModelBinder`와 `ViewModelBinder` 엔터티를 편집합니다.

1. `widgetModelBinder.ts`는 계약에 설명된 데이터를 사용하는 모델을 준비하는 데 도움이 됩니다.

    ```typescript
    export class WidgetModelBinder implements IModelBinder<WidgetModel> {
        public async contractToModel(contract: WidgetContract): Promise<WidgetModel> {
            model.sessionNumber = contract.sessionNumber || "107"; // 107 is the default session id
            ...
        }
    
        public modelToContract(model: WidgetModel): Contract {
            const contract: WidgetContract = {
                sessionNumber: model.sessionNumber
                ...
            };
            ...
        }
    }
    ```

1. `ko/widgetViewModelBinder.ts`는 개발자 포털이 특정 UI 프레임 워크에서 모델(viewmodel)을 어떻게 제공해야 하는지 알고 있습니다.

    ```typescript
    ...
    public async updateViewModel(model: WidgetModel, viewModel: WidgetViewModel): Promise<void> {
            viewModel.runtimeConfig(JSON.stringify({
                sessionNumber: model.sessionNumber
            }));
        }
    }
    ...
    ```

## <a name="adjust-design-time-widget-template"></a>디자인 타임 위젯 템플릿 조정

각 범위의 구성 요소는 독립적으로 실행됩니다. 별도의 종속성 주입 컨테이너, 자체 구성, 수명 주기 등이 있습니다. 다른 UI 프레임워크(이 예제에서는 Knockout JS)에 의해 구동될 수도 있습니다.

디자인 타임 관점에서 모든 런타임 구성 요소는 특정 속성 및/또는 콘텐츠가 포함된 HTML 태그일 뿐입니다. 필요한 경우 구성이 일반 표시로 전달됩니다. 이 예와 같이 간단한 경우에는 매개 변수가 특성으로 전달됩니다. 구성이 더 복잡한 경우 지정된 구성 공급자(예: `ISettingsProvider`)로 가져온 필수 설정의 식별자를 사용할 수 있습니다.

1. `ko/widgetView.html` 파일을 업데이트합니다.

    ```html
    <widget-runtime data-bind="attr: { params: runtimeConfig }"></widget-runtime>
    ```

    개발자 포탈이 디자인 타임 또는 게시 시간에서 `attr` 바인딩을 실행하는 경우 HTML 결과는 다음과 같습니다.

    ```html
    <widget-runtime params="{ sessionNumber: 107 }"></widget-runtime>
    ```

    그런 다음 런타임에서 `widget-runtime` 구성 요소는 `sessionNumber`를 읽고 초기화 코드에서 이를 사용합니다(아래 참조).

1. 만들 때 `widgetHandlers.ts` 파일을 업데이트하여 세션 ID를 할당합니다.

    ```typescript
    ...
    createModel: async () => {
        var model = new ConferenceSessionModel();
        model.sessionNumber = "107";
            return model;
        }
    ...
    ```

## <a name="revise-runtime-view-model"></a>런타임 뷰 모델 수정

런타임 구성 요소는 웹 사이트 자체에서 실행되는 코드입니다. 예를 들어 API Management 개발자 포털에서 구성 요소는 모든 스크립트 뒤의 동적 구성 요소(예: API 정보, API 콘솔)이며, 코드 샘플 생성, 요청 보내기 등의 작업을 처리합니다.

런타임 구성 요소의 뷰 모델에는 다음 메서드 및 속성이 있어야 합니다.

- `sessionNumber` 속성(`Param` 데코레이터로 표시됨)은 외부에서 전달된 구성 요소 입력 매개 변수로 사용됩니다(디자인 타임에 생성된 태그, 이전 단계 참조).
- `sessionDescription` 속성은 위젯 템플릿에 바인딩됩니다(본 문서 뒷부분의 `widget-runtime.html` 참조).
- `initialize` 메서드(`OnMounted` 데코레이터 포함)는 위젯이 생성되고 모든 매개 변수가 할당된 후 호출됩니다. `sessionNumber`를 읽고 `HttpClient`를 사용하여 API를 호출하기에 적절한 위치입니다. `HttpClient`는 IoC(Inversion of Control) 컨테이너에 의해 주입된 종속성입니다.

- 먼저 개발자 포털에서 위젯을 만들고 모든 매개 변수를 할당합니다. 그런 다음 `initialize` 메서드를 호출합니다.

    ```typescript
    ...
    import * as ko from "knockout";
    import { Component, RuntimeComponent, OnMounted, OnDestroyed, Param } from "@paperbits/common/ko/decorators";
    import { HttpClient, HttpRequest } from "@paperbits/common/http";
    ...

    export class WidgetRuntime {
        public readonly sessionDescription: ko.Observable<string>;

        constructor(private readonly httpClient: HttpClient) {
            ...
            this.sessionNumber = ko.observable();
            this.sessionDescription = ko.observable();
            ...
        }

        @Param()
        public readonly sessionNumber: ko.Observable<string>;

        @OnMounted()
        public async initialize(): Promise<void> {
            ...
            const sessionNumber = this.sessionNumber();

            const request: HttpRequest = {
                url: `https://conferenceapi.azurewebsites.net/session/${sessionNumber}`,
                method: "GET"
            };

            const response = await this.httpClient.send<string>(request);
            const sessionDescription = response.toText();
    
            this.sessionDescription(sessionDescription);
            ...
        }
        ...
    }
    ```

## <a name="tweak-the-widget-template"></a>위젯 템플릿 조정

위젯을 업데이트하여 세션 설명을 표시합니다.

`ko/runtime/widget-runtime.html` 파일에서 단락 태그와 `markdown`(또는 `text`) 바인딩을 사용하여 설명을 렌더링합니다.

```html
<p data-bind="markdown: sessionDescription"></p>
```

## <a name="add-the-widget-editor"></a>위젯 편집기 추가

위젯을 구성하여 `107` 세션에 대한 설명을 가져옵니다. `107`을 코드에서 기본 세션으로 지정했습니다. 모든 것이 제대로 수행되었는지 확인하려면 `npm start`를 실행하고 개발자 포털에서 페이지 설명이 표시되는지 확인합니다.

다음 단계를 수행하여 사용자가 위젯 편집기를 통해 세션 ID를 설정할 수 있도록 합니다.

1. `ko/widgetEditorViewModel.ts` 파일을 업데이트합니다.

    ```typescript
    export class WidgetEditor implements WidgetEditor<WidgetModel> {
        public readonly sessionNumber: ko.Observable<string>;

        constructor() {
            this.sessionNumber = ko.observable();
        }

        @Param()
        public model: WidgetModel;

        @Event()
        public onChange: (model: WidgetModel) => void;

        @OnMounted()
        public async initialize(): Promise<void> {
            this.sessionNumber(this.model.sessionNumber);
            this.sessionNumber.subscribe(this.applyChanges);
        }

        private applyChanges(): void {
            this.model.sessionNumber = this.sessionNumber();
            this.onChange(this.model);
        }
    }
    ```

    편집기 보기 모델은 이전에 본 것과 동일한 접근 방식을 사용하지만 `@Event()`로 데코레이트된 새 속성 `onChange`이 있습니다. 수신기(이 경우에는 내용 편집기)에 모델 변경 내용을 알리기 위해 콜백을 연결합니다.

1. `ko/widgetEditorView.html` 파일을 업데이트합니다.

    ```html
    <input type="text" class="form-control" data-bind="textInput: sessionNumber" />
    ```

1. `npm start`을 다시 실행합니다. 위젯 편집기에서 `sessionNumber`를 변경할 수 있어야 합니다. ID를 `108`로 변경하고 변경 내용을 저장한 다음 브라우저의 탭을 새로 고칩니다. 문제가 발생할 경우 페이지에 위젯을 다시 추가해야 할 수 있습니다.

    :::image type="content" source="media/developer-portal-implement-widgets/widget-editor.png" alt-text="위젯 편집기의 스크린샷":::

## <a name="rename-the-widget"></a>위젯 이름 바꾸기

`constants.ts` 파일에서 위젯 이름을 변경합니다.

```typescript
...
export const widgetName = "conference-session";
export const widgetDisplayName = "Conference session";
...
```

> [!NOTE]
> 위젯을 리포지토리에 기여하는 경우 `widgetName`은 폴더 이름과 동일해야 하며 표시 이름에서 파생되어야 합니다(소문자 및 공백은 대시로 대체됨). `Community` 카테고리가 유지되어야 합니다.

## <a name="next-steps"></a>다음 단계


다음 문서를 통해 개발자 포털에 대해 자세히 알아봅니다.

- [Azure API Management 개발자 포털 개요](api-management-howto-developer-portal.md)

- [위젯 기여](developer-portal-widget-contribution-guidelines.md) - Microsoft에서는 여러분의 기여를 반기고 장려합니다.

- 커뮤니티에서 기여한 위젯의 사용 방법을 알아보려면 [커뮤니티 위젯 사용](developer-portal-use-community-widgets.md)을 참조하세요.
