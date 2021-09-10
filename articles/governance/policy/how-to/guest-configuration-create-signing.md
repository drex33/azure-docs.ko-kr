---
title: 게스트 구성 패키지 서명 방법
description: 필요에 따라 게스트 구성 콘텐츠 패키지에 서명하고 에이전트에서 서명된 콘텐츠만 허용하도록 강제할 수 있습니다.
ms.date: 07/12/2021
ms.topic: how-to
ms.openlocfilehash: 7c7d120f17e32b3ea9accb6697ac66f4afcbb879
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122773112"
---
# <a name="how-to-sign-guest-configuration-packages"></a>게스트 구성 패키지 서명 방법

게스트 구성 사용자 지정 정책은 SHA256 해시를 사용하여 변경되지 않은 정책 패키지의 유효성을 검사합니다. 또한 필요에 따라 고객은 인증서를 사용하여 패키지에 서명하고 게스트 구성 확장에서 강제로 서명된 콘텐츠만 허용하도록 할 수 있습니다.

이 시나리오를 사용하려면 두 단계를 완료해야 합니다. cmdlet을 실행하여 콘텐츠 패키지에 서명하고 코드에 서명해야 하는 컴퓨터에 태그를 추가합니다.

## <a name="signature-validation-using-a-code-signing-certificate"></a>코드 서명 인증서를 사용하여 서명 유효성 검사

서명 유효성 검사 기능을 사용하려면 `Protect-GuestConfigurationPackage` cmdlet을 실행하여 게시하기 전 패키지에 서명합니다. 이 cmdlet에는 '코드 서명' 인증서가 필요합니다.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert") }
Protect-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Certificate $Cert -Verbose
```

`Protect-GuestConfigurationPackage` cmdlet의 매개 변수는 다음과 같습니다.

- **경로**: 게스트 구성 패키지의 전체 경로입니다.
- **인증서**: 패키지에 서명하기 위한 코드 서명 인증서입니다. 이 매개 변수는 Windows 콘텐츠에 서명하는 경우에만 지원됩니다.

## <a name="certificate-requirements"></a>인증서 요구 사항

GuestConfiguration 에이전트는 인증서 공개 키가 Windows 컴퓨터의 "신뢰할 수 있는 루트 인증서 인증 기관" 및 Linux 컴퓨터의 `/usr/local/share/ca-certificates/extra` 경로에 있다고 간주합니다. 노드가 서명된 콘텐츠를 확인하려면 사용자 지정 정책을 적용하기 전에 컴퓨터에 인증서 공개 키를 설치합니다. 이 프로세스는 VM 내의 모든 기술 또는 Azure Policy를 사용하여 수행할 수 있습니다. 예제 템플릿은 [인증서가 있는 머신을 배포](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-push-certificate-windows)하는 데 사용할 수 있습니다.
Key Vault 액세스 정책은 배포하는 동안 Compute 리소스 공급자가 인증서에 액세스할 수 있도록 허용해야 합니다. 자세한 단계는 [Azure Resource Manager에서 가상 머신에 대한 Key Vault 설정](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault)을 참조하세요.

다음은 서명 인증서에서 공개 키를 내보내 컴퓨터로 가져오는 예입니다.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert3") } | Select-Object -First 1
$Cert | Export-Certificate -FilePath "$env:temp\DscPublicKey.cer" -Force
```

## <a name="tag-requirements"></a>태그 요구 사항

콘텐츠를 게시한 후에는 이름이 `GuestConfigPolicyCertificateValidation`이고 값이 `enabled`인 태그를 코드 서명이 필요한 모든 가상 머신에 추가합니다. Azure Policy를 사용하여 태그를 대규모로 제공할 수 있는 방법은 [태그 샘플](../samples/built-in-policies.md#tags)을 참조하세요. 이 태그가 준비되면 `New-GuestConfigurationPolicy` cmdlet을 이용하여 생성된 정책 정의에서 게스트 구성 확장을 통해 요구 사항을 사용합니다.

## <a name="next-steps"></a>다음 단계

- 사용자 개발 환경에서 [패키지 아티팩트 테스트](./guest-configuration-create-test.md)합니다.
- 머신에서 액세스할 수 있도록 [패키지 아티팩트를 게시](./guest-configuration-create-publish.md)합니다.
- `GuestConfiguration` 모듈을 사용하여 사용자 환경의 대규모 관리를 위한 [Azure Policy 정의를 생성](./guest-configuration-create-definition.md)합니다.
- Azure Portal을 사용하여 [사용자 지정 정책 정의를 할당](../assign-policy-portal.md)합니다.
- [게스트 구성 정책 할당에 대한 규정 준수 세부 정보](./determine-non-compliance.md#compliance-details-for-guest-configuration)를 보는 방법을 알아봅니다.
