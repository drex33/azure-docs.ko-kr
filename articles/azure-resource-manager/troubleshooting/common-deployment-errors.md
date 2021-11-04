---
title: 일반적인 Azure 배포 오류 문제 해결
description: ARM 템플릿(Azure Resource Manager 템플릿) 또는 Bicep 파일을 통해 배포된 Azure 리소스의 일반적인 오류에 대해 설명합니다.
tags: top-support-issue
ms.topic: troubleshooting
ms.date: 11/02/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 988d2e85475ed1c94580cc550630df82fceddb7a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131470246"
---
# <a name="troubleshoot-common-azure-deployment-errors"></a>일반적인 Azure 배포 오류 문제 해결

이 문서에서는 일반적인 Azure 배포 오류에 대해 설명하고 솔루션에 대한 정보를 제공합니다. AZURE 리소스는 ARM 템플릿(Azure Resource Manager 템플릿) 또는 Bicep 파일을 통해 배포할 수 있습니다. 배포 오류에 대한 오류 코드를 찾을 수 없는 경우 [오류 코드 찾기](find-error-code.md)를 참조하세요.

오류 코드가 나열되지 않으면 GitHub 문제를 제출합니다. 페이지 오른쪽에서 **피드백을** 선택합니다. 페이지 아래쪽의 **피드백** 아래에서 **이 페이지를** 선택합니다.

## <a name="error-codes"></a>오류 코드

