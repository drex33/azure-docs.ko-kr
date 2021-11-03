---
title: Azure 컨테이너 앱 미리 보기의 수정 버전
description: Azure Container Apps에서 수정 버전을 만드는 방법을 알아봅니다.
services: app-service
author: craigshoemaker
ms.service: app-service
ms.topic: conceptual
ms.date: 09/16/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: f91168f5d879e9e8844f5b86cee70fe1a0193b09
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131103230"
---
# <a name="revisions-in-azure-container-apps-preview"></a>Azure 컨테이너 앱 미리 보기의 수정 버전

수정 버전은 컨테이너 앱의 변경할 수 없는 스냅숏입니다.

- 컨테이너 앱을 배포할 때 첫 번째 수정 버전이 자동으로 생성 됩니다.
- 컨테이너 앱의 구성이 변경 되 면 새 수정 버전이 자동으로 생성 됩니다 `template` .
- 수정 버전은 변경할 수 없지만 모든 수정 버전에 적용 되는 전역 구성 값에 대 한 변경의 영향을 받습니다.

:::image type="content" source="media/revisions/azure-container-apps-revisions.png" alt-text="Azure 컨테이너 앱: 컨테이너":::

수정 버전은 [수신](ingress.md) 이 HTTP를 통해 컨테이너 앱에 액세스할 수 있도록 설정 하는 경우에 가장 유용 합니다.  컨테이너 앱의 한 스냅숏에서 트래픽을 다음으로 전달 하려는 경우 수정 버전이 자주 사용 됩니다. 일반적인 트래픽 방향 전략 [에는 A/B 테스트](https://wikipedia.org/wiki/A/B_testing) 및 [BlueGreen 배포가](https://martinfowler.com/bliki/BlueGreenDeployment.html)포함 됩니다.

다음 다이어그램에서는 두 개의 수정 버전이 있는 컨테이너 앱을 보여 줍니다.

:::image type="content" source="media/revisions/azure-container-apps-revisions-traffic-split.png" alt-text="Azure 컨테이너 앱: 수정 버전 간 트래픽 분할":::

위에 표시 된 시나리오에서는 컨테이너 앱이 다음 상태임을 가정 합니다.

- [수신](ingress.md) 이 활성화 되어 있으므로 HTTP를 통해 컨테이너 앱을 사용할 수 있습니다.
- 첫 번째 수정 버전은 _수정 버전 1_ 로 배포 됩니다.
- 컨테이너가 업데이트 된 후 새 수정 버전이 _수정 버전 2_ 로 활성화 되었습니다.
- [트래픽 분할](revisions-manage.md#traffic-splitting) 규칙은 _수정 1_ 이 요청을 80% 수신 하는 반면, _수정 2_ 는 남은 20%를 수신 하도록 구성 됩니다.

## <a name="change-types"></a>변경 형식

컨테이너 앱에 대 한 변경 내용은 두 범주 ( *수정 버전 범위* 및 *응용 프로그램 범위* 변경) 중 하나에 속합니다. 수정 버전 범위 변경은 새 수정 버전을 트리거하는 변경 내용 이지만 응용 프로그램 범위 변경 내용은 수정 사항을 만들지 않습니다.

### <a name="revision-scope-changes"></a>수정 버전 범위 변경

다음 유형의 변경 내용은 새 수정 버전을 만듭니다.

- 컨테이너 변경 내용
- 크기 조정 규칙 추가 또는 업데이트
- Aapr 설정 변경
- 구성의 섹션에 영향을 주는 변경 내용 `template`

### <a name="application-scope-changes"></a>응용 프로그램 범위 변경

다음 유형의 변경 내용은 새 수정 버전을 만들지 않습니다.

- [트래픽 분할 규칙](revisions-manage.md#traffic-splitting) 에 대 한 변경 내용
- [수신](ingress.md) 설정 또는 해제
- [비밀 값](secure-app.md) 에 대 한 변경 내용
- 구성의 섹션 외부에 있는 모든 변경 내용 `template`

비밀에 대 한 변경 내용은 응용 프로그램 범위 변경 이지만 컨테이너가 새 비밀 값을 인식 하기 전에 수정 버전을 [다시 시작](revisions.md) 해야 합니다.

## <a name="activation-state"></a>활성화 상태

새 수정 버전은 비활성화 될 때까지 활성 상태로 유지 되거나, 이전 수정 버전을 자동으로 비활성화 하도록 컨테이너 앱을 설정 합니다.

- 비활성 개정은 특정 상태의 컨테이너 앱에 대 한 스냅숏 레코드로 유지 됩니다.
- 비활성 수정 버전에 대 한 요금은 청구 되지 않습니다.
- 제거 하기 전에 최대 100 개의 수정 버전을 계속 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [앱 보호](get-started.md)
