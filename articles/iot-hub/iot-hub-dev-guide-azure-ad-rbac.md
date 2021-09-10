---
title: Azure Active Directory를 사용하여 IoT Hub에 대한 액세스 제어 | Microsoft Docs
description: 개발자 가이드 - Azure AD 및 Azure RBAC를 사용하여 백 엔드 앱의 IoT Hub에 대한 액세스를 제어하는 방법입니다.
author: jlian
manager: briz
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/24/2021
ms.custom:
- 'Role: Cloud Development'
ms.openlocfilehash: 30be3718215c31566f36d931266e0e5cdf039357
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122867247"
---
# <a name="control-access-to-iot-hub-using-azure-active-directory"></a>Azure Active Directory를 사용하여 IoT Hub에 대한 액세스 제어

Azure IoT Hub는 AAD(Azure Active Directory)를 사용하여 디바이스 ID 만들기 또는 직접 메서드 호출과 같은 서비스 API에 대한 요청을 인증하도록 지원합니다. 또한 IoT Hub는 Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 동일한 서비스 API에 대한 권한을 부여하도록 지원합니다. 동시에 IoT Hub의 서비스 API에 액세스할 수 있는 권한을 사용자, 그룹 또는 애플리케이션 서비스 주체일 수 있는 AAD 보안 주체에 부여할 수 있습니다.

