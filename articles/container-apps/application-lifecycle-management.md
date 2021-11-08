---
title: Azure Container Apps 미리 보기의 애플리케이션 수명 주기 관리
description: Azure Container Apps 미리 보기의 전체 애플리케이션 수명 주기에 대해 알아보기
services: container-apps
author: craigshoemaker
ms.service: container-apps
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: f731ceae2059c5b0e48f1f1428b60a988ee12ce0
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132027188"
---
# <a name="application-lifecycle-management-in-azure-container-apps-preview"></a>Azure Container Apps 미리 보기의 애플리케이션 수명 주기 관리

Azure Container Apps 애플리케이션 수명 주기는 수정 버전을 중심으로 [합니다.](revisions.md)

컨테이너 앱을 배포하면 첫 번째 수정 버전이 자동으로 만들어집니다. [컨테이너가](containers.md) 변경되거나 구성 섹션이 조정되면 더 많은 수정 버전이 [만들어집니다.](revisions.md) `template`

컨테이너 앱은 배포, 업데이트 및 비활성화의 세 단계를 통해 진행됩니다.

## <a name="deployment"></a>배포

컨테이너 앱이 배포되면 첫 번째 수정 버전이 자동으로 만들어집니다.

:::image type="content" source="media/application-lifecycle-management/azure-container-apps-lifecycle-deployment.png" alt-text="Azure Container Apps: 배포 단계":::

## <a name="update"></a>업데이트

컨테이너 앱이 [수정 범위 변경으로](revisions.md#revision-scope-changes)업데이트되면 새 수정 버전이 만들어집니다. 새 이전 수정 [버전을 자동으로 비활성화할지 또는 사용 가능한 상태로 유지하도록 허용할지](revisions.md)선택할 수 있습니다.

:::image type="content" source="media/application-lifecycle-management/azure-container-apps-lifecycle-update.png" alt-text="Azure Container Apps: 업데이트 단계":::

## <a name="deactivate"></a>비활성화

수정 버전이 더 이상 필요하지 않으면 나중에 다시 활성화하는 옵션을 사용하여 수정 버전을 비활성화할 수 있습니다. 비활성화하는 동안 컨테이너가 [종료됩니다.](#shutdown)

:::image type="content" source="media/application-lifecycle-management/azure-container-apps-lifecycle-deactivate.png" alt-text="Azure Container Apps: 비활성화 단계":::

## <a name="shutdown"></a>종료

컨테이너는 다음과 같은 상황에서 종료됩니다.

- 컨테이너 앱이 규모 확장됨에 따라
- 컨테이너 앱이 삭제될 때
- 수정 버전이 비활성화될 때

종료가 시작되면 컨테이너 호스트는 [컨테이너에 SIGTERM 메시지를](https://wikipedia.org/wiki/Signal_(IPC)) 보냅니다. 컨테이너에 구현된 코드는 종료를 처리하기 위해 이 운영 체제 수준 메시지에 응답할 수 있습니다.

애플리케이션이 메시지에 응답하지 않으면 `SIGTERM` [SIGKILL은](https://wikipedia.org/wiki/Signal_(IPC)) 컨테이너를 종료합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [수정 버전 작업](revisions.md)
