---
title: IoT Central 조직 관리 | Microsoft Docs
description: 이 문서에서는 조직 계층 구조를 만들고 관리하여 IoT Central 조직의 디바이스에 액세스할 수 있는 사용자를 제어하는 방법을 설명합니다. 조직을 사용하여 다중 테넌트 IoT Central 애플리케이션을 만듭니다.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/20/2021
ms.topic: how-to
ms.openlocfilehash: e15e2b095cd6b6876728ff773a7b4617e325d77d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128656762"
---
# <a name="manage-iot-central-organizations"></a>IoT Central 조직 관리

조직에서는 IoT Central 애플리케이션에서 디바이스를 볼 수 있는 사용자를 관리하는 데 사용하는 계층을 정의할 수 있습니다. 사용자의 역할은 표시되는 디바이스에 대한 권한과 액세스할 수 있는 환경을 결정합니다.

조직은 계층적입니다.

:::image type="content" source="media/howto-create-organization/organizations-hierarchy.png" alt-text="조직의 샘플 계층 구조를 보여 주는 다이어그램" border="false":::

첫 번째 조직 계층 구조를 만들 때 애플리케이션의 이름이 루트에 있음을 알 수 있습니다. 추가하는 각 조직은 자식 또는 리프 조직이 됩니다. 조직 계층 구조는 상속을 사용합니다. 예를 들어 이전 다이어그램에 표시된 계층 구조에서 다음을 수행합니다.

- *Adatum Solar* 사용자는 *미국,* *캐나다,* *유럽* 및 라틴 아메리카 디바이스에 액세스할 *수* 있습니다.
- *Contoso* 사용자는 계층 구조의 모든 조직에 있는 모든 디바이스에 액세스할 수 있습니다.

형제 조직의 사용자는 서로의 다른 디바이스를 볼 수 없습니다.

사용자에게 애플리케이션에 대한 액세스 권한을 부여할 때 액세스 권한을 할당하는 계층 구조가 높을수록 사용자가 더 많이 보고 관리할 수 있습니다. 조직은 사용자가 볼 수 있는 디바이스를 제어합니다. 역할은 디바이스 만들기, 읽기 및 삭제와 같이 사용자가 수행할 수 있는 작업을 정의합니다.

다음 스크린샷은 IoT Central 조직 계층 정의를 보여줍니다.

:::image type="content" source="media/howto-create-organization/organizations-definition.png" alt-text="조직 계층 정의의 스크린샷.":::

## <a name="create-a-hierarchy"></a>계층 만들기

조직 사용을 시작하려면 조직 계층 구조를 정의해야 합니다. 계층 구조의 각 조직은 디바이스를 배치하고, 대시보드 및 디바이스 그룹을 저장하고, 사용자를 초대하는 논리적 컨테이너 역할을 합니다. 조직을 만들려면 IoT Central 애플리케이션의 **관리** 섹션으로 이동하여 **조직** 탭을 선택하고 + **새로** 만들기를 선택하거나 기존 조직의 상황에 맞는 메뉴를 사용합니다. 한 번에 하나 이상의 조직을 만들려면 **+ 다른 조직 추가를** 선택합니다.

:::image type="content" source="media/howto-create-organization/create-organizations-hierarchy.png" alt-text="조직 계층 구조를 만들기 위한 옵션을 보여 주는 스크린샷.":::

> [!TIP]
> 조직의 초기 설정은 **앱 관리자** 역할의 멤버가 수행해야 합니다.

새 부모에 조직을 다시 할당하려면 **편집을** 선택하고 새 부모를 선택합니다.

조직을 삭제하려면 대시보드, 디바이스, 사용자, 디바이스 그룹 및 작업과 같은 관련 항목을 삭제하거나 다른 조직으로 이동해야 합니다.

> [!TIP]
> REST API 사용하여 [조직을 만들고 관리할](/rest/api/iotcentral/1.1-previewdataplane/organizations)수도 있습니다.

## <a name="assign-devices"></a>디바이스에 할당

