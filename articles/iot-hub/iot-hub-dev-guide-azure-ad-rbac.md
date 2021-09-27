---
title: Azure Active Directory를 사용 하 여 IoT Hub에 대 한 액세스 제어
description: 개발자 가이드. Azure AD 및 Azure RBAC를 사용 하 여 백 엔드 앱의 IoT Hub에 대 한 액세스를 제어 하는 방법입니다.
author: jlian
manager: briz
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/24/2021
ms.custom:
- 'Role: Cloud Development'
ms.openlocfilehash: a8387a5732ab51add02495b03236b42f9cd4e671
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128609444"
---
# <a name="control-access-to-iot-hub-by-using-azure-active-directory"></a>Azure Active Directory를 사용 하 여 IoT Hub에 대 한 액세스 제어

Azure Active Directory (Azure AD)를 사용 하 여 장치 id 만들기 및 직접 메서드 호출과 같은 Azure IoT 허브 서비스 api에 대 한 요청을 인증할 수 있습니다. Azure RBAC (역할 기반 액세스 제어)를 사용 하 여 동일한 서비스 Api에 권한을 부여할 수도 있습니다. 이러한 기술을 함께 사용 하 여 Azure AD 보안 주체에 IoT Hub 서비스 Api에 액세스할 수 있는 권한을 부여할 수 있습니다. 이 보안 주체는 사용자, 그룹 또는 응용 프로그램 서비스 사용자 일 수 있습니다.

