---
title: IoT Central 애플리케이션 만들기 | Microsoft Docs
description: 이 문서에서는 Azure IoT Central 사이트, Azure Portal 및 명령줄 환경 등에서 IoT Central 애플리케이션을 만드는 옵션을 설명합니다.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 05/11/2021
ms.topic: how-to
ms.openlocfilehash: c9f9dec23209d8bc401313a7213239dff52a1023
ms.sourcegitcommit: b5508e1b38758472cecdd876a2118aedf8089fec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2021
ms.locfileid: "113586206"
---
# <a name="create-an-iot-central-application"></a>IoT Central 애플리케이션 만들기

IoT Central 애플리케이션을 만드는 방법은 여러 가지입니다. 수동 방식을 선호하는 경우에는 GUI 기반 메서드 중 하나를 사용하고, 프로세스를 자동화하려는 경우에는 CLI 또는 프로그래밍 방식 중 하나를 사용할 수 있습니다.

어떤 방식을 선택하든 구성 옵션은 동일하며 프로세스를 완료하는 데 일반적으로 1분 미만이 걸립니다.

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

## <a name="options"></a>옵션

이 섹션에서는 IoT Central 애플리케이션을 만들 때 사용할 수 있는 옵션에 대해 설명합니다. 선택한 방법에 따라 양식에 옵션을 제공하거나 명령줄 매개변수로 제공해야 할 수도 있습니다.

### <a name="pricing-plans"></a>가격 책정 계획

체험 플랜을 사용하면 IoT Central 애플리케이션을 만들어 7일 동안 사용할 수 있습니다. 체험 플랜은:

