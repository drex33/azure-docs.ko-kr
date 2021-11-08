---
title: Azure Container Apps 미리 보기의 수정 버전
description: Azure Container Apps에서 수정 버전을 만드는 방법 알아보기
services: container-apps
author: craigshoemaker
ms.service: container-apps
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: 543d0465e4fe16aafa911d486d2099259ac8a769
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132026656"
---
# <a name="revisions-in-azure-container-apps-preview"></a>Azure Container Apps 미리 보기의 수정 버전

수정 버전은 컨테이너 앱의 변경 불가능한 스냅샷입니다.

- 첫 번째 수정 버전은 컨테이너 앱을 배포할 때 자동으로 만들어집니다.
- 컨테이너 앱의 구성이 변경되면 새 수정 버전이 자동으로 `template` 만들어집니다.
- 수정 버전은 변경할 수 없지만 모든 수정 버전에 적용되는 전역 구성 값 변경의 영향을 받습니다.

:::image type="content" source="media/revisions/azure-container-apps-revisions.png" alt-text="Azure Container Apps: 컨테이너":::

수정 버전은 수신을 사용하도록 설정하여 HTTP를 통해 컨테이너 [앱에](ingress.md) 액세스할 수 있도록 하는 경우에 가장 유용합니다.  수정 버전은 컨테이너 앱의 한 스냅샷에서 다음 스냅샷으로 트래픽을 연결하려는 경우에 자주 사용됩니다. 일반적인 트래픽 방향 전략에는 [A/B 테스트](https://wikipedia.org/wiki/A/B_testing) 및 [BlueGreen 배포가](https://martinfowler.com/bliki/BlueGreenDeployment.html)포함되며,

다음 다이어그램은 두 개의 수정 버전이 있는 컨테이너 앱을 보여줍니다.

:::image type="content" source="media/revisions/azure-container-apps-revisions-traffic-split.png" alt-text="Azure Container Apps: 수정 버전 간에 트래픽 분할":::

위에 표시된 시나리오에서는 컨테이너 앱이 다음과 같은 상태라고 가정합니다.

- [수신이](ingress.md) 활성화되어 컨테이너 앱을 HTTP를 통해 사용할 수 있습니다.
- 첫 번째 수정 버전은 _수정 버전 1_ 로 배포됩니다.
- 컨테이너가 업데이트된 후 새 수정 버전이 _수정 버전 2_ 로 활성화되었습니다.
- [트래픽 분할](revisions-manage.md#traffic-splitting) 규칙은 _수정 버전 1이_ 요청의 80%를 수신하고 수정 버전 2는 나머지 _20%를_ 받도록 구성됩니다.

## <a name="change-types"></a>변경 형식

컨테이너 앱에 대한 변경 내용은 수정 *범위* 및 *애플리케이션 범위* 변경의 두 가지 범주 중 하나에 속합니다. 수정 범위 변경은 새 수정 버전을 트리거하는 변경이지만 애플리케이션 범위 변경은 수정 버전을 만들지 않습니다.

### <a name="revision-scope-changes"></a>수정 범위 변경

다음 유형의 변경 내용으로 새 수정 버전이 생성되었습니다.

- 컨테이너 변경 내용
- 크기 조정 규칙 추가 또는 업데이트
- Dapr 설정 변경
- 구성의 섹션에 영향을 주는 모든 변경 `template`

### <a name="application-scope-changes"></a>애플리케이션 범위 변경

다음 유형의 변경으로 새 수정 버전이 만들어지지 않습니다.

- 트래픽 [분할 규칙의](revisions-manage.md#traffic-splitting) 변경 내용
- [수신](ingress.md) 켜기 또는 끄기
- 비밀 [값에](secure-app.md) 대한 변경 내용
- 구성 섹션 `template` 외부의 변경

비밀에 대한 변경은 애플리케이션 범위 변경이지만 컨테이너가 새 비밀 값을 인식하기 전에 수정 버전을 [다시](revisions.md) 시작해야 합니다.

## <a name="activation-state"></a>활성화 상태

새 수정 버전은 비활성화하거나 컨테이너 앱이 이전 수정 버전을 자동으로 비활성화하도록 설정할 때까지 활성 상태로 유지됩니다.

- 비활성 수정 버전은 컨테이너 앱의 스냅샷 레코드로 특정 상태로 유지됩니다.
- 비활성 수정 버전에 대한 요금은 청구되지 않습니다.
- 제거되기 전에 최대 100개의 수정 버전을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [앱 보호](get-started.md)
