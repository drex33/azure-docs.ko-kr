---
title: 인스턴스 및 인증 설정(포털)
titleSuffix: Azure Digital Twins
description: Azure Portal을 사용하여 Azure Digital Twins 서비스 인스턴스를 설정하는 방법을 참조하세요.
author: baanders
ms.author: baanders
ms.date: 9/2/2021
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy21q2, subject-rbac-steps
ms.openlocfilehash: 2b7522c91ce5cb60683ce9be905a263a5cb99e52
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129984823"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-portal"></a>Azure Digital Twins 인스턴스 및 인증 설정(포털)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

이 문서에서는 인스턴스를 만들고 인증을 설정하는 등 **새 Azure Digital Twins 인스턴스를 설정** 하는 단계에 대해 설명합니다. 이 문서를 완료하면 프로그래밍을 시작할 준비가 된 Azure Digital Twins 인스턴스가 준비되었습니다.

이 문서의 버전은 Azure Portal을 사용하여 이러한 단계를 하나씩 수동으로 진행합니다. Azure Portal은 명령줄 도구의 대안을 제공하는 웹 기반의 통합 콘솔입니다.

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]

## <a name="create-the-azure-digital-twins-instance"></a>Azure Digital Twins 인스턴스 만들기

[!INCLUDE [digital-twins-setup-portal.md](../../includes/digital-twins-setup-portal.md)]

