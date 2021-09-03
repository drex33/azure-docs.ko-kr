---
title: Azure Static Web Apps의 사전 프로덕션 환경에서 끌어오기 요청 검토
description: Azure Static Web Apps에서 사전 프로덕션 환경을 사용하여 끌어오기 요청 변경 내용을 검토하는 방법에 대해 알아봅니다.
services: static-web-apps
author: sinedied
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: yolasors
ms.openlocfilehash: 3869b18f580d0a12b2581554acf63258cb08f64a
ms.sourcegitcommit: 351279883100285f935d3ca9562e9a99d3744cbd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/19/2021
ms.locfileid: "112380683"
---
# <a name="review-pull-requests-in-pre-production-environments-in-azure-static-web-apps"></a>Azure Static Web Apps의 사전 프로덕션 환경에서 끌어오기 요청 검토

이 문서에서는 사전 프로덕션 환경을 사용하여 [Azure Static Web Apps](overview.md)와 함께 배포된 애플리케이션에 대한 변경 내용을 검토하는 방법을 보여 줍니다.

사전 프로덕션(스테이징) 환경은 프로덕션에서 사용할 수 없는 변경 내용을 포함하는 애플리케이션의 완벽하게 작동하는 스테이징된 버전입니다.

Azure Static Web Apps는 리포지토리에서 GitHub Actions 워크플로를 생성합니다. 워크플로가 감시하는 분기에 대해 끌어오기 요청을 만들면 사전 프로덕션 환경이 빌드됩니다. 사전 프로덕션 환경에서 앱을 단계별로 실행하여 프로덕션으로 푸시하기 전에 검토를 수행할 수 있습니다.

Azure Static Web Apps를 사용하는 동시에 여러 사전 프로덕션 환경이 동시에 존재할 수 있습니다. 조사되는 분기에 대해 끌어오기 요청을 만들 때마다 변경 내용으로 스테이징된 버전이 고유한 사전 프로덕션 환경에 배포됩니다.

사전 프로덕션 환경을 사용할 경우 많은 이점이 있습니다. 예를 들어, 다음을 수행할 수 있습니다.

- 프로덕션 및 스테이징 간의 시각적 변경 내용을 검토합니다. 예를 들어 콘텐츠와 레이아웃에 대한 업데이트를 볼 수 있습니다.
- 팀에 변경 내용을 보여 줍니다.
- 애플리케이션의 서로 다른 버전을 비교합니다.
- 수용 테스트를 사용하여 변경 내용의 유효성을 검사합니다.
- 프로덕션 환경에 배포하기 전에 온전성 검사를 수행합니다.

> [!NOTE]
> 끌어오기 요청과 사전 프로덕션 환경은 현재 GitHub Actions 배포에서만 지원됩니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure Static Web Apps로 구성된 기존 GitHub 리포지토리입니다. 없는 경우 [첫 번째 정적 앱 빌드](getting-started.md)를 참조하세요.

## <a name="make-a-change"></a>변경

리포지토리를 변경하여 시작합니다. 다음 단계에 표시된 것처럼 GitHub에서 직접 수행할 수 있습니다.

1. GitHub에서 프로젝트의 리포지토리로 이동한 다음, **분기** 단추를 클릭하여 새 분기를 만듭니다.

    :::image type="content" source="./media/review-publish-pull-requests/create-branch.png" alt-text="GitHub 인터페이스를 사용하여 새 분기 만들기":::]

    분기 이름을 입력하고 **분기 만들기** 를 클릭합니다.

1. _앱_ 폴더로 이동하여 일부 텍스트 콘텐츠를 변경합니다. 예를 들어 제목 또는 단락을 변경할 수 있습니다. 편집하려는 파일을 찾았으면 **편집** 을 클릭하여 변경합니다.

    :::image type="content" source="./media/review-publish-pull-requests/edit-file.png" alt-text="GitHub 인터페이스의 파일 편집 단추":::

1. 변경을 수행한 후 **변경 내용 커밋** 을 클릭하여 분기에 대한 변경 내용을 커밋합니다.

    :::image type="content" source="./media/review-publish-pull-requests/commit-changes.png" alt-text="GitHub 인터페이스의 번경 내용 커밋 단추":::

