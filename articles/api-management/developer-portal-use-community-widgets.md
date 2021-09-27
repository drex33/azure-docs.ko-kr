---
title: 개발자 포털에서 커뮤니티 위젯 사용
titleSuffix: Azure API Management
description: API Management 개발자 포털의 커뮤니티 위젯에 대한 정보와, 위젯을 코드에서 삽입하고 사용하는 방법에 대해 알아봅니다.
author: dlepow
ms.author: danlep
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: 3fe751483ea22ff3464cc1fd505b313a5a48ade7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128656325"
---
# <a name="use-community-widgets-in-the-developer-portal"></a>개발자 포털에서 커뮤니티 위젯 사용

모든 개발자는 커뮤니티에서 제공한 위젯을 API Management 개발자 포털 [GitHub 리포지토리](https://github.com/Azure/api-management-developer-portal)의 `/community/widgets/`폴더에 저장합니다. 각 위젯은 개발자 포털 팀이 수락한 것입니다. 위젯을 포털의 [자체 호스트 버전](developer-portal-self-host.md)에 삽입하여 사용할 수 있습니다. 개발자 포털의 관리 버전은 현재 커뮤니티 위젯을 지원하지 않습니다.

> [!NOTE]
> 개발자 포털 팀은 제공된 위젯 및 해당 종속성을 철저하게 검사합니다. 그러나 팀에서 위젯을 로드하는 것이 안전하다고 보장하는 것은 아닙니다. 커뮤니티에서 제공하는 위젯을 사용하는 경우 스스로의 판단에 따라 결정하세요. Microsoft의 예방 조치에 대해 알아보려면 [위젯 기여 지침](developer-portal-widget-contribution-guidelines.md#contribution-guidelines)을 참조하세요.

## <a name="inject-and-use-external-widgets"></a>외부 위젯 삽입 및 사용

1. 개발자 포털의 최신 릴리스에 대한 [로컬 환경](developer-portal-self-host.md#step-1-set-up-local-environment)을 설정합니다.

1. `/community/widgets` 디렉터리의 위젯 폴더로 이동합니다. `readme.md` 파일에서 위젯의 설명을 읽습니다.

1. 포털 모듈에서 위젯을 등록합니다.

    1. `src/apim.design.module.ts` - 디자인 타임 종속성을 등록하는 모듈입니다.
    
        ```typescript
        import { WidgetNameDesignModule } from "../community/widgets/<widget-name>/widget.design.module";
    
        ...
    
            injector.bindModule(new WidgetNameDesignModule());
        ```
    
    1. `src/apim.publish.module.ts` - 게시 타임 종속성을 등록하는 모듈입니다.
    
        ```typescript
        import { WidgetNamePublishModule } from "../community/widgets/<widget-name>/widget.publish.module";
    
        ...
    
            injector.bindModule(new WidgetNamePublishModule());
        ```
    
    1. `src/apim.runtime.module.ts` - 런타임 종속성을 등록하는 모듈입니다.
    
        ```typescript
        import { WidgetNameRuntimeModule } from "../community/widgets/<widget-name>/widget.runtime.module";
    
        ...
    
            injector.bindModule(new WidgetNameRuntimeModule());
        ```

1. 위젯에 `npm_dependencies` 파일이 있는지 확인합니다.

1. 있는 경우 파일에서 명령을 복사하고 리포지토리의 최상위 디렉터리에서 실행합니다.

    이렇게 하면 위젯의 종속성이 설치됩니다.

1. `npm start`을 실행합니다.

위젯 선택기에서 **커뮤니티** 범주의 위젯을 볼 수 있습니다.

:::image type="content" source="media/developer-portal-use-community-widgets/widget-selector.png" alt-text="위젯 선택기의 스크린샷":::


## <a name="next-steps"></a>다음 단계


다음 문서를 통해 개발자 포털에 대해 자세히 알아봅니다.

- [Azure API Management 개발자 포털 개요](api-management-howto-developer-portal.md)

- [위젯 기여](developer-portal-widget-contribution-guidelines.md)
