---
title: IoT Central 조직 관리 | Microsoft Docs
description: 이 문서에서는 조직 계층을 만들고 관리 하 여 IoT Central 조직의 장치에 액세스할 수 있는 사용자를 제어 하는 방법을 설명 합니다. 조직을 사용 하 여 다중 테 넌 트 IoT Central 응용 프로그램을 만듭니다.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/20/2021
ms.topic: how-to
ms.openlocfilehash: 488c67c421a5f61ee716d55c8921144343971fc8
ms.sourcegitcommit: 5b25f76d0fd0ffb6784a2afab808fa55b3eac07b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2021
ms.locfileid: "133519584"
---
# <a name="manage-iot-central-organizations"></a>IoT Central 조직 관리

조직에서는 사용자가 IoT Central 응용 프로그램에서 어떤 장치를 볼 수 있는지를 관리 하는 데 사용 하는 계층을 정의할 수 있습니다. 사용자의 역할은 표시 하는 장치에 대 한 권한 및 사용자가 액세스할 수 있는 환경을 결정 합니다.

조직은 계층 구조입니다.

:::image type="content" source="media/howto-create-organization/organizations-hierarchy.png" alt-text="조직의 샘플 계층 구조를 보여 주는 다이어그램" border="false":::

첫 번째 조직 계층 구조를 만들 때 응용 프로그램의 이름이 루트에 있는 것을 볼 수 있습니다. 추가 하는 각 조직은 자식 또는 리프 조직이 됩니다. 조직 계층 구조는 상속을 사용 합니다. 예를 들어 위의 다이어그램에 표시 된 계층 구조에서 다음을 수행 합니다.

- *Adatum 태양계* 사용자는 *미국*, *캐나다*, *유럽* 및 *라틴 아메리카* 장치에 액세스할 수 있습니다.
- *Contoso* 사용자는 계층 내 모든 조직의 모든 장치에 액세스할 수 있습니다.

형제 조직의 사용자는 서로 다른 장치를 볼 수 없습니다.

사용자에 게 응용 프로그램에 대 한 액세스 권한을 부여 하는 경우 계층 구조에서 더 높은 수준에서 액세스 권한을 할당 하면 더 이상 보고 관리할 수 있습니다. 조직에서 사용자가 볼 수 있는 장치를 제어 합니다. 역할은 사용자가 장치 만들기, 읽기 및 삭제와 같은 작업을 수행할 수 있는 작업을 정의 합니다.

다음 스크린샷은 IoT Central의 조직 계층 구조 정의를 보여 줍니다.

:::image type="content" source="media/howto-create-organization/organizations-definition.png" alt-text="조직 계층 정의의 스크린샷":::

## <a name="create-a-hierarchy"></a>계층 만들기

조직 사용을 시작 하려면 조직 계층 구조를 정의 해야 합니다. 계층의 각 조직은 장치를 저장 하 고, 대시보드 및 장치 그룹을 저장 하 고, 사용자를 초대 하는 논리적 컨테이너 역할을 합니다. 조직을 만들려면 IoT Central 응용 프로그램의 **관리** 섹션으로 이동 하 여 **조직** 탭을 선택 하 고 **+ 새로 만들기** 를 선택 하거나 기존 조직의 상황에 맞는 메뉴를 사용 합니다. 한 번에 하나 이상의 조직을 만들려면 **+ 다른 조직 추가** 를 선택 합니다.

:::image type="content" source="media/howto-create-organization/create-organizations-hierarchy.png" alt-text="조직 계층을 만드는 옵션을 보여 주는 스크린샷":::

> [!TIP]
> 조직의 초기 설정은 **앱 관리자** 역할의 구성원에 의해 수행 되어야 합니다.

새 부모에 조직을 재할당 하려면 **편집** 을 선택 하 고 새 부모를 선택 합니다.

조직을 삭제 하려면 대시보드, 장치, 사용자, 장치 그룹 및 작업과 같은 연결 된 항목을 삭제 하거나 다른 조직으로 이동 해야 합니다.

> [!TIP]
> REST API를 사용 하 여 [조직을 만들고 관리할](/rest/api/iotcentral/1.1-previewdataplane/organizations)수도 있습니다.

## <a name="assign-devices"></a>디바이스에 할당

