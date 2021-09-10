---
title: '미리 보기: 신뢰할 수 있는 시작 VM 배포'
description: 신뢰할 수 있는 시작을 사용하는 VM을 배포합니다.
author: khyewei
ms.author: khwei
ms.reviewer: cynthn
ms.service: virtual-machines
ms.subservice: trusted-launch
ms.topic: how-to
ms.date: 04/06/2021
ms.custom: template-how-to
ms.openlocfilehash: bcc91283c29aaef251c2a18422e90090c56a6298
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122688921"
---
# <a name="deploy-a-vm-with-trusted-launch-enabled-preview"></a>신뢰할 수 있는 시작을 사용하도록 설정된 VM 배포(미리 보기)

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합

[신뢰할 수 있는 시작](trusted-launch.md)은 [2세대](generation-2.md) VM의 보안을 개선하는 방법입니다. 신뢰할 수 있는 시작은 vTPM 및 보안 부팅 등의 인프라 기술을 결합하여 고급 및 지속적인 공격 기법을 방지합니다.

> [!IMPORTANT]
> 신뢰할 수 있는 시작은 현재 공개 미리 보기로 제공됩니다.
>
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
>
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="deploy-using-the-portal"></a>Portal을 사용하여 배포

신뢰할 수 있는 시작이 사용하도록 설정된 가상 머신을 만듭니다.