- Azure 구독이 필요하지 않습니다.
- [Azure IoT Central](https://aka.ms/iotcentral) 사이트에서만 만들고 관리할 수 있습니다.
- 최대 5개의 디바이스를 연결할 수 있습니다.
- 애플리케이션을 유지하려는 경우 표준 플랜으로 업그레이드할 수 있습니다.

*표준* 플랜은:

- Azure 구독이 필요합니다. Azure 구독에서 **기여자** 이상의 액세스 권한이 있어야 합니다. 구독을 직접 만든 경우 자동으로 충분한 액세스 권한이 있는 관리자가 됩니다. 자세한 내용은 [Azure 역할 기반 액세스 제어란?](../../role-based-access-control/overview.md)을 참조하세요.
- 사용 가능한 모든 방법으로 IoT Central 애플리케이션을 만들고 관리할 수 있습니다.
- 디바이스를 필요한 수 만큼 연결할 수 있습니다. 디바이스별로 요금이 청구됩니다. 자세히 알아보려면 [Azure IoT Central 가격 책정](https://azure.microsoft.com/pricing/details/iot-central/)을 참조하세요.
- 체험 플랜으로 다운그레이드할 수는 없지만 다른 표준 플랜으로 업그레이드하거나 다운그레이드할 수 있습니다.

다음 표에는 세 가지 표준 플랜 간의 차이점이 요약되어 있습니다.

| 플랜 이름 | 체험 디바이스 | 메시지/월 | 사용 사례 |
| --------- | ------------ | -------------- | -------- |
| S0        | 2            | 400            | 하루에 몇 개의 메시지 |
| S1        | 2            | 5,000          | 시간당 몇 개의 메시지 |
| S2        | 2            | 30,000         | 몇 분 마다 여러 개의 메시지 |

### <a name="application-name"></a>애플리케이션 이름

선택한 애플리케이션 이름은 IoT Central 애플리케이션의 모든 페이지에 있는 제목 표시줄에 표시됩니다. 또한 [Azure IoT Central](https://aka.ms/iotcentral) 사이트의 **내 앱** 페이지에 있는 애플리케이션 타일에도 나타납니다.

선택한 하위 도메인은 애플리케이션을 고유하게 식별합니다. 하위 도메인은 애플리케이션에 액세스하는 데 사용하는 URL의 일부입니다. IoT Central 애플리케이션의 URL은 `https://yoursubdomain.azureiotcentral.com`과 같은 모양입니다.

### <a name="application-template-id"></a>애플리케이션 템플릿 ID

선택한 애플리케이션 템플릿에 따라 대시보드 및 디바이스 템플릿과 같은 애플리케이션의 초기 콘텐츠가 결정됩니다. 사용자 지정 애플리케이션의 템플릿 ID는 `iotc-pnp-preview`를 템플릿 ID로 사용합니다.

사용자 지정 및 업계 중심의 애플리케이션 템플릿에 대한 자세한 내용은 [애플리케이션 템플릿이란?](concepts-app-templates.md)을 참조하세요.

### <a name="billing-information"></a>대금 청구 정보

표준 플랜 중 하나를 선택하면 다음과 같은 청구 정보를 제공해야 합니다.

- 사용 중인 Azure 구독
- 사용 중인 구독이 포함된 디렉터리
- 애플리케이션을 호스하는 위치. IoT Central은 Azure 지역을 미국, 유럽, 아시아 태평양, 오스트레일리아, 영국 또는 일본 위치로 사용합니다.

## <a name="azure-iot-central-site"></a>Azure IoT Central 사이트

IoT Central 애플리케이션을 만들기 가장 쉬운 방법은 [Azure IoT Central](https://aka.ms/iotcentral) 사이트에 있습니다.

[빌드](https://apps.azureiotcentral.com/build)에서 사용할 애플리케이션 템플릿을 선택할 수 있습니다.

:::image type="content" source="media/howto-create-iot-central-application/choose-template.png" alt-text="애플리케이션 템플릿을 선택할 수 있는 빌드 페이지 스크린샷":::

**앱 만들기** 를 선택하면 템플릿에서 애플리케이션을 만드는 데 필요한 정보를 제공할 수 있습니다.

:::image type="content" source="media/howto-create-iot-central-application/create-application.png" alt-text="IoT Central의 애플리케이션 만들기 페이지를 보여주는 스크린샷":::

**내 앱** 페이지에는 액세스 권한이 있는 모든 IoT Central 애플리케이션이 나열됩니다. 이 목록에는 직접 만든 애플리케이션과 액세스 권한이 부여된 애플리케이션이 포함됩니다.

> [!TIP]
> Azure IoT Central 사이트에서 표준 가격 책정 플랜을 사용하여 만드는 모든 애플리케이션은 구독에서 **IOTC** 리소스 그룹을 사용합니다. 다음 섹션에 설명된 방식을 통해 사용할 리소스 그룹을 선택할 수 있습니다.

## <a name="copy-an-application"></a>애플리케이션 복사

모든 디바이스 인스턴스, 디바이스 데이터 기록 및 사용자 데이터를 제외한 모든 애플리케이션의 복사본을 만들 수 있습니다. 복사본은 요금이 청구될 표준 가격 책정 플랜을 사용합니다. 애플리케이션을 복사하여 무료 가격 책정 플랜을 사용하는 애플리케이션은 만들 수 없습니다.

**복사** 를 선택합니다. 대화 상자에서 새 애플리케이션에 대한 세부 정보를 입력합니다. 그런 다음 **복사** 를 선택하여 계속할 것인지 확인합니다. 이 양식의 필드에 대해 자세히 알아보려면 [애플리케이션 만들기](howto-create-iot-central-application.md)를 참조하세요.

:::image type="content" source="media/howto-create-iot-central-application/app-copy-1.png" alt-text="애플리케이션 설정 페이지를 보여 주는 스크린샷.":::

:::image type="content" source="media/howto-create-iot-central-application/app-copy-2.png" alt-text="애플리케이션 복사 설정 페이지를 보여 주는 스크린샷.":::

앱 복사 작업이 성공하면 링크를 사용하여 새 애플리케이션으로 이동할 수 있습니다.

애플리케이션을 복사하면 규칙 및 메일 작업의 정의도 복사됩니다. Flow 및 Logic Apps와 같은 일부 작업은 규칙 ID를 통해 특정 규칙에 연결됩니다. 다른 애플리케이션에 규칙이 복사되면 고유의 규칙 ID를 가져옵니다. 이 경우 사용자는 새 작업을 만든 다음 새 규칙을 연결해야 합니다. 일반적으로 새 앱에서 규칙 및 작업이 최신 상태인지 확인하는 것이 좋습니다.

> [!WARNING]
> 대시보드에 특정 디바이스에 대한 정보를 표시하는 타일이 포함된 경우 이러한 타일은 새 애플리케이션에 **요청된 리소스를 찾을 수 없습니다** 를 표시합니다. 새 애플리케이션의 디바이스에 대한 정보를 표시하도록 이러한 타일을 다시 구성해야 합니다.

## <a name="create-and-use-a-custom-application-template"></a>사용자 지정 애플리케이션 템플릿 만들기 및 사용

Azure IoT Central 애플리케이션을 만들 때 기본 제공 샘플 템플릿을 선택할 수 있습니다. 또한 기존 IoT Central 애플리케이션에서 고유의 애플리케이션 템플릿을 만들 수도 있습니다. 그런 다음 새 애플리케이션을 만들 때 고유의 애플리케이션 템플릿을 사용할 수 있습니다.

애플리케이션 템플릿을 만들 때 기존 애플리케이션에 다음 항목이 포함됩니다.

- 대시보드 레이아웃과 정의한 모든 타일을 포함하는 기본 애플리케이션 대시보드입니다.
- 측정, 설정, 속성, 명령, 대시보드를 포함하는 디바이스 템플릿입니다.
- 규칙. 모든 규칙 정의가 포함됩니다. 그러나 메일 작업을 제외한 작업은 포함되지 않습니다.
- 쿼리를 포함한 디바이스 그룹

> [!WARNING]
> 대시보드에 특정 디바이스에 대한 정보를 표시하는 타일이 포함된 경우 이러한 타일은 새 애플리케이션에 **요청된 리소스를 찾을 수 없습니다** 를 표시합니다. 새 애플리케이션의 디바이스에 대한 정보를 표시하도록 이러한 타일을 다시 구성해야 합니다.

애플리케이션 템플릿을 만들 때 다음 항목은 포함되지 않습니다.

- 디바이스
- 사용자
- 연속 데이터 내보내기 정의

이러한 항목을 애플리케이션 템플릿에서 만든 모든 애플리케이션에 수동으로 추가합니다.

기존 IoT Central 애플리케이션에서 애플리케이션 템플릿을 만들려면:

1. 애플리케이션의 **관리** 섹션으로 이동합니다.
1. **애플리케이션 템플릿 내보내기** 를 선택합니다.
1. **애플리케이션 템플릿 내보내기** 페이지에서 템플릿에 대한 이름과 설명을 입력합니다.
1. 애플리케이션 템플릿을 만들려면 **내보내기** 단추를 선택합니다. 이제 다른 사용자가 템플릿에서 새 애플리케이션을 만들 수 있도록 **공유 가능 링크** 를 복사할 수 있습니다.

:::image type="content" source="media/howto-create-iot-central-application/create-template.png" alt-text="애플리케이션 템플릿 만들기를 보여 주는 스크린샷.":::

:::image type="content" source="media/howto-create-iot-central-application/create-template-2.png" alt-text="애플리케이션 템플릿 내보내기를 보여 주는 스크린샷.":::

### <a name="use-an-application-template"></a>애플리케이션 템플릿 사용

애플리케이션 템플릿을 사용하여 새 IoT Central 애플리케이션을 만들려면 이전에 만든 **공유 가능 링크** 가 필요합니다. **공유 가능 링크** 를 브라우저의 주소 표시줄에 붙여넣습니다. 선택한 사용자 지정 애플리케이션 템플릿으로 **애플리케이션 만들기** 페이지를 표시합니다.

:::image type="content" source="media/howto-create-iot-central-application/create-app.png" alt-text="템플릿에서 애플리케이션 만들기를 보여 주는 스크린샷.":::

가격 책정 플랜을 선택하고 양식의 다른 필드를 입력합니다. 그런 다음 **만들기** 를 선택하여 애플리케이션 템플릿에서 새 IoT Central 애플리케이션을 만듭니다.

### <a name="manage-application-templates"></a>애플리케이션 템플릿 관리

**애플리케이션 템플릿 내보내기** 페이지에서 애플리케이션 템플릿을 삭제하거나 업데이트할 수 있습니다.

애플리케이션 템플릿을 삭제하면 더 이상 이전에 만든 공유 가능 링크를 사용하여 새 애플리케이션을 만들 수 없습니다.

애플리케이션 템플릿을 업데이트하려면 **애플리케이션 템플릿 내보내기** 페이지에서 템플릿 이름이나 설명을 변경합니다. 그런 다음 **내보내기** 단추를 다시 선택합니다. 이 작업은 새 **공유 가능 링크** 를 생성하고 이전의 **공유 가능 링크** URL을 무효화합니다.

## <a name="other-approaches"></a>다른 방법

다음 방식을 사용하여 IoT Central 애플리케이션을 만들 수도 있습니다.

- [Azure Portal에서 IoT Central 애플리케이션 만들기](howto-manage-iot-central-from-portal.md#create-iot-central-applications)
- [명령줄을 사용하여 IoT Central 애플리케이션 만들기](howto-manage-iot-central-from-cli.md#create-an-application)
- [프로그래밍 방식으로 IoT Central 애플리케이션 만들기](/samples/azure-samples/azure-iot-central-arm-sdk-samples/azure-iot-central-arm-sdk-samples/)

## <a name="next-steps"></a>다음 단계

Azure CLI에서 Azure IoT Central 애플리케이션을 관리하는 방법을 알아보았으면 다음 단계를 진행하는 것이 좋습니다.

> [!div class="nextstepaction"]
> [애플리케이션 관리](howto-administer.md)
