---
title: Azure Functions 배포 슬롯
description: Azure Functions를 사용하여 배포 슬롯을 만들고 사용하는 방법을 알아봅니다.
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: cshoe
ms.openlocfilehash: f8ac0c985018d8d4c3858a9ff47ac2b188539161
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130003461"
---
# <a name="azure-functions-deployment-slots"></a>Azure Functions 배포 슬롯

Azure Functions 배포 슬롯을 사용하면 함수 앱이 “슬롯”이라고 부르는 여러 인스턴스를 실행할 수 있습니다. 슬롯은 공개적으로 사용 가능한 엔드포인트를 통해 노출되는 서로 다른 환경입니다. 하나의 앱 인스턴스가 항상 프로덕션 슬롯에 매핑되고, 주문형 슬롯에 할당된 인스턴스를 교환할 수 있습니다. App Service 요금제에 따라 실행되는 함수 앱은 여러 슬롯을 포함할 수 있으며, 사용 요금제에 따라 하나의 슬롯만 허용됩니다.

다음은 슬롯 교환이 함수에 미치는 영향을 보여줍니다.

- 트래픽 리디렉션이 원활하게 수행되고, 교환으로 인해 삭제되는 요청이 없습니다.
- 함수가 교환 중 실행되는 경우 실행이 계속되고 다음 트리거가 교환된 앱 인스턴스에 라우팅됩니다.

## <a name="why-use-slots"></a>슬롯을 사용하는 이윤

배포 슬롯을 사용하면 많은 이점이 있습니다. 다음 시나리오에서는 슬롯의 일반적인 용도를 설명합니다.

- **여러 목적에 맞는 서로 다른 환경**: 여러 슬롯을 사용함으로써 프로덕션 또는 준비 슬롯으로 교환하기 전 앱 인스턴스를 차별화할 수 있습니다.
- **사전 준비**: 프로덕션에 직접 배포하는 대신 슬롯에 배포함으로써 라이브 전환 전에 앱을 준비할 수 있습니다. 또한 슬롯을 사용하면 HTTP로 트리거되는 워크로드의 지연 시간이 줄어듭니다. 배포 전 인스턴스가 준비되어, 새로 배포되는 함수의 콜드 시작을 줄여줍니다.
- **쉬운 폴백**: 프로덕션으로 교환한 후에는 이전에 준비된 앱을 포함하는 슬롯에 이제 이전 프로덕션 앱이 포함됩니다. 프로덕션 슬롯에 교환된 변경 사항이 예상한 것과 다르면 즉시 교환을 되돌려서 “마지막으로 알려진 정상 인스턴스”를 다시 가져올 수 있습니다.

## <a name="swap-operations"></a>교환 작업

교환 중 한 슬롯은 원본으로, 다른 슬롯은 대상으로 간주됩니다. 원본 슬롯에는 대상 슬롯에 적용된 애플리케이션의 인스턴스가 포함됩니다. 다음 단계에서는 교환 중 대상 슬롯에 가동 중지 시간이 발생하지 않도록 보장합니다.

