---
title: Azure Container Instances 상태
description: Azure Container Instances 프로비저닝 작업, 컨테이너 및 컨테이너 그룹의 상태에 대해 알아봅니다.
ms.author: nickoman
author: nickomang
ms.topic: article
ms.date: 03/25/2021
ms.openlocfilehash: 0144b6ebd82c6df4c9ec501897a4315b4554103d
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2021
ms.locfileid: "113018644"
---
# <a name="azure-container-instances-states"></a>Azure Container Instances 상태

Azure Container Instances는 여러 개의 독립적인 상태 값을 표시합니다. 이 문서에서는 이러한 값, 찾을 수 있는 위치 및 의미를 카탈로그로 표시합니다.

## <a name="where-to-find-state-values"></a>상태 값을 찾을 수 있는 위치

Azure Portal에서 상태는 다양한 위치에 표시됩니다. 모든 상태 값은 리소스의 JSON 정의를 통해 액세스할 수 있습니다. 이 값은 아래와 같이 개요 블레이드의 기본 정보 아래에서 찾을 수 있습니다.

:::image type="content" source="./media/container-state/provisioning-state.png" alt-text="Azure Portal의 개요 블레이드가 표시됩니다. 'JSON 뷰' 링크가 강조 표시됩니다.":::

상태는 Azure Portal의 다른 위치에도 표시됩니다. 다음 표에서는 상태 값을 찾을 수 있는 위치를 요약해서 보여 줍니다.

|Name|JSON 경로|Azure Portal 위치|
|-|-|-|
|컨테이너 그룹 상태|`properties.instanceView.state`|개요 블레이드의 기본 정보 아래|
|현재 컨테이너 상태|`properties.containers/initContainers[x].instanceView.currentState.state`|컨테이너 블레이드 표의 **상태** 열 아래|
|이전 컨테이너 상태|`properties.containers/initContainers[x].instanceView.previousState.state`|개요 블레이드의 기본 정보 아래에서 *JSON 보기* 를 통해|
|프로비저닝 상태|`properties.provisioningState`|개요 블레이드의 기본 정보 아래에서 *JSON 보기* 를 통해, HTTP 응답 본문|

## <a name="container-groups"></a>컨테이너 그룹

이 값은 백 엔드에서 배포된 컨테이너 그룹의 상태입니다.

:::image type="content" source="./media/container-state/container-group-state.png" alt-text="Azure Portal의 리소스에 대한 개요 블레이드가 웹 브라우저에 표시됩니다. '상태: 실행 중' 텍스트가 강조 표시됩니다.":::

- **실행 중**: 컨테이너 그룹이 실행 중이고 사용자 작업 또는 다시 시작 정책으로 인한 중지가 발생하기 전까지 계속해서 실행됩니다.

- **중지됨**: 컨테이너 그룹이 중지되었으며 사용자 작업 없이 실행되도록 예약되지 않습니다.

- **보류 중**: 컨테이너 그룹이 초기화되기를 기다리고 있습니다(init 컨테이너 실행 완료, 해당하는 경우 Azure 파일 볼륨 탑재). 사용자 작업(중지/삭제)이 발생하지 않는 한, 컨테이너는 계속해서 **실행 중** 상태로 전환하려고 합니다.

- **성공**: 컨테이너 그룹 실행이 성공적으로 완료되었습니다. *사용 안 함* 및 *실패한 경우* 다시 시작 정책에만 적용됩니다.

- **실패**: 컨테이너 그룹의 실행을 완료하지 못했습니다. *사용 안 함* 다시 시작 정책에만 적용됩니다. 이 상태는 인프라 오류(예: 잘못된 Azure 파일 공유 자격 증명) 또는 사용자 애플리케이션 오류(예: 애플리케이션이 존재하지 않는 환경 변수를 참조함)를 나타냅니다.

다음 표에서는 지정된 다시 시작 정책에 따라 컨테이너 그룹에 적용할 수 있는 상태를 보여 줍니다.

|값|안 함|실패한 경우|항상|
|--|--|--|--|
|실행 중|예|예|예|
|중지됨|예|예|예|
|보류 중|예|예|예|
|성공|예|예|아니요|
|실패|예|아니요|예|

## <a name="containers"></a>컨테이너

컨테이너에는 현재 상태와 이전 상태라는 두 가지 상태 값이 있습니다. 아래 표시된 Azure Portal에는 현재 상태만 표시됩니다. 모든 상태 값은 컨테이너 그룹의 다시 시작 정책에 관계 없이 지정된 모든 컨테이너에 적용됩니다.

