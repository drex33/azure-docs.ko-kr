---
title: Azure 컨테이너 앱 미리 보기의 응용 프로그램 수명 주기 관리
description: Azure Container Apps 미리 보기의 전체 응용 프로그램 수명 주기에 대해 알아보기
services: app-service
author: craigshoemaker
ms.service: app-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: 8ee2acbfeca4c5f15817b988fb1375fad014eb2a
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131577102"
---
# <a name="application-lifecycle-management-in-azure-container-apps-preview"></a>Azure 컨테이너 앱 미리 보기의 응용 프로그램 수명 주기 관리

Azure Container Apps 응용 프로그램 수명 주기는 [개정판](revisions.md)을 중심으로 합니다.

컨테이너 앱을 배포 하면 첫 번째 수정 버전이 자동으로 생성 됩니다. [컨테이너가](containers.md) 변경 될 때 [더 많은 수정 버전이 만들어지거나](revisions.md) 구성 섹션에 대 한 조정이 수행 됩니다 `template` .

컨테이너 앱은 배포, 업데이트 및 비활성화의 세 단계로 흐릅니다.

## <a name="deployment"></a>배포

컨테이너 앱이 배포 되 면 첫 번째 수정 버전이 자동으로 생성 됩니다.

:::image type="content" source="media/application-lifecycle-management/azure-container-apps-lifecycle-deployment.png" alt-text="Azure 컨테이너 앱: 배포 단계":::

## <a name="update"></a>업데이트

컨테이너 앱이 [수정 버전 범위 변경](revisions.md#revision-scope-changes)으로 업데이트 되 면 새 수정 버전이 만들어집니다. [새 오래 된 수정 버전을 자동으로 비활성화 하거나 사용 가능한 상태로 유지](revisions.md)하도록 허용할지 여부를 선택할 수 있습니다.

:::image type="content" source="media/application-lifecycle-management/azure-container-apps-lifecycle-update.png" alt-text="Azure 컨테이너 앱: 업데이트 단계":::

## <a name="deactivate"></a>비활성화

수정이 더 이상 필요 하지 않은 경우 나중에 다시 활성화 하는 옵션을 사용 하 여 수정 버전을 비활성화할 수 있습니다. 비활성화 하는 동안 컨테이너는 [종료](#shutdown)됩니다.

:::image type="content" source="media/application-lifecycle-management/azure-container-apps-lifecycle-deactivate.png" alt-text="Azure 컨테이너 앱: 비활성화 단계":::

## <a name="shutdown"></a>Shutdown

컨테이너는 다음과 같은 경우에 종료 됩니다.

- 컨테이너 앱 크기 조정
- 컨테이너 앱을 삭제 하는 동안
- 수정이 비활성화 되 면

종료를 시작 하면 컨테이너 호스트는 컨테이너에 [SIGTERM 메시지](https://wikipedia.org/wiki/Signal_(IPC)) 를 보냅니다. 컨테이너에 구현 된 코드는이 운영 체제 수준 메시지에 응답 하 여 종료를 처리할 수 있습니다.

응용 프로그램이 메시지에 응답 하지 않으면 `SIGTERM` [SIGKILL](https://wikipedia.org/wiki/Signal_(IPC)) 는 컨테이너를 종료 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [수정 버전 작업](revisions.md)