조직 계층 구조를 정의한 후 조직에 디바이스를 할당합니다. 각 디바이스는 단일 조직에만 속하므로 계층 구조에서 적절한 조직을 선택합니다.

애플리케이션에서 새 디바이스를 만들 때 계층 구조의 조직에 할당합니다.

:::image type="content" source="media/howto-create-organization/assign-device.png" alt-text="조직에 디바이스를 할당하는 방법을 보여 주는 스크린샷":::

기존 디바이스를 조직에 할당하거나 다시 할당하려면 디바이스 목록에서 디바이스를 선택한 다음, **조직** 을 선택합니다.

:::image type="content" source="media/howto-create-organization/change-device-organization.png" alt-text="디바이스가 연결된 조직을 변경하는 방법을 보여 주는 스크린샷.":::

> [!TIP]
> 디바이스 목록에서 디바이스가 속한 조직을 확인할 수 있습니다. 디바이스 목록의 필터 도구를 사용하여 특정 조직의 디바이스를 표시합니다.

디바이스를 다른 조직에 다시 할당하면 디바이스의 데이터는 데이터 수집 시간에 따라 조직과 함께 유지됩니다. 예:

- 디바이스는 1일에서 7일까지 **Contoso/Customer1** 조직의 일부이며 8일에 **Contoso/Customer4** 조직으로 이동되었습니다.
- 9일째에 **Contoso/Customer4** 사용자는 8일과 9일의 데이터를 볼 수 있습니다.
- 9일째에 **Contoso/Customer1** 사용자는 1일에서 7일까지의 데이터를 볼 수 있습니다.

### <a name="device-first-registration"></a>디바이스 우선 등록

디바이스는 먼저 디바이스 목록에 추가하지 않고도 IoT Central 애플리케이션에 자체 등록할 수 있습니다. 이 경우 IoT Central 계층의 루트 조직에 디바이스를 추가합니다. 그런 다음 디바이스를 다른 조직에 다시 할당할 수 있습니다.