1. Azure [Portal](https://aka.ms/TL_preview)에 로그인합니다.
   > [!NOTE]
   > 포털 링크는 신뢰할 수 있는 시작 미리 보기에 대해 고유합니다.
   >
2. **Virtual Machines** 를 검색합니다.
3. **서비스** 에서 **가상 머신** 을 선택합니다.
4. **가상 머신** 페이지에서 **추가** 를 선택한 다음, **가상 머신** 을 선택합니다.
5. **프로젝트 세부 정보** 에서 올바른 구독이 선택되어 있는지 확인합니다.
6. **리소스 그룹** 에서 **새로 만들기** 를 선택하고 리소스 그룹의 이름을 입력하거나 드롭다운 목록에서 기존 리소스 그룹을 선택합니다.
7. **인스턴스 세부 정보** 에서 가상 머신 이름에 대한 이름을 입력하고 [신뢰할 수 있는 시작](trusted-launch.md#public-preview-limitations)을 지원하는 지역을 선택합니다.
8. **이미지** 에서 Gen 2 [신뢰할 수 있는 실행을 지원하는 이미지](trusted-launch.md#public-preview-limitations)를 선택합니다. 다음 메시지가 표시되는지 확인합니다. **이 이미지는 신뢰할 수 있는 실행 미리 보기를 지원합니다. 고급 탭에서 구성합니다**.
   > [!TIP]
   > 드롭다운에 원하는 이미지의 Gen 2 버전이 표시되지 않으면 **모든 이미지 보기** 를 선택한 다음 Gen 2 이미지만 표시하도록 **VM 세대** 필터를 변경합니다. 목록에서 이미지를 찾은 다음 **선택** 드롭다운을 사용하여 Gen 2 버전을 선택합니다.

    :::image type="content" source="media/trusted-launch/gen-2-image.png" alt-text="신뢰할 수 있는 시작을 지원하는 gen2 이미지임을 확인하는 메시지를 보여 주는 스크린샷":::

13. 신뢰할 수 있는 시작을 지원하는 VM 크기를 선택합니다. [지원되는 크기](trusted-launch.md#public-preview-limitations) 목록을 참조하세요.
14. **관리자 계정** 정보를 입력한 다음, **인바운드 포트 규칙** 을 입력합니다.
1. 페이지 위쪽에서 선택하여 **고급** 탭으로 전환합니다.
1. **VM 생성** 섹션까지 아래로 스크롤합니다. **Gen 2** 가 선택되었는지 확인합니다.
1. 계속 **고급** 탭에서 **신뢰할 수 있는 시작** 으로 스크롤한 다음, **신뢰할 수 있는 실행** 확인란을 선택합니다. 그러면 보안 부팅 및 vTPM이라는 두 가지 추가 옵션이 표시됩니다. 배포에 적합한 옵션을 선택합니다.

    :::image type="content" source="media/trusted-launch/trusted-launch-portal.png" alt-text="신뢰할 수 있는 시작 옵션을 보여 주는 스크린샷":::

15. 페이지의 아래쪽에서 **검토 + 생성** 을 선택합니다.
16. **가상 머신 만들기** 페이지에서 배포하려는 VM의 세부 정보를 볼 수 있습니다. 준비가 되면 **만들기** 를 선택합니다.

    :::image type="content" source="media/trusted-launch/validation.png" alt-text="신뢰할 수 있는 시작 옵션이 포함되어 있는 유효성 검사 페이지의 스크린샷":::


VM 배포에는 몇 분 정도 걸립니다.

## <a name="deploy-using-a-template"></a>템플릿을 사용하여 배포

빠른 시작 템플릿을 사용하여 신뢰할 수 있는 시작 VM을 배포할 수 있습니다.

**Linux**: [![Azure에 배포](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.compute%2Fvm-trustedlaunch-linux%2Fazuredeploy.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.compute%2Fvm-trustedlaunch-linux%2FcreateUiDefinition.json)

**Windows**: [![Azure에 배포](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.compute%2Fvm-trustedlaunch-windows%2Fazuredeploy.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.compute%2Fvm-trustedlaunch-windows%2FcreateUiDefinition.json)

## <a name="view-and-update"></a>보기 및 업데이트

포털에서 VM에 대한 **개요** 페이지를 방문하여 기존 VM에 대한 신뢰할 수 있는 시작 구성을 볼 수 있습니다.

신뢰할 수 있는 시작 구성을 변경하려면 왼쪽 메뉴의 **설정** 섹션에서 **구성** 을 선택합니다. **신뢰할 수 있는 시작** 섹션에서 보안 부팅 및 vTPM을 사용하거나 사용하지 않도록 설정할 수 있습니다. 완료되면 페이지 맨 위에 있는 **저장** 을 선택합니다.

:::image type="content" source="media/trusted-launch/configuration.png" alt-text="신뢰할 수 있는 시작 구성을 변경하는 방법의 스크린샷":::

VM이 실행 중인 경우 수정된 신뢰할 수 있는 시작 구성을 적용하기 위해 VM을 다시 시작한다는 메시지가 표시됩니다. 변경 내용을 적용하려면 **예** 를 선택한 다음, VM이 다시 시작될 때까지 기다립니다.


## <a name="verify-secure-boot-and-vtpm"></a>보안 부팅 및 vTPM 확인

가상 머신에서 보안 부팅 및 vTPM이 사용되도록 설정되었는지 확인할 수 있습니다.

### <a name="linux-validate-if-secure-boot-is-running"></a>Linux: 보안 부팅이 실행 중인지 유효성 검사

VM에 대한 SSH를 수행한 다음, 다음 명령을 실행합니다.

```bash
mokutil --sb-state
```

보안 부팅을 사용하는 경우 명령은 다음을 반환합니다.

```bash
SecureBoot enabled
```

### <a name="linux-validate-if-vtpm-is-enabled"></a>Linux: vTPM이 사용되도록 설정되었는지 유효성 검사

VM에 SSH를 수행합니다 tpm0 디바이스가 있는지 확인합니다.

```bash
ls /dev/tpm0
```

vTPM을 사용하는 경우 명령은 다음을 반환합니다.

```output
/dev/tpm0
```

vTPM을 사용하지 않는 경우 명령은 다음을 반환합니다.

```output
ls: cannot access '/dev/tpm0': No such file or directory
```

### <a name="windows-validate-that-secure-boot-is-running"></a>Windows: 보안 부팅이 실행 중인지 확인

원격 데스크톱을 사용하여 VM에 연결한 다음, `msinfo32.exe`를 실행합니다.

오른쪽 창에서 보안 부팅 상태가 **ON** 인지 확인합니다.

## <a name="enable-the-azure-security-center-experience"></a>Azure Security Center 환경 사용

Azure Security Center를 사용하여 신뢰할 수 있는 시작 VM에 대한 정보를 표시하려면 여러 정책을 사용하도록 설정해야 합니다. 정책을 사용하도록 설정하는 가장 쉬운 방법은 이 [Resource Manager 템플릿](https://github.com/prash200/azure-quickstart-templates/tree/master/101-asc-trustedlaunch-policies)을 구독에 배포하는 것입니다.

아래 단추를 선택하여 구독에 정책을 배포합니다.

[![Azure에 배포](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-asc-trustedlaunch-policies%2Fazuredeploy.json)

템플릿은 구독당 한 번만 배포해야 합니다. 지원되는 모든 VM에 `GuestAttestation` 및 `AzureSecurity` 확장이 자동으로 설치됩니다. 오류가 발생하는 경우 템플릿을 다시 배포해 보세요.

신뢰할 수 있는 시작 VM에 대한 vTPM 및 보안 부팅 권장 사항을 얻으려면 [구독에 사용자 지정 이니셔티브 추가](../security-center/custom-security-policies.md#to-add-a-custom-initiative-to-your-subscription)를 참조하세요.

## <a name="sign-things-for-secure-boot-on-linux"></a>Linux에서 보안 부팅에 대한 항목 서명

UEFI 보안 부팅을 위해 항목을 서명해야 하는 경우도 있습니다.  예를 들어 Ubuntu용 [보안 부팅에 대한 항목을 서명하는 방법](https://ubuntu.com/blog/how-to-sign-things-for-secure-boot)을 살펴봐야 할 수 있습니다. 이러한 경우 VM에 대해 MOK 유틸리티 등록 키를 입력해야 합니다. 이렇게 하려면 Azure 직렬 콘솔을 사용하여 MOK 유틸리티에 액세스해야 합니다.

1. Linux용 Azure Serial Console을 사용하도록 설정합니다. 자세한 내용은 [Linux용 직렬 콘솔](/troubleshoot/azure/virtual-machines/serial-console-linux)을 참조하세요.
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **가상 머신** 을 검색하고 목록에서 VM을 선택합니다.
1. 왼쪽 메뉴의 **지원 + 문제 해결** 아래에서 **직렬 콘솔** 을 선택합니다. 페이지는 직렬 콘솔을 사용하여 오른쪽으로 열립니다.
1. Azure 직렬 콘솔을 사용하여 VM에 로그온합니다. **로그인** 의 경우 VM을 만들 때 사용한 사용자 이름을 입력합니다. 예를 들어 *azureuser* 입니다. 메시지가 표시되면 사용자 이름과 연결된 암호를 입력합니다.
1. 로그인한 후에는 `mokutil`을 사용하여 공개 키 `.der` 파일을 가져옵니다.

    ```bash
    sudo mokutil –import <path to public key.der>
    ```
1. `sudo reboot`를 입력하여 Azure 직렬 콘솔에서 머신을 다시 부팅합니다. 10초 카운트다운이 시작됩니다.
1. 위로 또는 아래로 키를 눌러 카운트다운을 중단하고 UEFI 콘솔 모드에서 대기합니다. 타이머가 중단되지 않으면 부팅 프로세스가 계속되고 모든 MOK 변경 내용이 손실됩니다.
1. MOK 유틸리티 메뉴에서 적절한 작업을 선택합니다.

    :::image type="content" source="media/trusted-launch/mok-mangement.png" alt-text="직렬 콘솔에서 MOK 관리 메뉴에 사용할 수 있는 옵션을 보여 주는 스크린샷":::


## <a name="next-steps"></a>다음 단계

[신뢰할 수 있는 시작](trusted-launch.md) 및 [2세대](generation-2.md) VM에 대해 자세히 알아보세요.