Azure AD를 사용하여 액세스를 인증하고 Azure RBAC를 사용하여 권한을 제어하면 [보안 토큰](iot-hub-dev-guide-sas.md)보다 뛰어난 보안 및 사용 편의성을 제공합니다. 보안 토큰에 내재된 잠재적인 보안 취약성을 최소화하기 위해 [가능한 경우 IoT 허브와 함께 Azure AD를 사용](#azure-ad-access-and-shared-access-policies)하는 것이 좋습니다. 

> [!NOTE]
> IoT Hub의 *디바이스 API*(예: 디바이스-클라우드 메시지 및 reported 속성 업데이트)에는 Azure AD를 사용한 인증이 지원되지 않습니다. [대칭 키](iot-hub-dev-guide-sas.md#use-a-symmetric-key-in-the-identity-registry) 또는 [X.509](iot-hub-x509ca-overview.md)를 사용하여 디바이스를 IoT 허브에 인증하세요.

## <a name="authentication-and-authorization"></a>인증 및 권한 부여

Azure AD 보안 주체에서 IoT Hub 서비스 API에 대한 액세스를 요청하면 해당 보안 주체의 ID가 먼저 *인증* 됩니다. 이 단계에서는 런타임에 OAuth 2.0 액세스 토큰을 포함하도록 요청해야 합니다. 토큰을 요청하는 리소스 이름은 `https://iothubs.azure.net`입니다. 애플리케이션이 Azure VM, Azure Function 앱 또는 App Service 앱과 같은 Azure 리소스 내에서 실행되는 경우 [관리 ID](../active-directory/managed-identities-azure-resources/how-managed-identities-work-vm.md)로 표시될 수 있습니다. 

Azure AD 보안 주체가 인증되면 두 번째 단계는 *권한 부여* 입니다. 이 단계에서는 IoT Hub에서 Azure AD의 역할 할당 서비스와 대조하여 보안 주체의 권한을 확인합니다. 보안 주체의 권한이 요청된 리소스 또는 API와 일치하면 IoT Hub에서 요청을 승인합니다. 따라서 이 단계에서는 하나 이상의 Azure 역할을 보안 주체에 할당해야 합니다. IoT Hub는 일반적인 권한 그룹이 있는 몇 가지 기본 제공 역할을 제공합니다.

## <a name="manage-access-to-iot-hub-using-azure-rbac-role-assignment"></a>Azure RBAC 역할 할당을 사용하여 IoT Hub에 대한 액세스 관리

Azure AD 및 RBAC를 사용하는 경우 IoT Hub에서 API를 요청하는 보안 주체가 권한을 부여하는 데 적절한 수준의 권한을 갖도록 요구합니다. 권한을 보안 주체에 부여하려면 역할 할당을 해당 보안 주체에 부여합니다. 

- 보안 주체가 사용자, 그룹 또는 애플리케이션 서비스 주체인 경우 [Azure Portal을 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-portal.md)을 따릅니다.
- 보안 주체가 관리 ID인 경우 [Azure Portal을 사용하여 리소스에 대한 관리 ID 액세스 할당](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md)을 따릅니다.

최소 권한을 보장하려면 항상 가장 낮은 가능한 [리소스 범위](#resource-scope)(IoT Hub 범위일 수 있음)에서 적절한 역할을 할당합니다.

IoT Hub에서 Azure AD 및 RBAC를 사용하여 IoT Hub 서비스 API에 대한 액세스 권한을 부여하기 위해 제공하는 기본 역할은 다음과 같습니다.

| 역할 | Description | 
| ---- | ----------- | 
| [IoT Hub 데이터 기여자](../role-based-access-control/built-in-roles.md#iot-hub-data-contributor) | IoT Hub 데이터 평면 작업에 대한 모든 권한을 허용합니다. |
| [IoT Hub 데이터 읽기 권한자](../role-based-access-control/built-in-roles.md#iot-hub-data-reader) | IoT Hub 데이터 평면 속성에 대한 전체 읽기 액세스 권한을 허용합니다. |
| [IoT Hub 레지스트리 기여자](../role-based-access-control/built-in-roles.md#iot-hub-registry-contributor) | IoT Hub 디바이스 레지스트리에 대한 모든 권한을 허용합니다. |
| [IoT Hub 쌍 기여자](../role-based-access-control/built-in-roles.md#iot-hub-twin-contributor) | 모든 IoT Hub 디바이스 및 모듈 쌍에 대한 읽기 및 쓰기 액세스 권한을 허용합니다. |

또한 필요한 [권한](#permissions-for-iot-hub-service-apis)을 결합하여 IoT Hub에서 사용할 사용자 지정 역할을 정의할 수 있습니다. 자세한 내용은 [Azure 역할 기반 액세스 제어의 기본 제공 역할](../role-based-access-control/custom-roles.md)을 참조하세요.

### <a name="resource-scope"></a>리소스 범위

Azure RBAC 역할을 보안 주체에 할당하기 전에 보안 주체에게 부여해야 하는 액세스 범위를 결정합니다. 모범 사례에 따르면 항상 가능한 가장 좁은 범위만 부여하는 것이 가장 좋습니다. 더 광범위한 범위에서 정의된 Azure RBAC 역할은 그 아래에 있는 리소스에 상속됩니다.

다음 목록에서는 IoT Hub에 대한 액세스 범위를 지정할 수 있는 수준에 대해 설명하며, 가장 좁은 범위부터 시작합니다.

- **IoT 허브.** 이 범위에서 역할 할당은 IoT Hub에 적용됩니다. 개별 IoT 허브보다 작은 범위는 없습니다. 개별 디바이스 ID 또는 트윈 섹션과 같은 더 작은 범위의 역할 할당은 지원되지 않습니다.
- **리소스 그룹.** 이 범위에서 역할 할당은 리소스 그룹의 모든 IoT 허브에 적용됩니다.
- **구독.** 이 범위에서 역할 할당은 구독의 모든 리소스 그룹에 있는 모든 IoT 허브에 적용됩니다.
- **관리 그룹.** 이 범위에서 역할 할당은 관리 그룹의 모든 구독에 있는 모든 리소스 그룹의 모든 IoT 허브에 적용됩니다.

## <a name="permissions-for-iot-hub-service-apis"></a>IoT 허브 서비스 API에 대한 권한

다음 표에서는 IoT Hub 서비스 API 작업에 사용할 수 있는 권한을 설명합니다. 클라이언트에서 특정 작업을 호출할 수 있도록 하려면 클라이언트에 할당된 RBAC 역할에서 해당 작업에 대한 충분한 권한을 제공해야 합니다.

| RBAC 작업 | Description |
|-|-|
| Microsoft.Devices/IotHubs/devices/read | 디바이스 또는 모듈 ID 읽기 |
| Microsoft.Devices/IotHubs/devices/write  | 디바이스 또는 모듈 ID 만들기 또는 업데이트  |
| Microsoft.Devices/IotHubs/devices/delete | 디바이스 또는 모듈 ID 삭제 |
| Microsoft.Devices/IotHubs/twins/read | 디바이스 또는 모듈 쌍 읽기 |
| Microsoft.Devices/IotHubs/twins/write | 디바이스 또는 모듈 쌍 쓰기 |
| Microsoft.Devices/IotHubs/jobs/read | 작업 목록 반환 |
| Microsoft.Devices/IotHubs/jobs/write | 작업 만들기 또는 업데이트 |
| Microsoft.Devices/IotHubs/jobs/delete | 작업 삭제 |
| Microsoft.Devices/IotHubs/cloudToDeviceMessages/send/action | 디바이스에 클라우드-디바이스 메시지 보내기  |
| Microsoft.Devices/IotHubs/cloudToDeviceMessages/feedback/action | 클라우드-디바이스 메시지 피드백 알림 받기, 완료 또는 중단 |
| Microsoft.Devices/IotHubs/cloudToDeviceMessages/queue/purge/action | 디바이스에 대해 보류 중인 모든 명령 삭제  |
| Microsoft.Devices/IotHubs/directMethods/invoke/action | 모든 디바이스 또는 모듈에서 직접 메서드 호출 |
| Microsoft.Devices/IotHubs/fileUpload/notifications/action  | 파일 업로드 알림 받기, 완료 또는 중단 |
| Microsoft.Devices/IotHubs/statistics/read | 디바이스 및 서비스 통계 읽기 |
| Microsoft.Devices/IotHubs/configurations/read | 디바이스 관리 구성 읽기 |
| Microsoft.Devices/IotHubs/configurations/write | 디바이스 관리 구성 만들기 또는 업데이트 |
| Microsoft.Devices/IotHubs/configurations/delete | 디바이스 관리 구성 삭제 |
| Microsoft.Devices/IotHubs/configurations/applyToEdgeDevice/action  | 에지 디바이스에 구성 콘텐츠 적용 |
| Microsoft.Devices/IotHubs/configurations/testQueries/action | 구성에 대한 대상 조건 및 사용자 지정 메트릭 쿼리의 유효성 검사 |

> [!TIP]
> - [대량 레지스트리 업데이트](/rest/api/iothub/service/bulkregistry/updateregistry) 작업에는 `Microsoft.Devices/IotHubs/devices/write` *및* `Microsoft.Devices/IotHubs/devices/delete`가 *모두* 필요합니다.
> - [쌍 쿼리](/rest/api/iothub/service/query/gettwins) 작업에는 `Microsoft.Devices/IotHubs/twins/read`가 필요합니다.
> - [디지털 트윈 가져오기](/rest/api/iothub/service/digitaltwin/getdigitaltwin)에는 `Microsoft.Devices/IotHubs/twins/read`가 필요하지만, [디지털 트윈 업데이트](/rest/api/iothub/service/digitaltwin/updatedigitaltwin)에는 `Microsoft.Devices/IotHubs/twins/write`가 필요합니다.
> - [구성 요소 호출 명령](/rest/api/iothub/service/digitaltwin/invokecomponentcommand) 및 [루트 수준 호출 명령](/rest/api/iothub/service/digitaltwin/invokerootlevelcommand)에는 모두 `Microsoft.Devices/IotHubs/directMethods/invoke/action`이 필요합니다.

> [!NOTE]
> Azure AD를 사용하여 IoT Hub에서 데이터를 가져오려면 [별도의 이벤트 허브로 라우팅을 설정](iot-hub-devguide-messages-d2c.md#event-hubs-as-a-routing-endpoint)합니다. [기본 제공 이벤트 허브 호환 엔드포인트](iot-hub-devguide-messages-read-builtin.md)에 액세스하려면 이전과 같이 연결 문자열(공유 액세스 키) 방법을 사용합니다. 

## <a name="azure-ad-access-and-shared-access-policies"></a>Azure AD 액세스 및 공유 액세스 정책

기본적으로 IoT Hub는 Azure AD와 [공유 액세스 정책 및 보안 토큰](iot-hub-dev-guide-sas.md)을 통한 서비스 API 액세스를 지원합니다. 보안 토큰에 내재된 잠재적인 보안 취약성을 최소화하려면 공유 액세스 정책을 사용하여 액세스를 사용하지 않도록 설정합니다. 

1. 서비스 클라이언트와 사용자가 [최소 권한 원칙](../security/fundamentals/identity-management-best-practices.md)에 따라 IoT Hub에 대한 [충분한 액세스 권한](#manage-access-to-iot-hub-using-azure-rbac-role-assignment)을 갖고 있는지 확인합니다.
1. [Azure Portal](https://portal.azure.com)에서 IoT Hub로 이동합니다.
1. 왼쪽에서 **공유 액세스 정책** 을 선택합니다.
1. **공유 액세스 정책을 사용하여 연결** 에서 **거부** 를 선택합니다.
    :::image type="content" source="media/iot-hub-dev-guide-azure-ad-rbac/disable-local-auth.png" alt-text="IoT Hub 공유 액세스 정책을 해제하는 방법을 보여 주는 Azure Portal 스크린샷":::
1. 경고를 검토한 다음 **저장** 을 선택합니다.

이제 Azure AD 및 RBAC를 통해서만 IoT Hub 서비스 API에 액세스할 수 있습니다.

## <a name="azure-ad-access-from-the-azure-portal"></a>Azure Portal에서 Azure AD 액세스

IoT Hub에 액세스하려고 하면 Azure Portal에서 먼저 Microsoft에 **Microsoft.Devices/iotHubs/listkeys/action** 을 사용하여 Azure 역할이 할당되었는지 확인합니다. 그렇다면 Azure Portal에서 공유 액세스 정책의 키를 사용하여 IoT Hub에 액세스합니다. 그렇지 않은 경우 Azure Portal에서 Azure AD 계정을 사용하여 데이터에 액세스하려고 시도합니다. 

Azure AD 계정을 사용하여 Azure Portal에서 IoT Hub에 액세스하려면 IoT 허브 데이터 리소스(예: 디바이스 및 트윈)에 액세스할 수 있는 권한이 필요하고, Azure Portal에서 IoT 허브 리소스로 이동할 수 있는 권한도 필요합니다. IoT Hub에서 제공하는 기본 제공 역할은 리소스(예: 디바이스 및 트윈)에 대한 액세스 권한을 부여하지만, IoT Hub 리소스에 대한 액세스 권한은 부여하지 않습니다. 따라서 포털에 액세스하려면 [읽기 권한자](../role-based-access-control/built-in-roles.md#reader)와 같은 ARM(Azure Resource Manager) 역할도 할당해야 합니다. 읽기 권한자 역할은 포털을 탐색할 수 있는 가장 제한된 역할이고 **Microsoft.Devices/iotHubs/listkeys/action** 권한(공유 액세스 정책을 통해 모든 IoT Hub 데이터 리소스에 대한 액세스 권한 부여)을 포함하지 않으므로 적합합니다. 

할당된 권한 이외의 액세스 권한이 계정에 없도록 하려면 사용자 지정 역할을 만들 때 **Microsoft.Devices/iotHubs/listkeys/action** 권한을 포함하지 *않습니다*. 예를 들어 디바이스 ID를 읽을 수 있지만 디바이스를 만들거나 삭제할 수 없는 사용자 지정 역할을 만들려면 다음과 같은 사용자 지정 역할을 만듭니다.
- **Microsoft.Devices/IotHubs/devices/read** 데이터 작업이 있음
- **Microsoft.Devices/IotHubs/devices/write** 데이터 작업이 없음
- **Microsoft.Devices/IotHubs/devices/delete** 데이터 작업이 없음
- **Microsoft.Devices/iotHubs/listkeys/action** 작업이 없음

그런 다음, **Microsoft.Devices/iotHubs/listkeys/action** 권한이 있는 다른 역할(예: [소유자](../role-based-access-control/built-in-roles.md#owner) 또는 [기여자](../role-based-access-control/built-in-roles.md#contributor))이 계정에 없는지 확인합니다. 계정에 리소스 액세스 권한이 있고 포털을 탐색할 수 있도록 하려면 [읽기 권한자](../role-based-access-control/built-in-roles.md#reader)를 할당합니다.

## <a name="azure-iot-extension-for-azure-cli"></a>Azure CLI용 Azure IoT 확장

IoT Hub에 대한 대부분의 명령은 Azure AD 인증을 지원합니다. 명령을 실행하는 데 사용되는 인증 유형은 키 또는 로그인 값을 허용하는 `--auth-type` 매개 변수를 사용하여 제어할 수 있습니다. 기본적으로 `key` 값이 설정되어 있습니다.

- `--auth-type`에 `key` 값이 있는 경우 이전과 마찬가지로 CLI가 IoT Hub와 상호 작용할 때 적절한 정책을 자동으로 검색합니다.

- `--auth-type`에 `login` 값이 있는 경우 Azure CLI 로그인 보안 주체의 액세스 토큰이 작업에 사용됩니다.

자세한 내용은 [Azure CLI에 대한 IoT 확장 릴리스 페이지](https://github.com/Azure/azure-iot-cli-extension/releases/tag/v0.10.12)를 참조하세요.

## <a name="sdk-samples"></a>SDK 샘플

- [.NET Microsoft.Azure.Devices SDK 샘플](https://aka.ms/iothubaadcsharpsample)
- [Java SDK 샘플](https://aka.ms/iothubaadjavasample)

## <a name="next-steps"></a>다음 단계

- 애플리케이션에서 Azure AD를 사용하는 이점에 대한 자세한 내용은 [Azure Active Directory와 통합](../active-directory/develop/active-directory-how-to-integrate.md)을 참조하세요.
- 사용자 및 서비스 주체에 대한 Azure AD의 액세스 토큰 요청 방법에 대한 자세한 내용은 [Azure AD에 대한 인증 시나리오](../active-directory/develop/authentication-vs-authorization.md)를 참조하세요.