조직 계층 구조를 정의한 후에는 장치를 조직에 할당 합니다. 각 장치는 단일 조직에만 속해 있으므로 계층에서 적절 한 조직을 선택 합니다.

응용 프로그램에서 새 장치를 만들 때 계층 구조의 조직에 할당 합니다.

:::image type="content" source="media/howto-create-organization/assign-device.png" alt-text="조직에 장치를 할당 하는 방법을 보여 주는 스크린샷":::

조직에 기존 장치를 할당 하거나 재할당 하려면 장치 목록에서 장치를 선택 하 고 **조직** 을 선택 합니다.

:::image type="content" source="media/howto-create-organization/change-device-organization.png" alt-text="장치와 연결 된 조직을 변경 하는 방법을 보여 주는 스크린샷":::

> [!TIP]
> 장치 목록에서 장치가 속한 조직을 확인할 수 있습니다. 장치 목록의 필터 도구를 사용 하 여 특정 조직에 장치를 표시 합니다.

다른 조직에 장치를 재할당 하는 경우 장치의 데이터는 데이터 수집 시간에 따라 조직에 유지 됩니다. 예를 들면 다음과 같습니다.

- 장치는 **contoso/Customer1** 조직의 일부 이며, 1 ~ 7 일 후에 8 일에 **contoso/Customer4** 조직으로 이동 됩니다.
- 9 일에 **Contoso/Customer4** 사용자는 8 ~ 9 일의 데이터를 볼 수 있습니다.
- 9 일에 **Contoso/Customer1** 사용자는 1 ~ 7 일의 데이터를 볼 수 있습니다.

### <a name="device-first-registration"></a>장치 우선 등록

장치는 먼저 장치 목록에 추가 되지 않고 IoT Central 응용 프로그램을 사용 하 여 자체 등록할 수 있습니다. 이 경우 IoT Central는 계층의 루트 조직에 장치를 추가 합니다. 그런 다음 다른 조직에 장치를 다시 할당할 수 있습니다.

