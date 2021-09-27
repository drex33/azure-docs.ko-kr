---
title: 개발자 포털용 위젯을 추가하는 방법
titleSuffix: Azure API Management
description: API Management 개발자 포털 리포지토리에 위젯을 추가할 때 따라야 하는 권장 지침에 대해 알아봅니다.
author: dlepow
ms.author: danlep
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: ae491880dc7dc69a3930e6c967fb7a660db75f38
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128631479"
---
# <a name="how-to-contribute-widgets-to-the-api-management-developer-portal"></a>API Management 개발자 포털에 위젯을 추가하는 방법

API Management 개발자 포털 [GitHub 리포지토리](https://github.com/Azure/api-management-developer-portal)에 위젯을 추가하려면 아래의 3단계 프로세스를 따르세요.

1. 리포지토리를 포크합니다.

1. 위젯을 구현합니다.

1. 끌어오기 요청을 생성하여 공식 리포지토리에 위젯을 추가합니다.

위젯은 리포지토리의 라이선스를 상속합니다. 이렇게 추가한 위젯은 포털의 자체 호스팅 버전에서 [옵트인 설치](developer-portal-use-community-widgets.md)로 이용할 수 있습니다. 개발자 포털 팀이 해당 위젯을 포털의 관리형 버전에 포함할 수도 있습니다.

[위젯 구현](developer-portal-implement-widgets.md) 자습서에서 자체 위젯을 개발하는 방법을 알아보세요.

## <a name="contribution-guidelines"></a>기여 지침

아래의 가이드는 Microsoft 고객과 고객의 포털을 방문하는 방문자들의 안전 및 개인 정보 보호를 위해 마련되었습니다. 기여가 수락될 수 있도록 아래의 지침을 따르세요.

1. 위젯을 `community/widgets/<your-widget-name>` 폴더에 저장합니다.

1. 위젯의 이름은 영숫자 소문자여야 여러 단어로 구성된 경우 대시로 구분해야 합니다. 예: `my-new-widget`.

1. 폴더에 게시된 포털에 있는 위젯의 스크린샷을 포함해야 합니다.

1. 폴더에 `/scaffolds/widget/readme.md` 파일의 템플릿을 따르는 `readme.md` 파일을 포함해야 합니다.

1. 폴더에 위젯의 종속성을 설치하거나 관리하기 위한 npm 명령이 들어 있는 `npm_dependencies` 파일을 포함할 수 있습니다.

    모든 종속성에는 명시적으로 버전을 지정해야 합니다. 예를 들면 다음과 같습니다.  

    ```console
    npm install azure-storage@2.10.3 axios@0.19.1
    ```

    위젯에서는 최소한의 종속성이 요구되어야 합니다. 모든 종속성은 검토자들이 신중하게 검토합니다. 위젯의 핵심 로직은 위젯 폴더에 오픈 소스로 포함해야 합니다. npm 패키지는 래핑하지 않습니다.

1. 위젯의 폴더에 들어 있지 않은 파일의 변경 사항은 위젯 기여의 일부로 허용되지 않습니다. 여기에는 `/package.json` 파일이 포함되나 이에 국한되지 않습니다.

1. 추적 스크립트를 삽입하거나 고객이 작성한 데이터를 사용자 지정 서비스로 보내는 것은 허용되지 않습니다.

    > [!NOTE]
    > 고객이 작성한 데이터는 `Logger` 인터페이스를 통해서만 수집할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 기여에 대한 자세한 내용은 API Management 개발자 포털 [GitHub 리포지토리](https://github.com/Azure/api-management-developer-portal/)를 참조하세요.

- 자체 위젯을 개발하는 방법을 단계적으로 알아보려면 [위젯 구현](developer-portal-implement-widgets.md)을 참조하세요.

- 커뮤니티에서 기여한 위젯을 사용하는 방법을 알아보려면 [커뮤니티 위젯 사용](developer-portal-use-community-widgets.md)을 참조하세요.