> [!NOTE]
> JSON 값 `currentState` 및 `previousState`에는 Azure Portal의 다른 위치에 표시되지 않는 종료 코드 또는 이유와 같은 추가 정보가 포함되어 있습니다.

:::image type="content" source="./media/container-state/container-state.png" alt-text="Azure Portal의 컨테이너 블레이드가 표시됩니다. 테이블이 표시되고 '상태' 열 아래에 '실행 중'이 강조 표시됩니다.":::

- **실행 중**: 컨테이너가 실행되고 있습니다.

- **대기 중**: 컨테이너가 실행되기를 기다리고 있습니다. 이 상태는 init 컨테이너가 아직 실행 중이거나 충돌 루프로 인해 컨테이너가 백오프 중임을 나타냅니다.

- **종료됨**: 컨테이너가 종료 코드 값을 나타내며 종료되었습니다.

## <a name="provisioning"></a>프로비전

이 값은 컨테이너 그룹에서 마지막으로 수행된 작업의 상태입니다. 일반적으로 이 작업은 PUT(만들기)이지만 POST(시작/다시 시작/중지) 또는 DELETE(삭제)일 수도 있습니다.

> [!IMPORTANT]
> 또한 사용자는 비터미널 프로비저닝 상태에 대한 종속성을 만들지 않아야 합니다. **성공** 및 **실패** 상태에 대한 종속성은 허용됩니다.

JSON 보기 외에 [HTTP 호출의 응답 본문](/rest/api/container-instances/containergroups/createorupdate#response)에서도 프로비저닝 상태를 찾을 수 있습니다.

### <a name="create-start-and-restart-operations"></a>만들기, 시작 및 다시 시작 작업

> [!IMPORTANT]
> PUT(만들기) 작업은 비동기 작업입니다. PUT의 응답 본문에서 반환된 값은 최종 상태가 아닙니다. 컨테이너 그룹의 resourceId 또는 AsyncOperation(PUT 응답 헤더에 반환됨)에 대한 후속 GET 호출을 수행하는 것이 배포 상태를 모니터링하기 위한 권장 방법입니다.

이러한 상태는 PUT(만들기) 및 POST(시작/다시 시작) 이벤트에 적용됩니다.

- **보류 중**: 컨테이너 그룹이 노드 할당, 가상 네트워크 프로비저닝, 사용자 이미지를 끌어오기 전에 필요한 모든 항목과 같은 인프라 설정을 대기하는 중입니다.

- **만드는 중**: 인프라 설치가 완료되었습니다. 컨테이너 그룹은 이제 필요한 리소스를 불러와 수신합니다(Azure 파일 볼륨 탑재, 수신 IP 주소 가져오기 등).

- **성공**: 컨테이너 그룹이 해당 컨테이너를 실행 상태로 전환하는 데 성공했으며 필요한 모든 리소스를 받았습니다.

- **비정상**: 컨테이너 그룹이 비정상입니다. 노드가 다운된 경우와 같이 예기치 않은 상태가 발생할 경우 컨테이너 그룹을 이동하여 복구하도록 작업이 자동으로 트리거됩니다.

- **복구 중**: 비정상 상태를 복구하기 위해 컨테이너 그룹을 이동하는 중입니다.

- **실패**: 컨테이너 그룹이 **성공** 프로비저닝 상태에 도달하지 못했습니다. 여러 가지 이유(액세스할 수 없는 네트워크 프로필, 지정된 지역의 용량 부족, 사용자 할당량의 완전 소비, 30분 후에 시간 초과)로 인해 오류가 발생할 수 있습니다. 오류에 대한 자세한 내용은 JSON 보기의 `events`에서 찾을 수 있습니다.
    > [!NOTE]
    > 실패 상태라고 해서 리소스가 제거되었거나 완료 시도를 중지한 것은 아닙니다. 컨테이너 그룹 상태는 그룹의 현재 상태를 나타냅니다. **실패** 프로비저닝 상태 후에 컨테이너 그룹이 실행되지 않도록 하려면 해당 그룹을 중지하거나 삭제해야 합니다.

### <a name="stop-and-delete-operations"></a>중지 및 삭제 작업

이러한 값은 POST(중지) 및 DELETE(삭제) 이벤트에 적용됩니다.

- **성공**: 컨테이너 그룹을 중지하거나 삭제하는 작업이 성공적으로 완료되었습니다.

- **실패**: 컨테이너 그룹이 **성공** 프로비저닝 상태에 도달하지 못했습니다. 즉, 중지/삭제 이벤트가 완료되지 않았습니다. 오류에 대한 자세한 내용은 JSON 보기의 `events`에서 찾을 수 있습니다.