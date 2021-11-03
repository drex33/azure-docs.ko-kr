---
title: 일반 배포 오류 문제 해결
description: Azure Resource Manager 템플릿 (ARM 템플릿) 또는 Bicep 파일을 사용 하 여 Azure 리소스를 배포할 때 발생 하는 일반적인 오류를 해결 하는 방법을 설명 합니다.
tags: top-support-issue
ms.topic: troubleshooting
ms.date: 10/26/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: fe10ccd523618c1afbc9bf5214bcd51bc18419ed
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131102986"
---
# <a name="troubleshoot-common-azure-deployment-errors"></a>일반적인 Azure 배포 오류 문제 해결

이 문서에서는 일반적인 Azure 배포 오류 중 일부에 대해 설명하고 오류를 해결하기 위한 정보를 제공합니다. Azure Resource Manager 템플릿 (ARM 템플릿) 또는 Bicep 파일을 사용 하 여 Azure 리소스를 배포할 수 있습니다. 배포 오류에 대한 오류 코드를 찾을 수 없는 경우 [오류 코드 찾기](#find-error-code)를 참조하세요.

오류 코드에 대한 정보를 찾고 있는데 이 문서에서 해당 정보를 제공하지 않는다면 알려주세요. 이 페이지의 하단에서 사용자 의견을 남길 수 있습니다. 사용자 의견은 GitHub 문제를 통해 추적됩니다.

PowerShell을 실행 하 고 포털에서 Azure CLI 명령을 실행 하려면 [Azure Cloud Shell](../../cloud-shell/overview.md)를 사용 합니다. 컴퓨터에서 명령을 실행 하려면 [Azure PowerShell](/powershell/azure/install-az-ps) 또는 [Azure CLI](/cli/azure/install-azure-cli)를 설치 합니다.

## <a name="error-codes"></a>오류 코드

| 오류 코드 | 완화 방법 | 자세한 정보 |
| ---------- | ---------- | ---------------- |
| AccountNameInvalid | 스토리지 계정에 대한 명명 제한 사항을 따릅니다. | [스토리지 계정 이름 오류 해결](error-storage-account-name.md) |
| AccountPropertyCannotBeSet | 사용 가능한 스토리지 계정 속성을 확인합니다. | [storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| AllocationFailed | 클러스터나 지역에 사용할 수 있는 리소스가 없거나 요청한 VM 크기를 지원할 수 없습니다. 나중에 요청을 다시 시도하거나 다른 VM 크기를 요청합니다. | [Linux의 프로비전 및 할당 문제](/troubleshoot/azure/virtual-machines/troubleshoot-deployment-new-vm-linux) <br><br> [Windows의 프로비전 및 할당 문제](/troubleshoot/azure/virtual-machines/troubleshoot-deployment-new-vm-windows) <br><br> [할당 오류 문제 해결](/troubleshoot/azure/virtual-machines/allocation-failure)|
| AnotherOperationInProgress | 동시 작업이 완료될 때까지 기다립니다. | |
| AuthorizationFailed | 계정 또는 서비스 주체가 배포를 완료하는 데 충분한 권한이 없습니다. 계정이 속한 역할 및 배포 범위에 대한 액세스 권한을 확인합니다.<br><br>필수 리소스 공급자가 등록되지 않은 경우 이 오류가 표시될 수 있습니다. | [Azure RBAC(Azure 역할 기반 액세스 제어)](../../role-based-access-control/role-assignments-portal.md)<br><br>[등록 오류 해결](error-register-resource-provider.md) |
| BadRequest | Resource Manager에서 예상한 것과 일치하지 않는 배포 값을 보냈습니다. 문제 해결에 도움이 되는 내부 상태 메시지를 확인합니다. | [템플릿 참조](/azure/templates/) 및 [지원되는 위치](../templates/resource-location.md) |
| 충돌 | 리소스의 현재 상태에서 허용되지 않는 작업을 요청하고 있습니다. 예를 들어 디스크 크기 조정은 VM을 만들거나 VM의 할당을 취소할 때만 허용됩니다. | |
| DeploymentActiveAndUneditable | 이 리소스 그룹에 대한 동시 배포가 완료될 때까지 기다립니다. | |
| DeploymentFailedCleanUp | 전체 모드로 배포하는 경우 템플릿에 없는 모든 리소스가 삭제됩니다. 템플릿에 없는 리소스를 모두 삭제할 수 있는 적절한 권한이 없는 경우 이 오류가 발생합니다. 오류를 방지하려면 배포 모드를 증분으로 변경합니다. | [Azure Resource Manager 배포 모드](../templates/deployment-modes.md) |
| DeploymentNameInvalidCharacters | 배포 이름에는 문자, 숫자, 하이픈 `(-)` , 점 또는 밑줄만 사용할 수 있습니다 `(.)` `(_)` . | |
| DeploymentNameLengthLimitExceeded | 배포 이름은 64자로 제한됩니다.  | |
| DeploymentFailed | DeploymentFailed 오류는 해결하는 데 필요한 세부 정보를 제공하지 않는 일반 오류입니다. 자세한 정보를 제공하는 오류 코드에 대해서는 오류 세부 정보를 살펴봅니다. | [오류 코드 찾기](#find-error-code) |
| DeploymentQuotaExceeded | 리소스 그룹당 800개 배포 제한에 도달하면 기록에서 더 이상 필요하지 않은 배포를 삭제합니다. | [배포 수가 800을 초과하는 경우 오류 해결](deployment-quota-exceeded.md) |
| DeploymentJobSizeExceeded | 크기를 줄이기 위해 템플릿을 단순화합니다. | [템플릿 크기 오류 해결](error-job-size-exceeded.md) |
| DnsRecordInUse | DNS 레코드 이름은 고유해야 합니다. 다른 이름을 입력하세요. | |
| ImageNotFound | VM 이미지 설정을 확인합니다. |  |
| InternalServerError | 일시적인 문제로 인해 발생 했습니다. 배포를 다시 시도합니다. | |
| InUseSubnetCannotBeDeleted | 리소스를 업데이트하려고 할 때 이 오류가 발생할 수 있으며 해당 리소스를 삭제한 다음 만들면 요청이 처리됩니다. 변경되지 않은 모든 값을 지정해야 합니다. | [리소스 업데이트](/azure/architecture/guide/azure-resource-manager/advanced-templates/update-resource) |
| InvalidAuthenticationTokenTenant | 해당 테넌트에 대한 액세스 토큰을 가져옵니다. 계정이 속한 테넌트의 토큰만 가져올 수 있습니다. | |
| InvalidContentLink | 아마 사용할 수 없는 중첩된 템플릿에 연결하려고 했을 것입니다. 중첩된 템플릿에 제공된 URI를 다시 한 번 확인합니다. 스토리지 계정에 해당 템플릿이 있는 경우 액세스 가능한 URI인지 확인합니다. SAS 토큰을 전달해야 할 수 있습니다. 현재 [Azure Storage 방화벽](../../storage/common/storage-network-security.md) 뒤에 있는 스토리지 계정의 템플릿에 연결할 수 없습니다. 템플릿을 GitHub와 같은 다른 리포지토리로 이동하는 것이 좋습니다. | [연결된 템플릿](../templates/linked-templates.md) |
| InvalidDeploymentLocation | 구독 수준에서 배포하는 경우 이전에 사용한 배포 이름에 대해 다른 위치를 제공했습니다. | [구독 수준 배포](../templates/deploy-to-subscription.md) |
| InvalidParameter | 리소스에 대해 제공한 값 중 하나가 예상 값과 일치하지 않습니다. 이 오류는 다양한 조건으로 인해 발생할 수 있습니다. 예를 들어 암호가 완전하지 않거나 Blob 이름이 올바르지 않을 수 있습니다. 오류 메시지에는 수정해야 하는 값이 표시됩니다. | |
| InvalidRequestContent | 배포 값에 인식되지 않는 값이 포함되었거나 필요한 값이 누락되었습니다. 리소스 종류에 대한 값을 확인합니다. | [템플릿 참조](/azure/templates/) |
| InvalidRequestFormat | 배포를 실행할 때 디버그 로깅을 사용하도록 설정하고 요청 내용을 확인합니다. | [디버그 로깅](#enable-debug-logging) |
| InvalidResourceLocation | 스토리지 계정에 고유한 이름을 제공합니다. | [스토리지 계정 이름 오류 해결](error-storage-account-name.md) |
| InvalidResourceNamespace | **type** 속성에 지정한 리소스 네임스페이스를 확인합니다. | [템플릿 참조](/azure/templates/) |
| InvalidResourceReference | 리소스가 아직 없거나 잘못 참조되었습니다. 종속성을 추가해야 하는지 확인합니다. **reference** 함수를 사용할 때 시나리오에 필요한 매개 변수가 포함되었는지 확인합니다. | [종속성 오류 해결](error-not-found.md) |
| InvalidResourceType | **type** 속성에 지정한 리소스 종류를 확인합니다. | [템플릿 참조](/azure/templates/) |
| InvalidSubscriptionRegistrationState | 리소스 공급자에 구독을 등록합니다. | [등록 오류 해결](error-register-resource-provider.md) |
| InvalidTemplateDeployment <br> InvalidTemplate | 템플릿 구문에 오류가 있는지 확인합니다. | [잘못된 템플릿 오류 해결](error-invalid-template.md) |
| InvalidTemplateCircularDependency | 불필요한 종속성을 제거합니다. | [순환 종속성 해결](error-invalid-template.md#circular-dependency) |
| JobSizeExceeded | 크기를 줄이기 위해 템플릿을 단순화합니다. | [템플릿 크기 오류 해결](error-job-size-exceeded.md) |
| LinkedAuthorizationFailed | 계정이 배포하는 리소스 그룹과 동일한 테넌트에 속하는지 확인합니다. | |
| LinkedInvalidPropertyId | 리소스에 대한 리소스 ID가 올바르게 해석되지 않습니다. 구독 ID, 리소스 그룹 이름, 리소스 종류, 부모 리소스 이름(필요한 경우) 및 리소스 이름을 포함하여 리소스 ID에 필요한 모든 값을 제공했는지 확인합니다. | |
| LocationRequired | 리소스에 대한 위치를 제공합니다. | [위치 설정](../templates/resource-location.md) |
| MismatchingResourceSegments | 중첩된 리소스에 이름 및 형식의 세그먼트 수가 올바른지 확인합니다. | [리소스 세그먼트 해결](error-invalid-template.md#incorrect-segment-lengths) |
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
| RequestDisallowedByPolicy | 구독에는 배포 중에 수행하려는 작업을 방지하는 리소스 정책이 포함됩니다. 해당 작업을 차단하는 정책을 찾습니다. 가능하면 정책의 제한 사항을 충족하도록 배포를 변경합니다. | [정책 오류 해결](error-policy-requestdisallowedbypolicy.md) |
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

## <a name="find-error-code"></a>오류 코드 찾기

두 가지 유형의 오류가 발생할 수 있습니다.

- 유효성 검사 오류
- 배포 오류

유효성 검사 오류는 배포 전에 확인할 수 있는 시나리오에서 발생합니다. 예를 들어 템플릿 구문 오류가 발생하거나 구독 할당량을 초과하는 리소스를 배포하려고 합니다. 배포 오류는 배포 프로세스 중 발생하는 조건에서 발생합니다. 여기에는 병렬로 배포되는 리소스에 액세스하려는 시도가 포함됩니다.

ARM 템플릿의 경우 두 오류 유형 모두 배포 문제를 해결하는 데 사용하는 오류 코드를 반환합니다. 두 가지 오류 유형 모두 [활동 로그](../../azure-monitor/essentials/activity-log.md)에 나타납니다. 하지만 배포가 시작된 것은 아니므로 유효성 검사 오류는 배포 기록에 나타나지 않습니다. Bicep 파일 유효성 검사 오류는 활동 로그 또는 배포 기록에 표시되지 않습니다. 활동 로그가 최신 배포 정보를 표시하는 데 몇 분 정도 걸립니다.

### <a name="validation-errors"></a>유효성 검사 오류

ARM 템플릿을 포털에 업로드하고 리소스를 배포할 수 있습니다. 템플릿에 구문 오류가 있으면 배포를 실행하려고 할 때 유효성 검사 오류가 표시됩니다. 자세한 내용은 [사용자 지정 템플릿 에서 리소스 배포를 참조하세요.](../templates/deploy-portal.md#deploy-resources-from-custom-template)

다음 예제에서는 스토리지 계정을 배포하려고 시도하며 유효성 검사 오류가 발생합니다.

:::image type="content" source="media/common-deployment-errors/validation-error.png" alt-text="Azure Portal 유효성 검사 오류의 스크린샷":::

메시지를 선택하여 세부 정보를 확인합니다. 템플릿에 오류 코드가 인 구문 오류가 `InvalidTemplate` 있습니다. **요약** 은 식에 닫는 괄호가 누락된 것을 보여줍니다.

:::image type="content" source="media/common-deployment-errors/validation-details.png" alt-text="구문 오류를 보여 주는 유효성 검사 오류 메시지의 스크린샷.":::

### <a name="deployment-errors"></a>배포 오류

작업이 유효성 검사를 통과하지만 배포 중에 실패하면 배포 오류가 표시됩니다.

포털에서 배포 오류를 보려면 리소스 그룹으로 이동하여 **배포 설정**  >  **선택합니다.** **오류 세부 정보** 를 선택합니다.

:::image type="content" source="media/common-deployment-errors/deployment-error-details.png" alt-text="실패한 배포에 대한 오류 세부 정보의 리소스 그룹 링크 스크린샷.":::

오류 메시지 및 오류 `NoRegisteredProviderFound` 코드가 표시됩니다.

:::image type="content" source="media/common-deployment-errors/deployment-error-summary.png" alt-text="배포 오류 세부 정보를 보여 주는 메시지의 스크린샷.":::

### <a name="powershell"></a>PowerShell

PowerShell을 사용하여 배포 오류 코드 및 메시지를 보려면 [Get-AzResourceGroupDeployment](/powershell/module/az.resources/get-azresourcegroupdeployment)를 사용합니다.

```azurepowershell
(Get-AzResourceGroupDeploymentOperation -DeploymentName exampledeployment -ResourceGroupName examplegroup).StatusMessage
```

### <a name="azure-cli"></a>Azure CLI

Azure CLI 있는 배포 오류 코드 및 메시지를 보려면 [az deployment operation group list 를](/cli/azure/deployment/operation/group#az_deployment_operation_group_list)사용합니다.

```azurecli
az deployment operation group list --name exampledeployment --resource-group examplegroup --query "[*].properties.statusMessage"
```

## <a name="enable-debug-logging"></a>디버그 로깅 활성화

무엇이 잘못되었는지 알려면 요청 및 응답에 대한 정보가 더 필요한 경우가 있습니다. 배포 중에 추가 정보가 기록되도록 요청할 수 있습니다.

### <a name="powershell"></a>PowerShell

PowerShell에서 [New-AzResourceGroupDeployment를](/powershell/module/az.resources/new-azresourcegroupdeployment) 사용하여 `DeploymentDebugLogLevel` 매개 변수를 , 또는 로 `All` `ResponseContent` `RequestContent` 설정합니다.

> [!NOTE]
> 디버그 로깅을 사용하도록 설정하면 [Get-AzResourceGroupDeploymentOperation과](/powershell/module/az.resources/get-azresourcegroupdeploymentoperation)같은 명령을 통해 암호 또는 listKeys와 같은 비밀을 기록할 수 있다는 경고가 표시됩니다.
>
> `request` `response` `Get-AzResourceGroupDeploymentOperation` Az 모듈 버전 4.8 이상에서는 출력에 이 포함되지 않으므로 Azure CLI 사용하여 디버그 및 정보를 `properties` 가져옵니다.  자세한 내용은 [GitHub 문제 14706을 참조하세요.](https://github.com/Azure/azure-powershell/issues/14706)


```azurepowershell
New-AzResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName examplegroup `
  -TemplateFile c:\Azure\Templates\storage.json `
  -DeploymentDebugLogLevel All
```

요청 내용을 다음 cmdlet으로 검토합니다.

```azurepowershell
(Get-AzResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.request `
| ConvertTo-Json
```

또는 응답 내용을 다음으로 검토합니다.

```azurepowershell
(Get-AzResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.response `
| ConvertTo-Json
```

이 정보를 통해 템플릿의 값이 잘못 설정되었는지 확인할 수 있습니다.

### <a name="azure-cli"></a>Azure CLI

현재 Azure CLI는 디버그 로깅 켜기를 지원하지 않지만 디버그 로깅을 검색할 수 있습니다.

다음 명령을 사용하여 배포 작업을 검토합니다.

```azurecli
az deployment operation group list \
  --resource-group examplegroup \
  --name exampledeployment
```

다음 명령으로 요청 내용을 검사합니다.

```azurecli
az deployment operation group list \
  --name exampledeployment \
  --resource-group examplegroup \
  --query [].properties.request
```

다음 명령으로 응답 내용을 검사합니다.

```azurecli
az deployment operation group list \
  --name exampledeployment \
  --resource-group examplegroup \
  --query [].properties.response
```

### <a name="nested-template"></a>중첩된 템플릿

[중첩된](../templates/linked-templates.md#nested-template) ARM 템플릿에 대한 디버그 정보를 기록하려면 `debugSetting` 요소를 사용합니다.

```json
{
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2020-10-01",
  "name": "nestedTemplate",
  "properties": {
    "mode": "Incremental",
    "templateLink": {
      "uri": "{template-uri}",
      "contentVersion": "1.0.0.0"
    },
    "debugSetting": {
       "detailLevel": "requestContent, responseContent"
    }
  }
}
```

Bicep은 [Microsoft.Resources/deployments](/azure/templates/microsoft.resources/deployments)대신 [모듈을](../bicep/modules.md) 사용합니다. 모듈을 사용하면 코드를 다시 사용하여 다른 Bicep 파일에서 Bicep 파일을 배포할 수 있습니다.

## <a name="create-a-troubleshooting-template"></a>문제 해결 템플릿 만들기

경우에 따라 템플릿 문제를 해결하는 가장 쉬운 방법은 일부를 테스트해보는 것입니다. 간소화된 템플릿을 만들어 오류를 일으키는 것으로 생각되는 부분에 집중할 수 있습니다. 예를 들어 리소스를 참조할 때 오류가 발생한다고 가정합니다. 전체 템플릿을 처리하는 대신, 문제를 일으킬 수 있는 부분만 반환하는 템플릿을 만듭니다. 이렇게 하면 템플릿 함수를 바르게 사용하여, 올바른 매개 변수를 전달하고 원하는 리소스를 가져오고 있는지 확인하는 데 도움이 됩니다.

다음 ARM 템플릿 및 Bicep 파일 예제에서는 기존 스토리지 계정을 사용합니다. 매개 변수는 스토리지 계정의 이름 및 리소스 그룹을 지정합니다. Azure Cloud Shell 사용하여 [ARM 템플릿](../templates/deploy-cloud-shell.md#deploy-local-template) 또는 [Bicep 파일을](../bicep/deploy-cloud-shell.md)배포할 수 있습니다. 출력은 스토리지 계정의 속성 이름 및 값을 가진 개체입니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageName": {
      "type": "string"
    },
    "storageResourceGroup": {
      "type": "string"
    }
  },
  "variables": {},
  "resources": [],
  "outputs": {
    "exampleOutput": {
      "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageName')), '2021-04-01')]",
      "type": "object"
    }
  }
}
```

Bicep 샘플의 경우 스토리지 계정이 있는 리소스 그룹에서 배포 명령을 실행합니다.

```bicep
param storageName string

resource stg 'Microsoft.Storage/storageAccounts@2021-04-01' existing = {
  name: storageName
}

output exampleOutput object = stg.properties
```

또는 종속성을 잘못 설정하는 것과 관련되었다고 생각되는 배포 오류가 발생한다고 가정해보세요. 간소화된 템플릿을 분리하여 템플릿을 테스트합니다. 먼저 단일 리소스(예: SQL Server)를 배포하는 템플릿을 만듭니다. 리소스가 올바르게 정의된 것이 확실하면 종속되는 리소스(예: SQL Database)를 추가합니다. 이러한 두 리소스가 올바르게 정의되어 있으면 다른 종속 리소스(예: 감사 정책)를 추가합니다. 각 테스트 배포 간에 리소스 그룹을 삭제하여 해당 리소스 그룹을 적절하게 테스트하고 있는지 확인합니다.

## <a name="next-steps"></a>다음 단계

- ARM 템플릿 문제 해결에 대한 자세한 내용은 [빠른 시작: ARM 템플릿 배포 문제 해결을 참조하세요.](quickstart-troubleshoot-arm-deployment.md)
- Bicep 파일 문제 해결에 대한 자세한 내용은 [빠른 시작: Bicep 파일 배포 문제 해결을 참조하세요.](quickstart-troubleshoot-bicep-deployment.md)
- 배포 중 오류를 확인하는 작업에 대해 알아보려면 [배포 작업 보기](../templates/deployment-history.md)를 참조하세요.