Azure AD를 사용 하 여 액세스를 인증 하 고 Azure RBAC를 사용 하 여 권한을 제어 하면 향상 된 보안 및 [보안 토큰](iot-hub-dev-guide-sas.md)에 대 한 사용 편의성을 제공 합니다. 보안 토큰에 내재 된 잠재적인 보안 문제를 최소화 하려면 [가능 하면 IoT hub와 함께 AZURE AD를 사용](#azure-ad-access-and-shared-access-policies)하는 것이 좋습니다. 

> [!NOTE]
> Azure AD로 인증 하는 것은 장치-클라우드 메시지 및 업데이트 보고 속성 같은 IoT Hub *장치 api* 에 대해 지원 되지 않습니다. [대칭 키](iot-hub-dev-guide-sas.md#use-a-symmetric-key-in-the-identity-registry) 또는 [x.509](iot-hub-x509ca-overview.md) 를 사용 하 여 IoT Hub에 대 한 장치를 인증 합니다.

## <a name="authentication-and-authorization"></a>인증 및 권한 부여

Azure AD 보안 주체가 IoT Hub 서비스 API에 대 한 액세스를 요청 하면 보안 주체의 id가 먼저 *인증* 됩니다. 인증의 경우 요청은 런타임에 OAuth 2.0 액세스 토큰을 포함 해야 합니다. 토큰을 요청하는 리소스 이름은 `https://iothubs.azure.net`입니다. 응용 프로그램이 Azure VM, Azure Functions 앱 또는 Azure App Service 앱과 같은 Azure 리소스에서 실행 되는 경우 [관리 되는 id](../active-directory/managed-identities-azure-resources/how-managed-identities-work-vm.md)로 표시 될 수 있습니다. 

Azure AD 보안 주체가 인증 된 후 다음 단계는 *권한 부여* 입니다. 이 단계에서는 Azure AD 역할 할당 서비스를 사용 하 여 보안 주체의 사용 권한을 결정 IoT Hub. 보안 주체의 권한이 요청된 리소스 또는 API와 일치하면 IoT Hub에서 요청을 승인합니다. 따라서이 단계를 수행 하려면 보안 주체에 하나 이상의 Azure 역할을 할당 해야 합니다. IoT Hub는 일반적인 권한 그룹이 있는 몇 가지 기본 제공 역할을 제공합니다.

## <a name="manage-access-to-iot-hub-by-using-azure-rbac-role-assignment"></a>Azure RBAC 역할 할당을 사용 하 여 IoT Hub에 대 한 액세스 관리

Azure AD 및 RBAC를 사용하는 경우 IoT Hub에서 API를 요청하는 보안 주체가 권한을 부여하는 데 적절한 수준의 권한을 갖도록 요구합니다. 보안 주체에 권한을 부여 하려면 역할 할당을 제공 합니다. 

- 보안 주체가 사용자, 그룹 또는 응용 프로그램 서비스 사용자 인 경우 [Azure Portal를 사용 하 여 Azure 역할 할당](../role-based-access-control/role-assignments-portal.md)의 지침을 따릅니다.
- 보안 주체가 관리 되는 id 인 경우 [Azure Portal를 사용 하 여 리소스에 관리 id 액세스 할당](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md)의 지침을 따르세요.

최소 권한을 보장 하려면 항상 가능한 가장 낮은 [리소스 범위](#resource-scope)에서 적절 한 역할을 할당 합니다 .이는 IoT Hub 범위입니다.

IoT Hub는 Azure AD 및 RBAC를 사용 하 여 IoT Hub 서비스 Api에 대 한 액세스 권한을 부여 하기 위한 다음과 같은 Azure 기본 제공 역할을 제공 합니다.

| 역할 | Description | 
| ---- | ----------- | 
| [IoT Hub 데이터 기여자](../role-based-access-control/built-in-roles.md#iot-hub-data-contributor) | 데이터 평면 작업을 IoT Hub에 대 한 모든 액세스를 허용 합니다. |
| [IoT Hub 데이터 판독기](../role-based-access-control/built-in-roles.md#iot-hub-data-reader) | 데이터 평면 속성 IoT Hub에 대 한 전체 읽기 액세스를 허용 합니다. |
| [IoT Hub 레지스트리 기여자](../role-based-access-control/built-in-roles.md#iot-hub-registry-contributor) | IoT Hub 장치 레지스트리에 대 한 모든 액세스를 허용 합니다. |
| [IoT Hub 쌍 기여자](../role-based-access-control/built-in-roles.md#iot-hub-twin-contributor) | 모든 IoT Hub 장치 및 모듈 쌍에 대 한 읽기 및 쓰기 액세스를 허용 합니다. |

필요한 [사용 권한을](#permissions-for-iot-hub-service-apis) 결합 하 여 IoT Hub에 사용할 사용자 지정 역할을 정의할 수도 있습니다. 자세한 내용은 [Azure 역할 기반 액세스 제어를 위한 사용자 지정 역할 만들기](../role-based-access-control/custom-roles.md)를 참조 하세요.

### <a name="resource-scope"></a>리소스 범위

Azure RBAC 역할을 보안 주체에 할당하기 전에 보안 주체에게 부여해야 하는 액세스 범위를 결정합니다. 가능한 한 가장 좁은 범위만 부여 하는 것이 항상 가장 좋습니다. 더 광범위한 범위에서 정의된 Azure RBAC 역할은 그 아래에 있는 리소스에 상속됩니다.

이 목록에서는 가장 좁은 범위에서 시작 하 여 IoT Hub에 대 한 액세스 범위를 지정할 수 있는 수준을 설명 합니다.

- **IoT 허브.** 이 범위에서 역할 할당은 IoT hub에 적용 됩니다. 개별 IoT 허브보다 작은 범위는 없습니다. 개별 장치 id 또는 쌍 섹션과 같은 더 작은 범위의 역할 할당은 지원 되지 않습니다.
- **리소스 그룹.** 이 범위에서 역할 할당은 리소스 그룹의 모든 IoT hub에 적용 됩니다.
- **구독.** 이 범위에서 역할 할당은 구독의 모든 리소스 그룹에 있는 모든 IoT hub에 적용 됩니다.
- **관리 그룹.** 이 범위에서 역할 할당은 관리 그룹의 모든 구독에 있는 모든 리소스 그룹의 모든 IoT hub에 적용 됩니다.

## <a name="permissions-for-iot-hub-service-apis"></a>IoT Hub service Api에 대 한 사용 권한

다음 표에서는 IoT Hub service API 작업에 사용할 수 있는 권한을 설명 합니다. 클라이언트에서 특정 작업을 호출할 수 있도록 하려면 클라이언트의 할당 된 RBAC 역할에서 작업에 대 한 충분 한 권한을 제공 해야 합니다.

| RBAC 작업 | Description |
|-|-|
| `Microsoft.Devices/IotHubs/devices/read` | 모든 장치 또는 모듈 id를 읽습니다. |
| `Microsoft.Devices/IotHubs/devices/write`  | 장치 또는 모듈 id를 만들거나 업데이트 합니다.  |
| `Microsoft.Devices/IotHubs/devices/delete` | 모든 장치 또는 모듈 id를 삭제 합니다. |
| `Microsoft.Devices/IotHubs/twins/read` | 모든 장치 또는 모듈 쌍을 읽습니다. |
| `Microsoft.Devices/IotHubs/twins/write` | 모든 장치 또는 모듈 쌍을 씁니다. |
| `Microsoft.Devices/IotHubs/jobs/read` | 작업 목록을 반환 합니다. |
| `Microsoft.Devices/IotHubs/jobs/write` | 작업을 만들거나 업데이트 합니다. |
| `Microsoft.Devices/IotHubs/jobs/delete` | 작업을 삭제 합니다. |
| `Microsoft.Devices/IotHubs/cloudToDeviceMessages/send/action` | 모든 장치에 클라우드-장치 메시지를 보냅니다.  |
| `Microsoft.Devices/IotHubs/cloudToDeviceMessages/feedback/action` | 클라우드-장치 메시지 피드백 알림을 받거나 완료 하거나 중단 합니다. |
| `Microsoft.Devices/IotHubs/cloudToDeviceMessages/queue/purge/action` | 장치에 대해 보류 중인 모든 명령을 삭제 합니다.  |
| `Microsoft.Devices/IotHubs/directMethods/invoke/action` | 모든 장치 또는 모듈에서 직접 메서드를 호출 합니다. |
| `Microsoft.Devices/IotHubs/fileUpload/notifications/action`  | 파일 업로드 알림을 받거나 완료 하거나 중단 합니다. |
| `Microsoft.Devices/IotHubs/statistics/read` | 장치 및 서비스 통계를 읽습니다. |
| `Microsoft.Devices/IotHubs/configurations/read` | 장치 관리 구성을 읽습니다. |
| `Microsoft.Devices/IotHubs/configurations/write` | 장치 관리 구성을 만들거나 업데이트 합니다. |
| `Microsoft.Devices/IotHubs/configurations/delete` | 모든 장치 관리 구성을 삭제 합니다. |
| `Microsoft.Devices/IotHubs/configurations/applyToEdgeDevice/action`  | 에 지 장치에 구성 콘텐츠를 적용 합니다. |
| `Microsoft.Devices/IotHubs/configurations/testQueries/action` | 구성에 대 한 대상 조건 및 사용자 지정 메트릭 쿼리의 유효성을 검사 합니다. |

> [!TIP]
> - [대량 레지스트리 업데이트](/rest/api/iothub/service/bulkregistry/updateregistry) 작업을 수행 하려면 및가 모두 필요 합니다 `Microsoft.Devices/IotHubs/devices/write` `Microsoft.Devices/IotHubs/devices/delete` .
> - [쌍 쿼리](/rest/api/iothub/service/query/gettwins) 작업에는 `Microsoft.Devices/IotHubs/twins/read`가 필요합니다.
> - [디지털](/rest/api/iothub/service/digitaltwin/getdigitaltwin) 쌍을 받으려면 `Microsoft.Devices/IotHubs/twins/read` 를 사용 해야 합니다. [디지털 쌍 업데이트](/rest/api/iothub/service/digitaltwin/updatedigitaltwin) 에는가 필요 `Microsoft.Devices/IotHubs/twins/write` 합니다.
> - [구성 요소 호출 명령](/rest/api/iothub/service/digitaltwin/invokecomponentcommand) 및 [루트 수준 호출 명령](/rest/api/iothub/service/digitaltwin/invokerootlevelcommand)에는 모두 `Microsoft.Devices/IotHubs/directMethods/invoke/action`이 필요합니다.

> [!NOTE]
> Azure AD를 사용 하 여 IoT Hub에서 데이터를 가져오려면 [별도의 이벤트 허브로 라우팅을 설정](iot-hub-devguide-messages-d2c.md#event-hubs-as-a-routing-endpoint)합니다. [기본 제공 Event Hubs 호환 끝점](iot-hub-devguide-messages-read-builtin.md)에 액세스 하려면 이전 처럼 연결 문자열 (공유 액세스 키) 메서드를 사용 합니다. 

## <a name="azure-ad-access-and-shared-access-policies"></a>Azure AD 액세스 및 공유 액세스 정책

기본적으로 IoT Hub는 Azure AD 및 [공유 액세스 정책 및 보안 토큰](iot-hub-dev-guide-sas.md)을 통해 service API 액세스를 지원 합니다. 보안 토큰에 내재된 잠재적인 보안 취약성을 최소화하려면 공유 액세스 정책을 사용하여 액세스를 사용하지 않도록 설정합니다. 

1. 서비스 클라이언트 및 사용자에 게 IoT hub에 대 한 [충분 한 액세스](#manage-access-to-iot-hub-by-using-azure-rbac-role-assignment) 권한이 있는지 확인 합니다. [최소 권한의 원칙](../security/fundamentals/identity-management-best-practices.md)을 따릅니다.
1. [Azure Portal](https://portal.azure.com)에서 IoT Hub로 이동합니다.
1. 왼쪽 창에서 **공유 액세스 정책** 을 선택 합니다.
1. **공유 액세스 정책을 사용하여 연결** 에서 **거부** 를 선택합니다.
    :::image type="content" source="media/iot-hub-dev-guide-azure-ad-rbac/disable-local-auth.png" alt-text="공유 액세스 정책 IoT Hub 해제 하는 방법을 보여 주는 스크린샷":::
1. 경고를 검토 하 고 **저장** 을 선택 합니다.

이제 Azure AD 및 RBAC를 통해서만 IoT Hub 서비스 Api에 액세스할 수 있습니다.

## <a name="azure-ad-access-from-the-azure-portal"></a>Azure Portal에서 Azure AD 액세스

IoT Hub에 액세스 하려고 하면 Azure Portal 먼저를 사용 하 여 Azure 역할이 할당 되었는지 여부를 확인 `Microsoft.Devices/iotHubs/listkeys/action` 합니다. 있는 경우 Azure Portal 공유 액세스 정책의 키를 사용하여 IoT Hub 액세스합니다. 그렇지 않은 경우 Azure Portal Azure AD 계정을 사용하여 데이터에 액세스하려고 시도합니다. 

Azure AD 계정을 사용하여 Azure Portal IoT Hub 액세스하려면 IoT Hub 데이터 리소스(예: 디바이스 및 트윈)에 액세스할 수 있는 권한이 필요합니다. 또한 Azure Portal IoT Hub 리소스로 이동하려면 권한이 필요합니다. IoT Hub 제공되는 기본 제공 역할은 디바이스 및 쌍과 같은 리소스에 대한 액세스 권한을 부여합니다. 그러나 IoT Hub 리소스에 대한 액세스 권한은 부여하지 않습니다. 따라서 포털에 액세스하려면 [읽기](../role-based-access-control/built-in-roles.md#reader)권한자 와 같은 Azure Resource Manager 역할도 할당해야 합니다. 독자 역할은 포털을 탐색할 수 있는 가장 제한된 역할이므로 좋은 선택입니다. 공유 액세스 정책을 `Microsoft.Devices/iotHubs/listkeys/action` 통해 모든 IoT Hub 데이터 리소스에 대한 액세스를 제공하는 권한은 포함되지 않습니다. 

계정에 할당된 권한 외부의 액세스 권한이 없도록 하려면 사용자 지정 역할을 만들 때 사용 권한을 포함하지 `Microsoft.Devices/iotHubs/listkeys/action` 마세요. 예를 들어 디바이스 ID를 읽을 수 있지만 디바이스를 만들거나 삭제할 수 없는 사용자 지정 역할을 만들려면 다음과 같은 사용자 지정 역할을 만듭니다.
- 데이터 `Microsoft.Devices/IotHubs/devices/read` 동작이 있습니다.
- `Microsoft.Devices/IotHubs/devices/write`데이터 작업이 없습니다.
- `Microsoft.Devices/IotHubs/devices/delete`데이터 작업이 없습니다.
- `Microsoft.Devices/iotHubs/listkeys/action`작업이 없습니다.

그런 다음, `Microsoft.Devices/iotHubs/listkeys/action` [소유자](../role-based-access-control/built-in-roles.md#owner) 또는 [기여자](../role-based-access-control/built-in-roles.md#contributor)와 같이 권한이 있는 다른 역할이 계정에 없는지 확인합니다. 계정에 리소스 액세스 권한이 있고 포털을 탐색할 수 있도록 하려면 [읽기](../role-based-access-control/built-in-roles.md#reader)권한자 를 할당합니다.

## <a name="azure-iot-extension-for-azure-cli"></a>Azure CLI용 Azure IoT 확장

IoT Hub에 대한 대부분의 명령은 Azure AD 인증을 지원합니다. 또는 값을 허용하는 매개 변수를 사용하여 명령을 실행하는 데 사용되는 인증 유형을 제어할 수 `--auth-type` `key` `login` 있습니다. `key`기본값은 입니다.

- `--auth-type`에 `key` 이전과 같이 값이 있는 경우 CLI는 IoT Hub 상호 작용할 때 적절한 정책을 자동으로 검색합니다.

- 에 값이 있으면 보안 `--auth-type` `login` 주체에 기록된 Azure CLI 액세스 토큰이 작업에 사용됩니다.

자세한 내용은 Azure CLI [릴리스에 대한 Azure IoT 확장 페이지를 참조하세요.](https://github.com/Azure/azure-iot-cli-extension/releases/tag/v0.10.12)

## <a name="sdk-samples"></a>SDK 샘플

- [.NET Microsoft.Azure.Devices SDK 샘플](https://aka.ms/iothubaadcsharpsample)
- [Java SDK 샘플](https://aka.ms/iothubaadjavasample)

## <a name="next-steps"></a>다음 단계

- 애플리케이션에서 Azure AD를 사용하는 이점에 대한 자세한 내용은 [Azure Active Directory와 통합](../active-directory/develop/active-directory-how-to-integrate.md)을 참조하세요.
- 사용자 및 서비스 주체에 대한 Azure AD의 액세스 토큰 요청 방법에 대한 자세한 내용은 [Azure AD에 대한 인증 시나리오](../active-directory/develop/authentication-vs-authorization.md)를 참조하세요.
