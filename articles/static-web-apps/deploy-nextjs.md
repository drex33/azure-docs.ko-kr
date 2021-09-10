---
title: '자습서: Azure Static Web Apps에서 정적으로 렌더링된 Next.js 웹 사이트 배포'
description: Azure Static Web Apps를 사용하여 Next.js 동적 사이트를 생성하고 배포합니다.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 08/05/2021
ms.author: cshoe
ms.custom: devx-track-js
ms.openlocfilehash: 1f8ef3146ce7ef1b1767c04284ddbdb191b50d81
ms.sourcegitcommit: d01c2b2719e363178720003b67b968ac2a640204
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122455906"
---
# <a name="deploy-static-rendered-nextjs-websites-on-azure-static-web-apps"></a>Azure Static Web Apps에서 정적으로 렌더링된 Next.js 웹 사이트 배포

이 자습서에서는 [Azure Static Web Apps](overview.md)에 [Next.js](https://nextjs.org) 생성 정적 웹 사이트를 배포하는 방법을 알아봅니다. Next.js 관련 사항에 대한 자세한 내용은 [시작 템플릿의 추가 정보](https://github.com/staticwebdev/nextjs-starter#readme)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/).
- GitHub 계정. [체험 계정을 만듭니다](https://github.com/join).
- 설치된 [Node.js](https://nodejs.org).

## <a name="set-up-a-nextjs-app"></a>Next.js 앱 설정

Next.js CLI를 사용하여 앱을 만드는 대신 시작 리포지토리를 사용할 수 있습니다. 시작 리포지토리에는 동적 경로를 지원하는 기존 Next.js 애플리케이션이 포함되어 있습니다.

시작하려면 템플릿 리포지토리에서 GitHub 계정 아래에 새 리포지토리를 만듭니다.

1. [https://github.com/staticwebdev/nextjs-starter/generate](https://github.com/login?return_to=/staticwebdev/nextjs-starter/generate)로 이동합니다.
1. 리포지토리 이름을 **nextjs-starter** 로 지정
1. 그런 다음, 새 리포지토리를 머신에 복제합니다. `<YOUR_GITHUB_ACCOUNT_NAME>`을 계정 이름으로 바꿔야 합니다.

    ```bash
    git clone http://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/nextjs-starter
    ```

1. 새로 복제된 Next.js 앱으로 이동합니다.

   ```bash
   cd nextjs-starter
   ```

1. 종속성을 설치합니다.

    ```bash
    npm install
    ```

1. 개발 중인 Next.js 앱을 시작합니다.

    ```bash
    npm run dev
    ```

`http://localhost:3000`으로 이동하여 앱을 엽니다. 이 경우 기본 설정 브라우저에서 다음 웹 사이트를 열어 볼 수 있습니다.

:::image type="content" source="media/deploy-nextjs/start-nextjs-app.png" alt-text="Next.js 앱 시작":::

프레임워크 또는 라이브러리를 선택하면 선택한 항목에 대한 세부 정보 페이지가 표시됩니다.

:::image type="content" source="media/deploy-nextjs/start-nextjs-details.png" alt-text="세부 정보 페이지":::

## <a name="deploy-your-static-website"></a>정적 웹 사이트 배포

다음 단계에서는 앱을 Azure Static Web Apps에 연결하는 방법을 보여 줍니다. Azure에 있으면 애플리케이션을 프로덕션 환경에 배포할 수 있습니다.

### <a name="create-a-static-app"></a>정적 앱 만들기

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
1. **리소스 만들기** 를 선택합니다.
1. **Static Web Apps** 를 검색합니다.
1. **Static Web Apps** 를 선택합니다.
1. **만들기** 를 선택합니다.
1. _기본_ 탭에서 다음 값을 입력합니다.

    | 속성 | 값 |
    | --- | --- |
    | _구독_ | Azure 구독 이름. |
    | _리소스 그룹_ | **my-nextjs-group**  |
    | _이름_ | **my-nextjs-app** |
    | _플랜 유형_ | **Free** |
    | _Azure Functions API 및 스테이징 환경을 위한 영역_ | 가장 가까운 지역을 선택합니다. |
    | _원본_ | **GitHub** |

1. **GitHub로 로그인** 을 선택하고 GitHub로 인증합니다.

1. 다음 GitHub 값을 입력합니다.

    | 속성 | 값 |
    | --- | --- |
    | _조직_ | 적절한 GitHub 조직을 선택합니다. |
    | _리포지토리_ | **nextjs-starter** 를 선택합니다. |
    | _분기_ | **기본** 을 선택합니다. |

1. _빌드 세부 정보_ 섹션의 _빌드 기본 설정_ 에서 **사용자 지정** 을 선택합니다. 빌드 구성에 대해 다음 값을 추가합니다.

    | 속성 | 값 |
    | --- | --- |
    | _앱 위치_ | 상자에 **/** 를 입력합니다. |
    | _API 위치_ | 이 상자를 비워 둡니다. |
    | _출력 위치_ | 상자에 **out** 을 입력합니다. |

### <a name="review-and-create"></a>검토 후 만들기

1. **검토 + 만들기** 단추를 선택하여 세부 정보가 모두 올바른지 확인합니다.

1. **만들기** 를 선택하여 App Service Static Web App 만들기를 시작하고 배포를 위한 GitHub Action을 프로비전합니다.

1. 배포가 완료되면 **리소스로 이동** 을 선택합니다.

1. _개요_ 창에서 *URL* 링크를 선택하여 배포된 애플리케이션을 엽니다.

웹 사이트가 즉시 로드되지 않으면 빌드가 계속 실행 중인 것입니다. 워크플로가 완료되면 브라우저를 새로 고치고 웹앱을 볼 수 있습니다.

작업 워크플로의 상태를 확인하려면 리포지토리의 작업 대시보드로 이동합니다.

```url
https://github.com/<YOUR_GITHUB_USERNAME>/nextjs-starter/actions
```

이제 `main` 분기를 변경하면 웹 사이트의 새 빌드 및 배포가 시작됩니다.

### <a name="sync-changes"></a>변경 내용 동기화

앱을 만들 때 Azure Static Web Apps는 리포지토리에 GitHub Actions 파일을 만들었습니다. 최신 버전을 로컬 리포지토리로 가져와 서버와 동기화합니다.

터미널로 돌아가 다음 `git pull origin main` 명령을 실행합니다.

## <a name="clean-up-resources"></a>리소스 정리

이 애플리케이션을 계속 사용하지 않을 경우 다음 단계를 통해 Azure Static Web App 인스턴스를 삭제할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)을 엽니다.
1. 위쪽 검색 창에서 **my-nextjs-group** 을 검색합니다.
1. 그룹 이름을 선택합니다.
1. **삭제** 단추를 선택합니다.
1. **예** 를 선택하여 삭제 작업을 확인합니다.

> [!div class="nextstepaction"]
> [사용자 지정 도메인 설정](custom-domain.md)