3. 다음 **리소스 만들기** 페이지에서 아래 지정된 값을 입력합니다.
    * **구독**: 사용 중인 Azure 구독
      - **리소스 그룹**: 인스턴스를 배포할 리소스 그룹입니다. 기존 리소스 그룹이 아직 없는 경우 *새로 만들기* 링크를 선택하고 새 리소스 그룹의 이름을 입력하여 여기에서 하나를 만들 수 있습니다.
    * **위치**: 배포를 위한 Azure Digital twins 지원 지역입니다. 지역별 지원에 대한 자세한 내용은 [지역별 사용 가능한 Azure 제품(Azure Digital Twins)](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins) 을 참조하세요.
    * **리소스 이름**: Azure Digital Twins 인스턴스의 이름입니다. 구독에 이미 지정된 이름을 사용하고 있는 지역에 다른 Azure Digital Twins 인스턴스가 있는 경우 다른 이름을 선택하라는 메시지가 표시됩니다.
    * **리소스에 대한 액세스 권한 부여**: 이 섹션의 확인란을 선택하면 인스턴스의 데이터에 액세스하고 관리할 수 있는 권한이 Azure 계정에 부여됩니다. 인스턴스를 관리할 사용자인 경우 지금 이 확인란을 선택해야 합니다. 구독에 대한 권한이 없으므로 회색으로 표시됩니다. 리소스를 계속 만들고 필요한 권한이 있는 사용자가 나중에 역할을 부여하게 할 수 있습니다. 이 역할 및 인스턴스에 역할 할당에 대한 자세한 내용은 다음 섹션인 [사용자 액세스 권한 설정](#set-up-user-access-permissions)을 참조하세요.

    :::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins-2.png" alt-text="Azure Portal의 Azure Digital Twins에 대한 리소스 만들기 프로세스의 스크린샷. 설명된 값이 채워져 있습니다.":::

4. 완료되었을 때 인스턴스에 대한 추가 설정을 구성하지 않으려는 경우 **검토 + 만들기** 를 선택할 수 있습니다. 이렇게 하면 입력한 인스턴스 세부 정보를 검토하고 **만들기를** 완료할 수 있는 요약 페이지가 표시됩니다. 

    인스턴스에 대한 자세한 정보를 구성하려면 다음 섹션에서 나머지 설정 탭을 설명합니다.

### <a name="additional-setup-options"></a>추가 설치 옵션

다음은 설치 중에 **리소스 만들기** 프로세스의 다른 탭을 사용하여 구성할 수 있는 추가 옵션입니다.

* **네트워킹**: 이 탭에서는 [Azure Private Link](../private-link/private-link-overview.md)로 프라이빗 엔드포인트를 사용하도록 설정하여 인스턴스에 대한 공용 네트워크 노출을 제거할 수 있습니다. 자세한 내용은 [Private Link를 사용하여 프라이빗 액세스 사용(미리 보기)](./how-to-enable-private-link.md?tabs=portal#add-a-private-endpoint-during-instance-creation)을 참조하세요.
* **고급**: 이 탭에서는 이벤트를 엔드포인트로 전달할 때 사용할 수 있는 인스턴스에 대해 [시스템 관리 ID](concepts-route-events.md)를 사용하도록 설정할 수 있습니다. Azure Digital Twins의 시스템 관리 ID 사용에 대한 자세한 내용은 [Azure Digital Twins 솔루션의 보안](concepts-security.md#managed-identity-for-accessing-other-resources)을 참조하세요.
* **태그**: 이 탭에서는 인스턴스에 태그를 추가하여 Azure 리소스에서 구성하는 데 도움을 줍니다. Azure 리소스 태그에 대한 자세한 내용은 [논리 조직에 대한 리소스, 리소스 그룹 및 구독 태그](../azure-resource-manager/management/tag-resources.md)를 참조하세요.

### <a name="verify-success-and-collect-important-values"></a>성공 확인 및 중요한 값 수집

**만들기** 를 선택하여 인스턴스 설치를 완료한 후에는 포털 아이콘 표시줄에 나타나는 Azure 알림에서 인스턴스의 배포 상태를 볼 수 있습니다. 배포가 성공하면 알림이 표시됩니다. 이때 _리소스로 이동_ 단추를 선택하여 만든 인스턴스를 볼 수 있습니다.

:::image type="content" source="media/how-to-set-up-instance/portal/notifications-deployment.png" alt-text="성공적인 배포를 표시하고 Azure Portal에서 '리소스로 이동' 단추를 강조 표시하는 Azure 알림의 스크린샷":::

배포가 실패하면 알림에 이유가 표시됩니다. 오류 메시지의 조언을 따르고 인스턴스 만들기를 다시 시도합니다.

>[!TIP]
>인스턴스를 만든 후에는 Azure Portal 검색 창에서 인스턴스 이름을 검색하여 언제든지 페이지로 돌아갈 수 있습니다.

인스턴스의 *개요* 페이지에서 **이름**, **리소스 그룹** 및 **호스트 이름** 을 확인합니다. 이러한 값은 모두 중요하며 Azure Digital Twins 인스턴스를 계속 사용할 때 사용해야 할 수도 있습니다. 다른 사용자가 인스턴스에 대한 프로그래밍을 수행하는 경우 다른 사용자와 이 값을 공유해야 합니다.

:::image type="content" source="media/how-to-set-up-instance/portal/instance-important-values.png" alt-text="Azure Digital Twins 인스턴스의 개요 페이지에서 중요한 값을 강조 표시하는 Azure Portal의 스크린샷":::

이제 Azure Digital Twins 인스턴스를 사용할 준비가 되었습니다. 다음으로, 적절한 Azure 사용자에게 관리 권한을 부여합니다.

## <a name="set-up-user-access-permissions"></a>사용자 액세스 권한 설정

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

Azure Digital Twins 사용자에 대한 역할 할당을 만드는 방법에는 두 가지가 있습니다.
* [Azure Digital Twins 인스턴스를 만드는 동안](#assign-the-role-during-instance-creation)
* [Azure ID 관리 사용(IAM)](#assign-the-role-using-azure-identity-management-iam)

둘 다 동일한 권한이 필요합니다.

### <a name="prerequisites-permission-requirements"></a>필수 조건: 권한 요구 사항

[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

### <a name="assign-the-role-during-instance-creation"></a>인스턴스를 만드는 동안 역할 할당

[이 문서의 앞부분에서](#create-the-azure-digital-twins-instance) 설명한 프로세스를 통해 Azure Digital Twins 리소스를 만드는 동안 **리소스에 대한 액세스 권한 부여** 에서 **Azure Digital Twins 데이터 소유자 역할 할당** 을 선택합니다. 이렇게 하면 데이터 평면 API에 대한 모든 액세스 권한이 부여됩니다.

:::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins-2-role.png" alt-text="Azure Portal의 Azure Digital Twins에 대한 리소스 만들기 프로세스의 스크린샷. 리소스에 대한 액세스 권한 부여 아래의 확인란이 강조 표시됩니다.":::

ID에 역할을 할당할 수 있는 권한이 없으면 상자가 회색으로 표시됩니다.

:::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins-2-role-greyed.png" alt-text="Azure Portal의 Azure Digital Twins에 대한 리소스 만들기 프로세스의 스크린샷. 리소스에 대한 액세스 권한 부여 아래의 확인란이 비활성화됩니다.":::

이 경우 계속해서 Azure Digital Twins 리소스를 만들 수 있지만 적절한 권한이 있는 사용자는 이 역할을 사용자에게 할당하거나 인스턴스 데이터를 관리할 사용자에게 할당해야 합니다.

### <a name="assign-the-role-using-azure-identity-management-iam"></a>Azure ID 관리(IAM)를 사용하여 역할 할당

Azure ID 관리(IAM)의 액세스 제어 옵션을 사용하여 **Azure Digital Twins 데이터 소유자** 역할을 할당할 수도 있습니다.

1. 먼저 Azure Portal에서 Azure Digital Twins 인스턴스의 페이지를 엽니다. 

1. **액세스 제어(IAM)** 를 선택합니다.

1. **추가** > **역할 할당 추가** 를 선택하여 역할 할당 추가 페이지를 엽니다.

1. **Azure Digital Twins 데이터 소유자** 역할을 할당합니다. 세부 단계에 대해서는 [Azure Portal을 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-portal.md)을 참조하세요.
    
    | 설정 | 값 |
    | --- | --- |
    | 역할 | [Azure Digital Twins 데이터 소유자](../role-based-access-control/built-in-roles.md#azure-digital-twins-data-owner) |
    | 다음에 대한 액세스 할당 | 사용자, 그룹 또는 서비스 주체 |
    | 멤버 | 할당할 사용자의 이름 또는 이메일 주소를 검색합니다. |
    
    ![역할 할당 추가 페이지](../../includes/role-based-access-control/media/add-role-assignment-page.png)

### <a name="verify-success"></a>성공 확인

*액세스 제어(IAM) > 역할 할당* 에서 설정된 역할 할당을 볼 수 있습니다. 사용자는 *Azure Digital Twins 데이터 소유자* 역할로 목록에 표시되어야 합니다. 

:::image type="content" source="media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="Azure Portal에서 Azure Digital Twins 인스턴스에 대한 역할 할당의 스크린샷":::

이제 Azure Digital Twins 인스턴스를 사용할 준비가 되고 관리 권한이 할당되었습니다.

## <a name="next-steps"></a>다음 단계

Azure Digital Twins CLI 명령을 사용하여 인스턴스에서 개별 REST API 호출을 테스트합니다. 
* [az dt reference](/cli/azure/dt?view=azure-cli-latest&preserve-view=true)
* [Azure Digital Twins CLI 명령 집합](concepts-cli.md)

또는 인증 코드를 사용하여 인스턴스에 클라이언트 애플리케이션을 연결하는 방법을 참조하세요.
* [앱 인증 코드 쓰기](how-to-authenticate-client.md)