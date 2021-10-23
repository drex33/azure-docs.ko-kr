---
title: Azure Active Directory 사용하여 IoT Hub 대한 액세스 제어
description: 개발자 가이드. Azure AD 및 Azure RBAC를 사용하여 백 엔드 앱에 대한 IoT Hub 대한 액세스를 제어하는 방법입니다.
author: jlian
manager: briz
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/20/2021
ms.custom:
- 'Role: Cloud Development'
ms.openlocfilehash: a95501462fe1db0a06e6596054d09645c86ee64f
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130223534"
---
# <a name="control-access-to-iot-hub-by-using-azure-active-directory"></a>Azure Active Directory 사용하여 IoT Hub 대한 액세스 제어

azure AD(Azure Active Directory)를 사용하여 디바이스 ID 만들기 및 직접 메서드 호출과 같은 Azure IoT Hub 서비스 API에 대한 요청을 인증할 수 있습니다. Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 동일한 서비스 API에 권한을 부여할 수도 있습니다. 이러한 기술을 함께 사용하면 Azure AD 보안 주체에 IoT Hub 서비스 API에 액세스할 수 있는 권한을 부여할 수 있습니다. 이 보안 주체는 사용자, 그룹 또는 애플리케이션 서비스 주체일 수 있습니다.

Azure AD를 사용하여 액세스를 인증하고 Azure RBAC를 사용하여 권한을 제어하면 보안 토큰에 대한 보안 및 사용 편의성이 [향상됩니다.](iot-hub-dev-guide-sas.md) 보안 토큰에 내재된 잠재적인 보안 문제를 최소화하려면 [가능하면 IoT Hub에서 Azure AD를 사용하는](#azure-ad-access-and-shared-access-policies)것이 좋습니다. 

> [!NOTE]
> IoT Hub 디바이스 API(예: *디바이스-클라우드* 메시지 및 보고된 속성 업데이트)에는 Azure AD를 통한 인증이 지원되지 않습니다. [대칭 키](iot-hub-dev-guide-sas.md#use-a-symmetric-key-in-the-identity-registry) 또는 [X.509를](iot-hub-x509ca-overview.md) 사용하여 IoT Hub 디바이스를 인증합니다.

## <a name="authentication-and-authorization"></a>인증 및 권한 부여

Azure AD 보안 주체가 IoT Hub 서비스 API에 대한 액세스를 요청하면 보안 주체의 ID가 먼저 *인증됩니다.* 인증을 위해 요청에는 런타임에 OAuth 2.0 액세스 토큰이 포함되어야 합니다. 토큰을 요청하는 리소스 이름은 `https://iothubs.azure.net`입니다. 애플리케이션이 Azure VM, Azure Functions 앱 또는 Azure App Service 앱과 같은 Azure 리소스에서 실행되는 경우 관리 [ID로](../active-directory/managed-identities-azure-resources/how-managed-identities-work-vm.md)표시될 수 있습니다. 

Azure AD 보안 주체가 인증된 후 다음 단계는 *권한 부여입니다.* 이 단계에서는 IoT Hub Azure AD 역할 할당 서비스를 사용하여 보안 주체가 가진 권한을 확인합니다. 보안 주체의 권한이 요청된 리소스 또는 API와 일치하면 IoT Hub에서 요청을 승인합니다. 따라서 이 단계에서는 보안 주체에 하나 이상의 Azure 역할을 할당해야 합니다. IoT Hub는 일반적인 권한 그룹이 있는 몇 가지 기본 제공 역할을 제공합니다.

## <a name="manage-access-to-iot-hub-by-using-azure-rbac-role-assignment"></a>Azure RBAC 역할 할당을 사용하여 IoT Hub 대한 액세스 관리

Azure AD 및 RBAC를 사용하는 경우 IoT Hub에서 API를 요청하는 보안 주체가 권한을 부여하는 데 적절한 수준의 권한을 갖도록 요구합니다. 보안 주체에게 권한을 부여하려면 역할 할당을 제공합니다. 

- 보안 주체가 사용자, 그룹 또는 애플리케이션 서비스 주체인 경우 [Azure Portal 사용하여 Azure 역할 할당의](../role-based-access-control/role-assignments-portal.md)지침을 따릅니다.
- 보안 주체가 관리 ID인 경우 [Azure Portal 사용하여 리소스에 관리 ID 액세스 할당의](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md)지침을 따릅니다.

최소 권한을 보장하려면 항상 가능한 가장 낮은 [리소스 범위(IoT Hub 범위)에서](#resource-scope)적절한 역할을 할당합니다.

IoT Hub Azure AD 및 RBAC를 사용하여 IoT Hub 서비스 API에 대한 액세스 권한을 부여하기 위한 다음과 같은 Azure 기본 제공 역할을 제공합니다.

| 역할 | Description | 
| ---- | ----------- | 
| [IoT Hub 데이터 기여자](../role-based-access-control/built-in-roles.md#iot-hub-data-contributor) | IoT Hub 데이터 평면 작업에 대한 모든 권한을 허용합니다. |
| [IoT Hub 데이터 판독기](../role-based-access-control/built-in-roles.md#iot-hub-data-reader) | IoT Hub 데이터 평면 속성에 대한 전체 읽기 권한을 허용합니다. |
| [IoT Hub 레지스트리 기여자](../role-based-access-control/built-in-roles.md#iot-hub-registry-contributor) | IoT Hub 디바이스 레지스트리에 대한 모든 권한을 허용합니다. |
| [IoT Hub 쌍 기여자](../role-based-access-control/built-in-roles.md#iot-hub-twin-contributor) | 모든 IoT Hub 디바이스 및 모듈 쌍에 대한 읽기 및 쓰기 액세스를 허용합니다. |

필요한 권한을 결합하여 IoT Hub 사용할 사용자 지정 역할을 정의할 수도 [있습니다.](#permissions-for-iot-hub-service-apis) 자세한 내용은 [Azure 역할 기반 액세스 제어에 대한 사용자 지정 역할 만들기를 참조하세요.](../role-based-access-control/custom-roles.md)

### <a name="resource-scope"></a>리소스 범위

Azure RBAC 역할을 보안 주체에 할당하기 전에 보안 주체에게 부여해야 하는 액세스 범위를 결정합니다. 가능한 가장 좁은 범위만 부여하는 것이 항상 가장 좋습니다. 더 광범위한 범위에서 정의된 Azure RBAC 역할은 그 아래에 있는 리소스에 상속됩니다.

이 목록에서는 가장 좁은 범위부터 시작하여 IoT Hub 대한 액세스 범위를 지정할 수 있는 수준을 설명합니다.

- **IoT 허브.** 이 범위에서 역할 할당은 IoT Hub에 적용됩니다. 개별 IoT 허브보다 작은 범위는 없습니다. 개별 디바이스 ID 또는 쌍 섹션과 같은 더 작은 범위의 역할 할당은 지원되지 않습니다.
- **리소스 그룹.** 이 범위에서 역할 할당은 리소스 그룹의 모든 IoT Hub에 적용됩니다.
- **구독.** 이 범위에서 역할 할당은 구독의 모든 리소스 그룹에 있는 모든 IoT Hub에 적용됩니다.
- **관리 그룹.** 이 범위에서 역할 할당은 관리 그룹의 모든 구독에 있는 모든 리소스 그룹의 모든 IoT Hub에 적용됩니다.

## <a name="permissions-for-iot-hub-service-apis"></a>IoT Hub 서비스 API에 대한 권한

다음 표에서는 IoT Hub 서비스 API 작업에 사용할 수 있는 사용 권한을 설명합니다. 클라이언트가 특정 작업을 호출할 수 있도록 하려면 클라이언트의 할당된 RBAC 역할이 작업에 대한 충분한 권한을 제공하는지 확인합니다.

| RBAC 작업 | Description |
|-|-|
| `Microsoft.Devices/IotHubs/devices/read` | 디바이스 또는 모듈 ID를 읽습니다. |
| `Microsoft.Devices/IotHubs/devices/write`  | 디바이스 또는 모듈 ID를 만들거나 업데이트합니다.  |
| `Microsoft.Devices/IotHubs/devices/delete` | 디바이스 또는 모듈 ID를 삭제합니다. |
| `Microsoft.Devices/IotHubs/twins/read` | 디바이스 또는 모듈 쌍을 읽습니다. |
| `Microsoft.Devices/IotHubs/twins/write` | 디바이스 또는 모듈 쌍을 작성합니다. |
| `Microsoft.Devices/IotHubs/jobs/read` | 작업 목록을 반환합니다. |
| `Microsoft.Devices/IotHubs/jobs/write` | 작업을 만들거나 업데이트합니다. |
| `Microsoft.Devices/IotHubs/jobs/delete` | 작업을 삭제합니다. |
| `Microsoft.Devices/IotHubs/cloudToDeviceMessages/send/action` | 모든 디바이스에 클라우드-디바이스 메시지를 보냅니다.  |
| `Microsoft.Devices/IotHubs/cloudToDeviceMessages/feedback/action` | 클라우드-디바이스 메시지 피드백 알림을 수신, 완료 또는 중단합니다. |
| `Microsoft.Devices/IotHubs/cloudToDeviceMessages/queue/purge/action` | 디바이스에 대해 보류 중인 모든 명령을 삭제합니다.  |
| `Microsoft.Devices/IotHubs/directMethods/invoke/action` | 모든 디바이스 또는 모듈에서 직접 메서드를 호출합니다. |
| `Microsoft.Devices/IotHubs/fileUpload/notifications/action`  | 파일 업로드 알림을 수신, 완료 또는 중단합니다. |
| `Microsoft.Devices/IotHubs/statistics/read` | 디바이스 및 서비스 통계를 읽습니다. |
| `Microsoft.Devices/IotHubs/configurations/read` | 디바이스 관리 구성을 읽습니다. |
| `Microsoft.Devices/IotHubs/configurations/write` | 디바이스 관리 구성을 만들거나 업데이트합니다. |
| `Microsoft.Devices/IotHubs/configurations/delete` | 디바이스 관리 구성을 삭제합니다. |
| `Microsoft.Devices/IotHubs/configurations/applyToEdgeDevice/action`  | 에지 디바이스에 구성 콘텐츠를 적용합니다. |
| `Microsoft.Devices/IotHubs/configurations/testQueries/action` | 구성에 대한 대상 조건 및 사용자 지정 메트릭 쿼리의 유효성을 검사합니다. |

> [!TIP]
> - [대량 레지스트리 업데이트](/rest/api/iothub/service/bulkregistry/updateregistry) 작업에는 및 가 모두 `Microsoft.Devices/IotHubs/devices/write` `Microsoft.Devices/IotHubs/devices/delete` 필요합니다.
> - [쌍 쿼리](/rest/api/iothub/service/query/gettwins) 작업에는 `Microsoft.Devices/IotHubs/twins/read`가 필요합니다.
> - [디지털 트윈을 얻으려면](/rest/api/iothub/service/digitaltwin/getdigitaltwin) 가 `Microsoft.Devices/IotHubs/twins/read` 필요합니다. [Digital Twin 업데이트에는](/rest/api/iothub/service/digitaltwin/updatedigitaltwin) `Microsoft.Devices/IotHubs/twins/write` 가 필요합니다.
> - [구성 요소 호출 명령](/rest/api/iothub/service/digitaltwin/invokecomponentcommand) 및 [루트 수준 호출 명령](/rest/api/iothub/service/digitaltwin/invokerootlevelcommand)에는 모두 `Microsoft.Devices/IotHubs/directMethods/invoke/action`이 필요합니다.

> [!NOTE]
> Azure AD를 사용하여 IoT Hub 데이터를 얻으려면 [별도의 이벤트 허브로 라우팅을 설정합니다.](iot-hub-devguide-messages-d2c.md#event-hubs-as-a-routing-endpoint) [기본 제공 Event Hubs 호환 엔드포인트](iot-hub-devguide-messages-read-builtin.md)에 액세스하려면 이전과 같이 연결 문자열(공유 액세스 키) 메서드를 사용합니다. 

## <a name="azure-ad-access-and-shared-access-policies"></a>Azure AD 액세스 및 공유 액세스 정책

기본적으로 IoT Hub Azure AD와 공유 액세스 정책 및 보안 토큰을 통해 서비스 API [액세스를 지원합니다.](iot-hub-dev-guide-sas.md) 보안 토큰에 내재된 잠재적인 보안 취약성을 최소화하려면 공유 액세스 정책을 사용하여 액세스를 사용하지 않도록 설정합니다. 

1. 서비스 클라이언트와 사용자가 IoT Hub에 [충분히 액세스할 수](#manage-access-to-iot-hub-by-using-azure-rbac-role-assignment) 있는지 확인합니다. 최소 [권한 원칙을](../security/fundamentals/identity-management-best-practices.md)따릅니다.
1. [Azure Portal](https://portal.azure.com)에서 IoT Hub로 이동합니다.
1. 왼쪽 창에서 공유 **액세스 정책** 을 선택합니다.
1. **공유 액세스 정책을 사용하여 연결** 에서 **거부** 를 선택합니다.
    :::image type="content" source="media/iot-hub-dev-guide-azure-ad-rbac/disable-local-auth.png" alt-text="IoT Hub 공유 액세스 정책을 해제하는 방법을 보여 주는 스크린샷" border="true":::
1. 경고를 검토한 다음, **저장을** 선택합니다.

이제 IoT Hub 서비스 API는 Azure AD 및 RBAC를 통해서만 액세스할 수 있습니다.

## <a name="azure-ad-access-from-the-azure-portal"></a>Azure Portal에서 Azure AD 액세스

IoT Hub 액세스하려고 하면 Azure Portal 먼저 를 사용하여 Azure 역할이 할당되었는지 `Microsoft.Devices/iotHubs/listkeys/action` 확인합니다. 있는 경우 Azure Portal 공유 액세스 정책의 키를 사용하여 IoT Hub 액세스합니다. 그렇지 않은 경우 Azure Portal Azure AD 계정을 사용하여 데이터에 액세스하려고 시도합니다. 

Azure AD 계정을 사용하여 Azure Portal IoT Hub 액세스하려면 IoT Hub 데이터 리소스(예: 디바이스 및 트윈)에 액세스할 수 있는 권한이 필요합니다. 또한 Azure Portal IoT Hub 리소스로 이동하려면 권한이 필요합니다. IoT Hub 제공된 기본 제공 역할은 디바이스 및 쌍과 같은 리소스에 대한 액세스 권한을 부여합니다. 그러나 IoT Hub 리소스에 대한 액세스 권한을 부여하지 않습니다. 따라서 포털에 액세스하려면 [읽기](../role-based-access-control/built-in-roles.md#reader)권한자 와 같은 Azure Resource Manager 역할도 할당해야 합니다. 독자 역할은 포털을 탐색할 수 있는 가장 제한된 역할이므로 좋은 선택입니다. 공유 액세스 정책을 `Microsoft.Devices/iotHubs/listkeys/action` 통해 모든 IoT Hub 데이터 리소스에 대한 액세스를 제공하는 권한은 포함되지 않습니다. 

계정에 할당된 권한 외부의 액세스 권한이 없도록 하려면 사용자 지정 역할을 만들 때 권한을 포함하지 `Microsoft.Devices/iotHubs/listkeys/action` 마세요. 예를 들어 디바이스 ID를 읽을 수 있지만 디바이스를 만들거나 삭제할 수 없는 사용자 지정 역할을 만들려면 다음과 같은 사용자 지정 역할을 만듭니다.
- 데이터 `Microsoft.Devices/IotHubs/devices/read` 동작이 있습니다.
- `Microsoft.Devices/IotHubs/devices/write`데이터 작업이 없습니다.
- `Microsoft.Devices/IotHubs/devices/delete`데이터 작업이 없습니다.
- `Microsoft.Devices/iotHubs/listkeys/action`작업이 없습니다.

그런 다음, `Microsoft.Devices/iotHubs/listkeys/action` [소유자](../role-based-access-control/built-in-roles.md#owner) 또는 [기여자](../role-based-access-control/built-in-roles.md#contributor)와 같이 권한이 있는 다른 역할이 계정에 없는지 확인합니다. 계정에 리소스 액세스 권한이 있고 포털을 탐색할 수 있도록 하려면 [읽기](../role-based-access-control/built-in-roles.md#reader)권한자 를 할당합니다.

## <a name="azure-iot-extension-for-azure-cli"></a>Azure CLI용 Azure IoT 확장

IoT Hub에 대한 대부분의 명령은 Azure AD 인증을 지원합니다. 또는 값을 허용하는 매개 변수를 사용하여 명령을 실행하는 데 사용되는 인증 유형을 제어할 수 `--auth-type` `key` `login` 있습니다. `key`기본값은 입니다.

- `--auth-type`에 `key` 이전과 같이 값이 있는 경우 CLI는 IoT Hub 상호 작용할 때 적합한 정책을 자동으로 검색합니다.

- `--auth-type`에 값이 있으면 `login` 보안 주체에 로그인된 Azure CLI 액세스 토큰이 작업에 사용됩니다.

자세한 내용은 Azure CLI [릴리스에 대한 Azure IoT 확장 페이지를 참조하세요.](https://github.com/Azure/azure-iot-cli-extension/releases/tag/v0.10.12)

## <a name="sdk-samples"></a>SDK 샘플

- [.NET Microsoft.Azure.Devices SDK 샘플](https://aka.ms/iothubaadcsharpsample)
- [Java SDK 샘플](https://aka.ms/iothubaadjavasample)

## <a name="next-steps"></a>다음 단계

- 애플리케이션에서 Azure AD를 사용하는 이점에 대한 자세한 내용은 [Azure Active Directory와 통합](../active-directory/develop/active-directory-how-to-integrate.md)을 참조하세요.
- 사용자 및 서비스 주체에 대한 Azure AD의 액세스 토큰 요청 방법에 대한 자세한 내용은 [Azure AD에 대한 인증 시나리오](../active-directory/develop/authentication-vs-authorization.md)를 참조하세요.
