---
title: 게스트 구성 할당 리소스 이해
description: 게스트 구성은 구성을 컴퓨터에 매핑하는 게스트 구성 할당이라는 확장 리소스를 만듭니다.
ms.date: 08/15/2021
ms.topic: conceptual
ms.openlocfilehash: c106ca492166cc604607bfc8da3f9c7d7b3bff2d
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122773109"
---
# <a name="understand-guest-configuration-assignment-resources"></a>게스트 구성 할당 리소스 이해

Azure Policy가 할당될 때 “게스트 구성” 범주에 있다면 게스트 할당을 설명하는 메타데이터가 포함됩니다.

[이 문서의 동영상 연습 사용 가능](https://youtu.be/DmCphySEB7A).

게스트 할당은 컴퓨터와 Azure Policy 시나리오 사이를 연결하는 것으로 생각할 수 있습니다. 예를 들어 다음 코드 조각은 Azure Windows Baseline 구성을 최소 버전 `1.0.0`으로 정책 범위에 있는 모든 머신에 연결합니다.

```json
"metadata": {
    "category": "Guest Configuration",
    "guestConfiguration": {
        "name": "AzureWindowsBaseline",
        "version": "1.*"
    }
//additional metadata properties exist
```

## <a name="how-azure-policy-uses-guest-configuration-assignments"></a>Azure Policy가 게스트 구성 할당을 사용하는 방법

메타데이터 정보는 게스트 구성 서비스에서 **AuditIfNotExists** 또는 **DeployIfNotExists** 정책 효과가 있는 정의 파일에 대한 감사 리소스를 자동으로 만드는 데 사용됩니다. 리소스 종류는 `Microsoft.GuestConfiguration/guestConfigurationAssignments`입니다. Azure Policy는 게스트 할당 리소스의 **complianceStatus** 속성을 사용하여 규정 준수 상태를 보고합니다. 자세한 내용은 [규정 준수 데이터 가져오기](../how-to/get-compliance-data.md)를 참조하세요.

### <a name="deletion-of-guest-assignments-from-azure-policy"></a>Azure Policy에서 게스트 할당 삭제

Azure Policy 할당이 삭제될 때 정책에서 게스트 구성 할당을 만든 경우, 게스트 구성 할당 또한 삭제됩니다.

## <a name="manually-creating-guest-configuration-assignments"></a>수동으로 게스트 구성 할당 만들기

Azure Resource Manager의 게스트 할당 리소스는 Azure Policy 또는 모든 클라이언트 SDK에서 만들 수 있습니다.

배포 템플릿 예제:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "apiVersion": "2021-01-25",
      "type": "Microsoft.Compute/virtualMachines/providers/guestConfigurationAssignments",
      "name": "myMachine/Microsoft.GuestConfiguration/myConfig",
      "location": "westus2",
      "properties": {
        "guestConfiguration": {
          "name": "myConfig",
          "contentUri": "https://mystorageaccount.blob.core.windows.net/mystoragecontainer/myConfig.zip?sv=SASTOKEN",
          "contentHash": "SHA256HASH",
          "version": "1.0.0",
          "assignmentType": "ApplyAndMonitor",
          "configurationParameter": {}
        }
      }
    }    
  ]
}
```

다음 표에서는 게스트 할당 리소스의 각 속성에 대해 설명합니다.

| 속성 | Description |
|-|-|
| name | 콘텐츠 패키지 MOF 파일 내의 구성 이름. |
| contentUri | 콘텐츠 패키지(.zip)에 대한 HTTPS URI 경로입니다. |
| contentHash | 콘텐츠 패키지의 SHA256 해시 값으로 콘텐츠 패키지가 변경되지 않았는지 확인하는 데 사용됩니다. |
| 버전 | 콘텐츠 패키지의 버전. 기본 제공 패키지에만 사용되며 사용자 지정 콘텐츠 패키지에는 사용되지 않습니다. |
| assignmentType | 할당의 동작. 허용되는 값: `Audit`, `ApplyandMonitor` 및 `ApplyandAutoCorrect`. |
| configurationParameter | 콘텐츠 패키지 MOF 파일이 컴퓨터에 다운로드된 후 재정의될 DSC 리소스 종류, 이름, 값의 목록. |

### <a name="deletion-of-manually-created-guest-configuration-assignments"></a>수동으로 만든 게스트 구성 할당 삭제

모든 수동 접근 방식(Azure Resource Manager 템플릿 배포 등)을 통해 만든 게스트 구성 할당은 수동으로 삭제해야 합니다.
상위 리소스(가상 머신 또는 Arc 지원 컴퓨터)를 삭제하면 게스트 구성 할당 또한 삭제됩니다.

게스트 구성 할당을 수동으로 삭제하려면 다음 예제를 사용하세요. "\<\>" 대괄호로 표시된 모든 예제 문자열을 바꿔야 합니다.

```PowerShell
# First get details about the guest configuration assignment
$resourceDetails = @{
  ResourceGroupName = '<myResourceGroupName>'
  ResourceType      = 'Microsoft.Compute/virtualMachines/providers/guestConfigurationAssignments/'
  ResourceName      = '<myVMName>/Microsoft.GuestConfiguration'
  ApiVersion        = '2020-06-25'
}
$guestAssignment = Get-AzResource @resourceDetails

# Review details of the guest configuration assignment
$guestAssignment

# After reviewing properties of $guestAssignment to confirm
$guestAssignment | Remove-AzResource
```

## <a name="next-steps"></a>다음 단계

- [게스트 구성 개요](./guest-configuration.md)를 참조하세요.
- 사용자 지정 게스트 구성 패키지 [개발 환경](../how-to/guest-configuration-create-setup.md)을 설정합니다.
- 게스트 구성을 위한 [패키지 아티팩트를 만듭니다](../how-to/guest-configuration-create.md).
- 사용자의 개발 환경에서 [패키지 아티팩트를 테스트](../how-to/guest-configuration-create-test.md)합니다.
- `GuestConfiguration`모듈을 사용하여 사용자 환경을 대규모로 관리하기 위한 [Azure Policy 정의를 만듭니다](../how-to/guest-configuration-create-definition.md).
- Azure Portal을 사용하여 [사용자 지정 정책 정의를 할당합니다](../assign-policy-portal.md).
- [게스트 구성에 대한 규정 준수 세부 정보](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration) 정책 할당을 보는 방법을 알아봅니다.