| 오류 코드 | 완화 방법 | 자세한 정보 |
| ---------- | ---------- | ---------------- |
| AccountNameInvalid | 스토리지 계정에 대한 명명 제한 사항을 따릅니다. | [스토리지 계정 이름 오류 해결](error-storage-account-name.md) |
| AccountPropertyCannotBeSet | 사용 가능한 스토리지 계정 속성을 확인합니다. | [storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| AllocationFailed | 클러스터나 지역에 사용할 수 있는 리소스가 없거나 요청한 VM 크기를 지원할 수 없습니다. 나중에 요청을 다시 시도하거나 다른 VM 크기를 요청합니다. | [Linux의 프로비전 및 할당 문제](/troubleshoot/azure/virtual-machines/troubleshoot-deployment-new-vm-linux) <br><br> [Windows의 프로비전 및 할당 문제](/troubleshoot/azure/virtual-machines/troubleshoot-deployment-new-vm-windows) <br><br> [할당 오류 문제 해결](/troubleshoot/azure/virtual-machines/allocation-failure)|
| AnotherOperationInProgress | 동시 작업이 완료될 때까지 기다립니다. | |
| AuthorizationFailed | 계정 또는 서비스 주체가 배포를 완료하는 데 충분한 권한이 없습니다. 계정이 속한 역할 및 배포 범위에 대한 액세스 권한을 확인합니다.<br><br>필수 리소스 공급자가 등록되지 않은 경우 이 오류가 표시될 수 있습니다. | [Azure RBAC(Azure 역할 기반 액세스 제어)](../../role-based-access-control/role-assignments-portal.md)<br><br>[등록 오류 해결](error-register-resource-provider.md) |
| BadRequest | Resource Manager에서 예상한 것과 일치하지 않는 배포 값을 보냈습니다. 문제 해결에 도움이 되는 내부 상태 메시지를 확인합니다. | [템플릿 참조](/azure/templates/) <br><br> [지원되는 위치](../templates/resource-location.md) |
| 충돌 | 리소스의 현재 상태에서 허용되지 않는 작업을 요청하고 있습니다. 예를 들어 디스크 크기 조정은 VM을 만들거나 VM의 할당을 취소할 때만 허용됩니다. | |
| DeploymentActiveAndUneditable | 이 리소스 그룹에 대한 동시 배포가 완료될 때까지 기다립니다. | |
| DeploymentFailedCleanUp | 전체 모드로 배포하는 경우 템플릿에 없는 모든 리소스가 삭제됩니다. 템플릿에 없는 리소스를 모두 삭제할 수 있는 적절한 권한이 없는 경우 이 오류가 발생합니다. 오류를 방지하려면 배포 모드를 증분으로 변경합니다. | [Azure Resource Manager 배포 모드](../templates/deployment-modes.md) |
| DeploymentNameInvalidCharacters | 배포 이름에는 문자, 숫자, `(-)` 하이픈, 점 또는 밑 줄만 포함될 수 `(.)` `(_)` 있습니다. | |
| DeploymentNameLengthLimitExceeded | 배포 이름은 64자로 제한됩니다.  | |
| DeploymentFailed | DeploymentFailed 오류는 해결하는 데 필요한 세부 정보를 제공하지 않는 일반 오류입니다. 자세한 정보를 제공하는 오류 코드에 대해서는 오류 세부 정보를 살펴봅니다. | [오류 코드 찾기](find-error-code.md) |
| DeploymentQuotaExceeded | 리소스 그룹당 800개 배포 제한에 도달하면 기록에서 더 이상 필요하지 않은 배포를 삭제합니다. | [배포 수가 800을 초과하는 경우 오류 해결](deployment-quota-exceeded.md) |
| DeploymentJobSizeExceeded | 크기를 줄이기 위해 템플릿을 단순화합니다. | [템플릿 크기 오류 해결](error-job-size-exceeded.md) |
| DnsRecordInUse | DNS 레코드 이름은 고유해야 합니다. 다른 이름을 입력하세요. | |
| ImageNotFound | VM 이미지 설정을 확인합니다. |  |
| InternalServerError | 일시적인 문제로 인해 발생합니다. 배포를 다시 시도합니다. | |
| InUseSubnetCannotBeDeleted | 이 오류는 요청 프로세스가 삭제되고 리소스를 만드는 경우 리소스를 업데이트하려고 할 때 발생할 수 있습니다. 변경되지 않은 모든 값을 지정해야 합니다. | [리소스 업데이트](/azure/architecture/guide/azure-resource-manager/advanced-templates/update-resource) |
| InvalidAuthenticationTokenTenant | 해당 테넌트에 대한 액세스 토큰을 가져옵니다. 계정이 속한 테넌트의 토큰만 가져올 수 있습니다. | |
| InvalidContentLink | 아마 사용할 수 없는 중첩된 템플릿에 연결하려고 했을 것입니다. 중첩된 템플릿에 제공된 URI를 다시 한 번 확인합니다. 스토리지 계정에 해당 템플릿이 있는 경우 액세스 가능한 URI인지 확인합니다. SAS 토큰을 전달해야 할 수 있습니다. 현재 [Azure Storage 방화벽](../../storage/common/storage-network-security.md) 뒤에 있는 스토리지 계정의 템플릿에 연결할 수 없습니다. 템플릿을 GitHub와 같은 다른 리포지토리로 이동하는 것이 좋습니다. | [연결된 템플릿](../templates/linked-templates.md) |
| InvalidDeploymentLocation | 구독 수준에서 배포하는 경우 이전에 사용한 배포 이름에 대해 다른 위치를 제공했습니다. | [구독 수준 배포](../templates/deploy-to-subscription.md) |
| InvalidParameter | 리소스에 대해 제공한 값 중 하나가 예상 값과 일치하지 않습니다. 이 오류는 다양한 조건으로 인해 발생할 수 있습니다. 예를 들어 암호가 완전하지 않거나 Blob 이름이 올바르지 않을 수 있습니다. 오류 메시지에는 수정해야 하는 값이 표시됩니다. | |
| InvalidRequestContent | 배포 값에 인식되지 않는 값이 포함되었거나 필요한 값이 누락되었습니다. 리소스 종류에 대한 값을 확인합니다. | [템플릿 참조](/azure/templates/) |
| InvalidRequestFormat | 배포를 실행할 때 디버그 로깅을 사용하도록 설정하고 요청 내용을 확인합니다. | [디버그 로깅](enable-debug-logging.md) |
| InvalidResourceLocation | 스토리지 계정에 고유한 이름을 제공합니다. | [스토리지 계정 이름 오류 해결](error-storage-account-name.md) |
| InvalidResourceNamespace | **type** 속성에 지정한 리소스 네임스페이스를 확인합니다. | [템플릿 참조](/azure/templates/) |
| InvalidResourceReference | 리소스가 아직 없거나 잘못 참조되었습니다. 종속성을 추가해야 하는지 확인합니다. **reference** 함수를 사용할 때 시나리오에 필요한 매개 변수가 포함되었는지 확인합니다. | [종속성 오류 해결](error-not-found.md) |
| InvalidResourceType | **type** 속성에 지정한 리소스 종류를 확인합니다. | [템플릿 참조](/azure/templates/) |
| InvalidSubscriptionRegistrationState | 리소스 공급자에 구독을 등록합니다. | [등록 오류 해결](error-register-resource-provider.md) |
| Invalid템플릿 배포 <br> InvalidTemplate | 템플릿 구문에 오류가 있는지 확인합니다. | [잘못된 템플릿 오류 해결](error-invalid-template.md) |
| InvalidTemplateCircularDependency | 불필요한 종속성을 제거합니다. | [순환 종속성 해결](error-invalid-template.md#circular-dependency) |
| JobSizeExceeded | 크기를 줄이기 위해 템플릿을 단순화합니다. | [템플릿 크기 오류 해결](error-job-size-exceeded.md) |
| LinkedAuthorizationFailed | 계정이 배포하는 리소스 그룹과 동일한 테넌트에 속하는지 확인합니다. | |
| LinkedInvalidPropertyId | 리소스의 리소스 ID가 확인 되지 않습니다. 리소스 ID에 필요한 모든 값을 제공 했는지 확인 합니다. 예를 들어 구독 ID, 리소스 그룹 이름, 리소스 종류, 부모 리소스 이름 (필요한 경우) 및 리소스 이름을 입력 합니다. | |
| LocationRequired | 리소스에 대한 위치를 제공합니다. | [위치 설정](../templates/resource-location.md) |
| MismatchingResourceSegments | 중첩 된 리소스의 이름 및 형식에 올바른 수의 세그먼트가 있는지 확인 합니다. | [리소스 세그먼트 해결](error-invalid-template.md#incorrect-segment-lengths) |
| MissingRegistrationForLocation | 리소스 공급자 등록 상태 및 지원되는 위치를 확인합니다. | [등록 오류 해결](error-register-resource-provider.md) |
| MissingSubscriptionRegistration | 리소스 공급자에 구독을 등록합니다. | [등록 오류 해결](error-register-resource-provider.md) |
| NoRegisteredProviderFound | 리소스 공급자 등록 상태를 확인합니다. | [등록 오류 해결](error-register-resource-provider.md) |
| NotFound | 종속 리소스를 부모 리소스와 병렬로 배포하려고 시도했을 수 있습니다. 종속성을 추가해야 하는지 확인합니다. | [종속성 오류 해결](error-not-found.md) |
| OperationNotAllowed | 배포에서 구독, 리소스 그룹 또는 지역에 대한 할당량을 초과하는 작업을 시도하고 있습니다. 가능하면 배포를 수정하여 할당량 내에서 유지합니다. 그렇지 않은 경우 할당량 변경을 요청하는 것이 좋습니다. | [할당량 오류 해결](error-resource-quota.md) |
| ParentResourceNotFound | 자식 리소스를 만들기 전에 부모 리소스가 있는지 확인합니다. | [부모 리소스 오류 해결](error-parent-resource.md) |
| PasswordTooLong | 너무 많은 문자가 포함된 암호를 선택했거나, 매개 변수로 전달하기 전에 암호 값을 보안 문자열로 변환했을 수 있습니다. 템플릿에 **보안 문자열** 매개 변수가 포함되어 있으면 값을 보안 문자열로 변환할 필요가 없습니다. 암호 값을 텍스트로 제공합니다. |  |
| PrivateIPAddressInReservedRange | 지정된 IP 주소에는 Azure에 필요한 주소 범위가 포함됩니다. 예약된 범위를 방지하도록 IP 주소를 변경합니다. | [IP 주소](../../virtual-network/ip-services/public-ip-addresses.md) |
| PrivateIPAddressNotInSubnet | 지정된 IP 주소가 서브넷 범위를 벗어났습니다. 서브넷 범위에 속하는 IP 주소로 변경합니다. | [IP 주소](../../virtual-network/ip-services/public-ip-addresses.md) |
| PropertyChangeNotAllowed | 일부 속성이 배포된 리소스에서 변경될 수 없습니다. 리소스를 업데이트할 때 변경이 허용되는 속성으로 제한합니다. | [리소스 업데이트](/azure/architecture/guide/azure-resource-manager/advanced-templates/update-resource) |
| RequestDisallowedByPolicy | 구독에는 배포 중에 수행 하려는 작업을 방해 하는 리소스 정책이 포함 됩니다. 해당 작업을 차단하는 정책을 찾습니다. 가능하면 정책의 제한 사항을 충족하도록 배포를 변경합니다. | [정책 오류 해결](error-policy-requestdisallowedbypolicy.md) |
| ReservedResourceName | 예약된 이름을 포함하지 않는 리소스 이름을 제공합니다. | [예약된 리소스 이름](error-reserved-resource-name.md) |
| ResourceGroupBeingDeleted | 삭제가 완료될 때까지 기다립니다. | |
| ResourceGroupNotFound | 배포에 대한 대상 리소스 그룹의 이름을 확인합니다. 대상 리소스 그룹이 구독에 이미 있어야 합니다. 구독 컨텍스트를 확인합니다. | [Azure CLI](/cli/azure/account?#az_account_set) [PowerShell](/powershell/module/Az.Accounts/Set-AzContext) |
| ResourceNotFound | 배포에서 확인할 수 없는 리소스를 참조합니다. **reference** 함수를 사용할 때 시나리오에 필요한 매개 변수가 포함되었는지 확인합니다. | [참조 오류 해결](error-not-found.md) |
| ResourceQuotaExceeded | 배포에서 구독, 리소스 그룹 또는 지역에 대한 할당량을 초과하는 리소스를 만들려고 합니다. 가능하면 인프라를 수정하여 할당량 내에서 유지합니다. 그렇지 않은 경우 할당량 변경을 요청하는 것이 좋습니다. | [할당량 오류 해결](error-resource-quota.md) |
| SkuNotAvailable | 선택한 위치에서 사용할 수 있는 SKU(예: VM 크기)를 선택합니다. | [SKU 오류 해결](error-sku-not-available.md) |
| StorageAccountAlreadyExists <br> StorageAccountAlreadyTaken | 스토리지 계정에 고유한 이름을 제공합니다. | [스토리지 계정 이름 오류 해결](error-storage-account-name.md)  |
| StorageAccountNotFound | 사용하려는 구독, 리소스 그룹 및 스토리지 계정 이름을 확인합니다. | |
| SubnetsNotInSameVnet | 가상 머신에는 하나의 가상 네트워크만 있을 수 있습니다. 여러 NIC를 배포할 때는 모두 동일한 가상 네트워크에 속해야 합니다. | [여러 NIC](../../virtual-machines/windows/multiple-nics.md) |
| SubscriptionNotFound | 배포에 대해 지정된 구독에 액세스할 수 없습니다. 구독 ID가 잘못되었거나, 템플릿을 배포하는 사용자가 구독에 배포할 수 있는 적절한 권한이 없거나, 구독 ID의 형식이 잘못된 것일 수 있습니다. 중첩 배포를 사용하여 [범위 간에 배포](../templates/deploy-to-resource-group.md)하는 경우 구독에 대한 GUID를 제공합니다. | |
| SubscriptionNotRegistered | 리소스를 배포하는 경우 구독에 대한 리소스 공급자를 등록해야 합니다. 배포에 Azure Resource Manager 템플릿을 사용하면 리소스 공급자가 구독에 자동으로 등록됩니다. 자동 등록이 시간 내에 완료되지 않는 경우가 있습니다. 이 일시적인 오류를 방지하려면 배포 전에 리소스 공급자를 등록합니다. | [등록 오류 해결](error-register-resource-provider.md) |
| TemplateResourceCircularDependency | 불필요한 종속성을 제거합니다. | [순환 종속성 해결](error-invalid-template.md#circular-dependency) |
| TooManyTargetResourceGroups | 단일 배포에 대한 리소스 그룹의 수를 줄입니다. | [범위 간 배포](../templates/deploy-to-resource-group.md) |

## <a name="next-steps"></a>다음 단계

- [오류 코드 찾기](find-error-code.md)
- [디버그 로깅 활성화](enable-debug-logging.md)
- [문제 해결 템플릿 만들기](create-troubleshooting-template.md)