대신 CSV 가져오기 기능을 사용 하 여 응용 프로그램에 장치를 대량 등록 하 고 조직에 할당할 수 있습니다. 자세히 알아보려면 [장치 가져오기](howto-manage-devices.md#import-devices)를 참조 하세요.

### <a name="gateways"></a>게이트웨이

게이트웨이 및 리프 장치를 조직에 할당 합니다. 게이트웨이 및 연결 된 리프 장치를 동일한 조직에 할당할 필요가 없습니다. 다른 조직에 할당 하는 경우 사용자가 게이트웨이를 볼 수 있지만 리프 장치나 리프 장치는 볼 수 없지만 게이트웨이는 볼 수 없습니다.

## <a name="roles"></a>역할

응용 프로그램에서 첫 번째 조직을 만들면 응용 프로그램에 조직 **관리자**, **조직 운영자** 및 **조직 뷰어** 라는 세 개의 새 역할이 추가 IoT Central. 이러한 역할은 조직 사용자가 가격 책정 계획, 브랜딩 및 색, API 토큰, 응용 프로그램 전체의 등록 그룹 정보 등의 특정 응용 프로그램 전체 기능에 액세스할 수 없기 때문에 필요 합니다.

:::image type="content" source="media/howto-create-organization/organization-roles.png" alt-text="세 가지 조직 역할을 보여 주는 스크린샷":::

응용 프로그램의 조직에 사용자를 초대할 때 이러한 역할을 사용할 수 있습니다.

### <a name="custom-organization-roles"></a>사용자 지정 조직 역할

조직 사용자에 대 한 사용자 지정 역할을 만들려면 새 역할을 만들고 **조직** 역할 유형을 선택 합니다.

:::image type="content" source="media/howto-create-organization/custom-organization-role.png" alt-text="사용자 지정 조직 역할을 만드는 방법을 보여 주는 스크린샷":::

그런 다음 역할에 대 한 권한을 선택 합니다.

:::image type="content" source="media/howto-create-organization/organization-role-permissions.png" alt-text="조직 역할에 대 한 사용자 지정 권한을 선택 하는 방법을 보여 주는 스크린샷":::

## <a name="invite-users"></a>사용자 초대

조직 계층을 만들고 조직에 장치를 할당 한 후에는 사용자를 응용 프로그램에 초대 하 고 조직에 할당 합니다.

사용자를 초대 하려면 **관리 > 사용자** 로 이동 합니다. 자신의 전자 메일 주소, 할당 된 조직 및 사용자가 구성원으로 속해 있는 역할을 입력 합니다. 선택한 조직에서 사용 가능한 역할 목록을 필터링 하 여 사용자를 유효한 역할에 할당 했는지 확인 합니다.

:::image type="content" source="media/howto-create-organization/assign-user-organization.png" alt-text="조직 및 역할에 사용자를 할당 하는 방법을 보여 주는 스크린샷":::

동일한 사용자를 여러 조직에 할당할 수 있습니다. 사용자는 할당 된 각 조직에서 다른 역할을 가질 수 있습니다.

| 이름 | 역할 | 조직 |
| ---- | ---- | ------------ |
| user1@contoso.com | 조직 관리자 | Contoso Inc./Lamna 상태 |
| user1@contoso.com | 조직 뷰어 | Contoso Inc./Adatum 태양계 |

새 사용자를 초대할 때 응용 프로그램 URL을 공유 하 고 로그인 하도록 요청 해야 합니다. 사용자가 처음으로 로그인 하면 해당 응용 프로그램이 사용자의 [내 앱](https://apps.azureiotcentral.com/myapps) 페이지에 표시 됩니다.

처음으로 응용 프로그램에 액세스 하려면 초대 된 사용자가 관리자 로부터 받은 링크를 사용 하 여 먼저 응용 프로그램으로 이동 해야 합니다. 그때까지 Azure IoT 중앙 사이트의 [내 앱](https://apps.azureiotcentral.com/myapps) 페이지에 응용 프로그램이 표시 되지 않습니다.

## <a name="use-organizations"></a>조직 사용

조직 계층 구조를 만든 후에는 다음과 같이 응용 프로그램의 영역에서 조직을 사용할 수 있습니다.

- 조직의 장치에 대 한 정보를 사용자에 게 표시 하는 [조직 대시보드](howto-manage-dashboards.md)
- 특정 조직에서 장치에 대 한 [장치 그룹](tutorial-use-device-groups.md)
- 특정 조직에서 장치에 대 한 [분석](howto-create-analytics.md) 입니다.
- 특정 조직에서 장치를 대량으로 관리 하는 [작업](howto-manage-devices-in-bulk.md#create-and-run-a-job) 입니다.

## <a name="default-organization"></a>기본 조직

응용 프로그램에서 사용할 기본 조직으로 조직을 설정할 수 있습니다. IoT Central 응용 프로그램에 새 사용자를 추가 하는 경우와 같이 조직을 선택할 때마다 기본 조직이 기본 옵션이 됩니다.

기본 조직을 설정 하려면 위쪽 메뉴 모음에서 **설정** 를 선택 합니다.

:::image type="content" source="media/howto-create-organization/set-default-organization.png" alt-text="기본 조직을 설정 하는 방법을 보여 주는 스크린샷":::

> [!TIP]
> 이는 사용자 에게만 적용 되는 개인 기본 설정입니다.

## <a name="add-organizations-to-an-existing-application"></a>기존 응용 프로그램에 조직 추가

조직 계층을 추가 하기 전에 응용 프로그램에 대시보드, 장치 그룹, 작업 등의 장치, 사용자 및 환경이 포함 될 수 있습니다.

조직 추가를 시작할 때 모든 기존 장치, 사용자 및 환경은 응용 프로그램의 루트 조직과 연결 된 상태를 유지 합니다.

- 새 조직에 장치를 다시 할당할 수 있습니다.
- 사용자를 새 조직에 할당 하 고 루트에서 할당을 해제할 수 있습니다.
- 대시보드, 장치 그룹 및 작업과 같은 환경을 다시 만들어 계층의 조직에 연결할 수 있습니다.

## <a name="limits"></a>제한

조직에 다음 제한이 적용 됩니다.

- 계층의 수준은 5 개이 하 여야 합니다.
- 총 조직 수는 200 개이 하 여야 합니다. 계층의 각 노드는 조직으로 계산 됩니다.

## <a name="next-steps"></a>다음 단계

Azure IoT 중앙 조직을 관리 하는 방법을 배웠으므로 이제 권장 되는 다음 단계는 [데이터 내보내기를 사용 하 여 클라우드 대상으로 IoT 데이터를 내보내는](howto-export-data.md)방법에 대해 알아봅니다.