1. **설정 적용:** 대상 슬롯의 설정은 원본 슬롯의 모든 인스턴스에 적용됩니다. 예를 들어 프로덕션 설정이 준비 인스턴스에 적용됩니다. 적용된 설정에는 다음 범주가 포함됩니다.
    - [슬롯 특정](#manage-settings) 앱 설정 및 연결 문자열(해당하는 경우)
    - [지속적인 배포](../app-service/deploy-continuous-deployment.md) 설정(설정된 경우)
    - [App Service 인증](../app-service/overview-authentication-authorization.md) 설정(설정된 경우)

1. **다시 시작 및 가용성 대기:** 교환할 때 원본 슬롯의 모든 인스턴스의 다시 시작이 완료되고 요청에 사용할 수 있을 때까지 기다립니다. 인스턴스를 다시 시작하지 못하는 경우 교환 작업은 모든 변경 내용을 원본 슬롯으로 되돌리고 작업을 중지합니다.

1. **라우팅 업데이트:** 원본 슬롯의 모든 인스턴스가 성공적으로 사전 준비되었으면 두 슬롯이 라우팅 규칙을 전환하여 교환을 완료합니다. 이 단계를 수행한 후 대상 슬롯(예: 프로덕션 슬롯)에는 이전에 원본 슬롯에서 준비된 앱이 있습니다.

1. **작업 반복:** 원본 슬롯에 이전에 대상 슬롯에 있던 사전 교환된 앱이 포함되므로, 모든 설정을 적용하고 원본 슬롯에 대해 인스턴스를 다시 시작하여 동일한 작업을 완료합니다.

다음 사항을 주의하세요.

- 교환 작업의 모든 지점에서 교환된 앱의 초기화가 원본 슬롯에서 발생합니다. 교환이 성공하거나 실패하는지에 관계없이 원본 슬롯이 준비되는 동안 대상 슬롯이 온라인 상태로 유지됩니다.

- 준비 슬롯을 프로덕션 슬롯과 교환하려면 프로덕션 슬롯이 *항상* 대상 슬롯인지 확인합니다. 이러한 방식으로 교환 작업은 프로덕션 앱에 영향을 주지 않습니다.

- *교환을 시작하기 전* 이벤트 원본 및 바인딩과 관련된 설정을 [배포 슬롯 설정](#manage-settings)으로 구성해야 합니다. 미리 “고정”으로 표시하면 이벤트 및 출력이 적절한 인스턴스로 전달됩니다.

## <a name="manage-settings"></a>설정 관리

일부 구성 설정은 슬롯에 따라 달라집니다. 다음 목록은 슬롯을 교환할 때 어떤 설정이 변경되고, 어떤 설정이 동일하게 유지되는지 자세히 보여줍니다.

**슬롯별 설정**:

* 게시 엔드포인트
* 사용자 지정 도메인 이름
* 공용이 아닌 인증서 및 TLS/SSL 설정
* 크기 조정 설정
* WebJob 스케줄러
* IP 제한
* Always On
* 진단 설정
* CORS(원본 간 리소스 공유)

**슬롯에 영향을 받지 않는 설정**:

* 프레임워크 버전, 32/64비트, 웹 소켓과 같은 일반 설정
* 앱 설정(슬롯에 맞도록 구성할 수 있음)
* 연결 설정(슬롯에 맞도록 구성할 수 있음)
* 처리기 매핑
* 공용 인증서
* WebJob 콘텐츠
* 하이브리드 연결 *
* 가상 네트워크 통합 *
* 서비스 엔드포인트 *
* Azure Content Delivery Network *

별표(*)로 표시된 기능은 교환되지 않도록 계획됩니다. 

> [!NOTE]
> 교환되지 않는 설정에 적용되는 특정 앱 설정 역시 교환되지 않습니다. 예를 들어 진단 설정은 교환되지 않으므로 `WEBSITE_HTTPLOGGING_RETENTION_DAYS` 및 `DIAGNOSTICS_AZUREBLOBRETENTIONDAYS`와 같은 관련 앱 설정 역시 슬롯 설정에 표시되지 않더라도 교환되지 않습니다.
>

### <a name="create-a-deployment-setting"></a>배포 설정 만들기

설정을 배포 설정으로 표시하여, “고정”으로 만들 수 있습니다. 고정 설정은 앱 인스턴스와 교환되지 않습니다.

한 슬롯에서 배포 설정을 만드는 경우 교환에 포함된 다른 슬롯에서 고유 값을 사용하여 동일한 설정을 만들어야 합니다. 이렇게 하면 설정 값이 변경되지 않고, 설정 이름이 슬롯 간에 일관되게 유지됩니다. 이러한 이름 일관성에 따라 코드가 한 슬롯에 정의되어 있지만 다른 슬롯에 정의되지 않은 설정에 액세스하도록 시도하지 않습니다.

다음 단계에 따라 배포 설정을 만듭니다.

1. 함수 앱에서 **배포 슬롯** 으로 이동한 후 슬롯 이름을 선택합니다.

    :::image type="content" source="./media/functions-deployment-slots/functions-navigate-slots.png" alt-text="Azure Portal에서 슬롯을 찾습니다." border="true":::

1. **구성** 을 선택한 후 현재 슬롯에 고정시키려는 설정 이름을 선택합니다.

    :::image type="content" source="./media/functions-deployment-slots/functions-configure-deployment-slot.png" alt-text="Azure Portal에서 슬롯에 대해 애플리케이션 설정을 구성합니다." border="true":::

1. **배포 슬롯 설정** 을 선택한 후 **확인** 을 선택합니다.

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slot-setting.png" alt-text="배포 슬롯 설정을 구성합니다." border="true":::

1. 설정 섹션이 사라지면 **저장** 을 선택하여 변경 내용을 유지합니다.

    :::image type="content" source="./media/functions-deployment-slots/functions-save-deployment-slot-setting.png" alt-text="배포 슬롯 설정을 저장합니다." border="true":::

## <a name="deployment"></a>배포

슬롯을 만들 때 슬롯은 비어 있습니다. [지원되는 배포 기술](./functions-deployment-technologies.md)을 사용하여 슬롯에 애플리케이션을 배포할 수 있습니다.

## <a name="scaling"></a>확장

모든 슬롯은 프로덕션 슬롯과 동일한 작업자 수로 크기가 조정됩니다.

- 사용 요금제의 경우 슬롯은 함수 앱에 따라 크기가 조정됩니다.
- App Service 요금제의 경우에는 앱이 고정된 수의 작업자로 크기가 조정됩니다. 슬롯이 앱 요금제와 동일한 작업자 수로 실행됩니다.

## <a name="add-a-slot"></a>슬롯 추가

[CLI](/cli/azure/functionapp/deployment/slot#az_functionapp_deployment_slot_create) 또는 포털을 통해 슬롯을 추가할 수 있습니다. 다음 단계에서는 포털에서 새 슬롯을 만드는 방법을 보여줍니다.

1. 함수 앱으로 이동합니다.

1. **배포 슬롯** 을 선택한 후 **+ 슬롯 추가** 를 선택합니다.

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slots-add.png" alt-text="Azure Functions 배포 슬롯을 추가합니다." border="true":::

1. 슬롯의 이름을 입력하고 **추가** 를 선택합니다.

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slots-add-name.png" alt-text="Azure Functions 배포 슬롯의 이름을 지정합니다." border="true":::

## <a name="swap-slots"></a>슬롯 교환

[CLI](/cli/azure/functionapp/deployment/slot#az_functionapp_deployment_slot_swap) 또는 포털을 통해 슬롯을 교환할 수 있습니다. 다음 단계에서는 포털에서 슬롯을 교환하는 방법을 보여줍니다.

1. 함수 앱으로 이동합니다.
1. **배포 슬롯** 을 선택한 후 **교환** 을 선택합니다.

    :::image type="content" source="./media/functions-deployment-slots/functions-swap-deployment-slot.png" alt-text="‘슬롯 추가’ 작업이 선택된 ‘배포 슬롯’ 페이지를 보여주는 스크린샷입니다." border="true":::

1. 교환에 대한 구성 설정을 확인하고 **교환** 을 선택합니다.
    
    :::image type="content" source="./media/functions-deployment-slots/azure-functions-deployment-slots-swap-config.png" alt-text="배포 슬롯을 교환합니다." border="true":::

교환 작업이 실행되는 동안 잠시 시간이 걸릴 수 있습니다.

## <a name="roll-back-a-swap"></a>교환 롤백

교환으로 인해 오류가 발생하거나 단순히 교환을 “실행 취소”하려는 경우 초기 상태로 롤백할 수 있습니다. 교환되기 전 상태로 돌아가려면 교환을 다시 수행하여 교환을 반대로 합니다.

## <a name="remove-a-slot"></a>슬롯 제거

[CLI](/cli/azure/functionapp/deployment/slot#az_functionapp_deployment_slot_delete) 또는 포털을 통해 슬롯을 제거할 수 있습니다. 다음 단계에서는 포털에서 슬롯을 제거하는 방법을 보여줍니다.

1. 함수 앱에서 **배포 슬롯** 으로 이동한 후 슬롯 이름을 선택합니다.

    :::image type="content" source="./media/functions-deployment-slots/functions-navigate-slots.png" alt-text="Azure Portal에서 슬롯을 찾습니다." border="true":::

1. **삭제** 를 선택합니다.

    :::image type="content" source="./media/functions-deployment-slots/functions-delete-deployment-slot.png" alt-text="‘삭제’ 작업이 선택된 ‘개요’ 페이지를 보여주는 스크린샷입니다." border="true":::

1. 삭제하려는 배포 슬롯의 이름을 입력한 후 **삭제** 를 선택합니다.

    :::image type="content" source="./media/functions-deployment-slots/functions-delete-deployment-slot-details.png" alt-text="Azure Portal에서 배포 슬롯을 삭제합니다." border="true":::

1. 삭제 확인 창을 닫습니다.

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slot-deleted.png" alt-text="배포 슬롯 삭제 확인." border="true":::

## <a name="automate-slot-management"></a>슬롯 관리 자동화

[Azure CLI](/cli/azure/functionapp/deployment/slot)를 사용하여 슬롯에 대해 다음 작업을 자동화할 수 있습니다.

- [create](/cli/azure/functionapp/deployment/slot#az_functionapp_deployment_slot_create)
- [delete](/cli/azure/functionapp/deployment/slot#az_functionapp_deployment_slot_delete)
- [list](/cli/azure/functionapp/deployment/slot#az_functionapp_deployment_slot_list)
- [swap](/cli/azure/functionapp/deployment/slot#az_functionapp_deployment_slot_swap)
- [auto-swap](/cli/azure/functionapp/deployment/slot#az_functionapp_deployment_slot_auto_swap)

## <a name="change-app-service-plan"></a>App Service 요금제 변경

특정 App Service 요금제에 따라 실행되는 함수 앱의 경우, 슬롯의 기본 App Service 요금제를 변경할 수 있습니다.

> [!NOTE]
> 사용 요금제에서는 슬롯의 App Service 요금제를 변경할 수 없습니다.

다음 단계에 따라 슬롯의 App Service 요금제를 변경합니다.

1. 함수 앱에서 **배포 슬롯** 으로 이동한 후 슬롯 이름을 선택합니다.

    :::image type="content" source="./media/functions-deployment-slots/functions-navigate-slots.png" alt-text="Azure Portal에서 슬롯을 찾습니다." border="true":::

1. **App Service 요금제** 에서 **App Service 요금제 변경** 을 선택합니다.

1. 업그레이드하려는 요금제를 선택하거나 새 요금제를 만듭니다.

    :::image type="content" source="./media/functions-deployment-slots/azure-functions-deployment-slots-change-app-service-apply.png" alt-text="Azure Portal에서 App Service 요금제를 변경합니다." border="true":::

1. **확인** 을 선택합니다.

## <a name="limitations"></a>제한 사항

Azure Functions 배포 슬롯은 다음 제한이 있습니다.

- 앱에 사용 가능한 슬롯 수가 요금제에 따라 달라집니다. 사용 요금제는 하나의 배포 슬롯만 허용됩니다. 추가 슬롯은 App Service 요금제에 따라 실행되는 앱에서 사용할 수 있습니다.
- 슬롯을 교환하면 `AzureWebJobsSecretStorageType` 앱이 `files`와 동일하게 설정된 앱의 키가 초기화됩니다.
- 슬롯이 활성화되면 Functions 앱이 포털에서 읽기 전용 모드로 설정됩니다.

## <a name="support-levels"></a>지원 수준

배포 슬롯에는 두 가지 지원 수준이 있습니다.

- **GA(일반 공급)** : 프로덕션 용도로 완전하게 지원되고 승인됩니다.
- **미리 보기** - 아직 지원되지 않지만 향후 GA 상태로 전환될 예정입니다.

| OS/호스팅 계획           | 지원 수준     |
| ------------------------- | -------------------- |
| Windows 사용       | 일반 공급 |
| Windows 프리미엄           | 일반 공급 |
| Windows 전용         | 일반 공급 |
| Linux 사용         | 일반 공급 |
| Linux 프리미엄             | 일반 공급 |
| Linux 전용           | 일반 공급 |

## <a name="next-steps"></a>다음 단계

- [Azure Functions의 배포 기술](./functions-deployment-technologies.md)