대신 CSV 가져오기 기능을 사용하여 디바이스를 애플리케이션에 대량 등록하고 조직에 할당할 수 있습니다. 자세한 내용은 [디바이스 가져오기를 참조하세요.](howto-manage-devices.md#import-devices)

### <a name="gateways"></a>게이트웨이

조직에 게이트웨이 및 리프 디바이스를 할당합니다. 게이트웨이 및 연결된 리프 디바이스를 동일한 조직에 할당할 필요가 없습니다. 다른 조직에 할당하는 경우 사용자는 게이트웨이를 볼 수 있지만 리프 디바이스 또는 리프 디바이스는 볼 수 없지만 게이트웨이는 볼 수 없습니다.

## <a name="roles"></a>역할

애플리케이션에서 첫 번째 조직을 만들 때 IoT Central 조직 **관리자, 조직** **운영자** 및 조직 **뷰어의** 세 가지 새 역할을 애플리케이션에 추가합니다. 조직 사용자는 가격 책정 계획, 브랜딩 및 색, API 토큰 및 애플리케이션 전체 등록 그룹 정보와 같은 특정 애플리케이션 전체 기능에 액세스할 수 없기 때문에 이러한 역할이 필요합니다.

:::image type="content" source="media/howto-create-organization/organization-roles.png" alt-text="세 가지 조직 역할을 보여 주는 스크린샷.":::

애플리케이션의 조직에 사용자를 초대할 때 이러한 역할을 사용할 수 있습니다.

### <a name="custom-organization-roles"></a>사용자 지정 조직 역할

조직 사용자에 대한 사용자 지정 역할을 만들려면 새 역할을 만들고 **조직** 역할 유형을 선택합니다.

:::image type="content" source="media/howto-create-organization/custom-organization-role.png" alt-text="사용자 지정 조직 역할을 만드는 방법을 보여 주는 스크린샷":::

그런 다음 역할에 대한 사용 권한을 선택합니다.

:::image type="content" source="media/howto-create-organization/organization-role-permissions.png" alt-text="조직 역할에 대한 사용자 지정 권한을 선택하는 방법을 보여 주는 스크린샷":::

## <a name="invite-users"></a>사용자 초대

조직 계층 구조를 만들고 조직에 디바이스를 할당한 후에는 사용자를 애플리케이션에 초대하고 조직에 할당합니다.

사용자를 초대하려면 관리 **> 사용자** 로 이동합니다. 이메일 주소, 할당된 조직 및 사용자가 구성원인 역할 또는 역할을 입력합니다. 선택한 조직은 사용 가능한 역할 목록을 필터링하여 사용자를 유효한 역할에 할당하도록 합니다.

:::image type="content" source="media/howto-create-organization/assign-user-organization.png" alt-text="조직 및 역할에 사용자를 할당하는 방법을 보여 주는 스크린샷":::

여러 조직에 동일한 사용자를 할당할 수 있습니다. 사용자는 할당된 각 조직에서 다른 역할을 가질 수 있습니다.

| 이름 | 역할 | 조직 |
| ---- | ---- | ------------ |
| user1@contoso.com | 조직 관리자 | Contoso Inc/Lamna Health |
| user1@contoso.com | 조직 뷰어 | Contoso Inc/Adatum Solar |

## <a name="use-organizations"></a>조직 사용

조직 계층 구조를 만든 후에는 다음과 같은 애플리케이션 영역에서 조직을 사용할 수 있습니다.

- [조직의 디바이스에](howto-manage-dashboards.md) 대한 정보를 사용자에게 표시하는 조직 대시보드입니다.
- 특정 조직의 [디바이스에](tutorial-use-device-groups.md) 대한 디바이스 그룹입니다.
- 특정 조직의 디바이스에 대한 [분석.](howto-create-analytics.md)
- 특정 조직의 디바이스를 대량으로 관리하는 [작업입니다.](howto-manage-devices-in-bulk.md#create-and-run-a-job)

## <a name="default-organization"></a>기본 조직

애플리케이션에서 사용할 기본 조직으로 조직을 설정할 수 있습니다. 기본 조직은 IoT Central 애플리케이션에 새 사용자를 추가하는 경우와 같이 조직을 선택할 때마다 기본 옵션이 됩니다.

기본 조직을 설정하려면 상단 메뉴 모음에서 **설정** 선택합니다.

:::image type="content" source="media/howto-create-organization/set-default-organization.png" alt-text="기본 조직을 설정하는 방법을 보여 주는 스크린샷":::

> [!TIP]
> 이는 본인에게만 적용되는 개인 기본 설정입니다.

## <a name="add-organizations-to-an-existing-application"></a>기존 애플리케이션에 조직 추가

조직 계층 구조를 추가하기 전에 애플리케이션에 디바이스, 사용자 및 대시보드, 디바이스 그룹 및 작업과 같은 환경이 포함될 수 있습니다.

조직 추가를 시작하면 모든 기존 디바이스, 사용자 및 환경이 애플리케이션의 루트 조직과 연결된 상태로 유지됩니다.

- 새 조직에 디바이스를 다시 할당할 수 있습니다.
- 사용자를 새 조직에 할당하고 루트에서 할당을 취소할 수 있습니다.
- 대시보드, 디바이스 그룹 및 작업과 같은 환경을 다시 만들고 계층 구조의 조직과 연결할 수 있습니다.

## <a name="limits"></a>제한

조직에 적용되는 제한은 다음과 같습니다.

- 계층은 5개 이하의 깊이 수준일 수 있습니다.
- 총 조직 수는 200을 초과할 수 없습니다. 계층의 각 노드는 조직으로 계산됩니다.

## <a name="next-steps"></a>다음 단계

이제 Azure IoT Central 조직을 관리하는 방법을 배웠으므로 제안된 다음 단계는 [데이터 내보내기를 사용하여 IoT 데이터를 클라우드 대상으로 내보내는](howto-export-data.md)방법을 알아보는 것입니다.