## <a name="create-a-pull-request"></a>끌어오기 요청 만들기

다음으로 이 변경에서 끌어오기 요청을 만듭니다.

1. GitHub에서 프로젝트의 **끌어오기 요청** 탭을 엽니다.

    :::image type="content" source="./media/review-publish-pull-requests/tab.png" alt-text="GitHub 리포지토리의 끌어오기 요청 탭":::

1. 분기의 **비교 & 끌어오기 요청** 단추를 클릭합니다.

1. 필요에 따라 변경 내용에 대한 일부 세부 정보를 입력한 다음, **끌어오기 요청 만들기** 를 클릭할 수 있습니다.

    :::image type="content" source="./media/review-publish-pull-requests/open.png" alt-text="GitHub에서 끌어오기 요청 만들기":::

필요한 경우 검토자를 할당하고 의견을 추가하여 변경 내용을 논의할 수 있습니다.

> [!NOTE]
> 분기에 새 커밋을 푸시하여 여러 변경 작업을 수행할 수 있습니다. 그러면 끌어오기 요청이 모든 변경 내용을 반영하도록 자동으로 업데이트됩니다.

## <a name="review-changes"></a>변경 내용 검토

끌어오기 요청을 만든 후에는 [GitHub Actions](https://github.com/features/actions) 배포 워크플로가 실행되고 사전 프로덕션 환경에 변경 내용이 배포됩니다.

워크플로에서 앱 빌드 및 배포를 완료하면 GitHub 봇은 사전 프로덕션 환경의 URL을 포함하는 주석을 끌어오기 요청에 추가합니다. 이 링크를 클릭하면 스테이징된 변경 사항을 볼 수 있습니다.

:::image type="content" source="./media/review-publish-pull-requests/bot-comment.png" alt-text="사전 프로덕션 URL이 포함된 끌어오기 요청 주석":::

생성된 URL을 클릭하여 변경 내용을 확인합니다.

URL을 자세히 살펴보면 다음과 같이 구성된 것을 볼 수 있습니다. `https://<SUBDOMAIN-PULL_REQUEST_ID>.<AZURE_REGION>.azurestaticapps.net`

지정된 끌어오기 요청에 대해 URL은 새 업데이트를 푸시하는 경우에도 동일하게 유지됩니다. URL을 유지하는 것 외에도, 동일한 사전 프로덕션 환경은 끌어오기 요청 수명 동안 다시 사용됩니다.

## <a name="publish-changes"></a>변경 내용 게시

변경 내용이 승인되면 끌어오기 요청을 병합하여 변경 내용을 프로덕션에 게시할 수 있습니다.

**끌어오기 요청 병합** 을 클릭합니다.

:::image type="content" source="./media/review-publish-pull-requests/merge.png" alt-text="GitHub 인터페이스의 끌어오기 요청 병합 단추":::

병합하면 변경 내용이 추적된 분기("프로덕션" 분기)로 복사됩니다. 그런 다음, 추적된 분기에서 배포 워크플로가 시작되고 애플리케이션을 다시 빌드한 후에 변경 내용이 적용됩니다.

프로덕션에서 변경 내용을 확인하려면 프로덕션 URL을 열어 웹 사이트의 라이브 버전을 로드합니다.

## <a name="limitations"></a>제한 사항

- 애플리케이션의 스테이징된 버전은 GitHub 리포지토리가 개인인 경우에도 현재 해당 URL에서 공개적으로 액세스할 수 있습니다.

    > [!WARNING]
    > 사전 프로덕션 환경에 대한 액세스는 제한되지 않으므로 중요한 콘텐츠를 스테이징된 버전에 게시할 때는 주의해야 합니다.

- Static Web Apps로 배포된 각 앱에 사용할 수 있는 사전 프로덕션 환경 수는 사용 중인 [호스팅 플랜](plans.md)에 따라 다릅니다. 예를 들어 무료 계층을 사용하는 경우 프로덕션 환경 외에도 세 개의 사전 프로덕션 환경을 사용할 수 있습니다.

- 사전 프로덕션 환경은 지리적으로 분산되지 않습니다.

- 현재 GitHub Actions 배포에서만 사전 프로덕션 환경을 지원합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [사용자 지정 도메인 설정](custom-domain.md)
