---
title: 포함 파일
description: 파일
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: mingshen-ms
ms.author: krsh
ms.date: 11/10/2021
ms.openlocfilehash: 921e441a783d1f7bf4dc68b9decf03d6b23fbdb8
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132354089"
---
## <a name="generalize-the-image"></a>이미지 일반화

Azure Marketplace 모든 이미지는 일반적인 방식으로 재사용할 수 있어야 합니다. 이를 달성하려면 운영 체제 VHD가 일반화되어야 합니다. 이 작업은 VM에서 인스턴스 관련 식별자와 소프트웨어 드라이버를 모두 제거합니다.

### <a name="for-windows"></a>Windows의 경우

Windows OS 디스크는 [sysprep](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview) 도구를 사용하여 일반화됩니다. 나중에 OS를 업데이트하거나 재구성하는 경우 sysprep을 다시 실행해야 합니다.

> [!WARNING]
> sysprep을 실행한 후 업데이트가 자동으로 실행될 수 있으므로 배포될 때까지 VM을 끕니다. 이렇게 종료하면 이후 업데이트에서 운영 체제 또는 설치된 서비스에 대한 인스턴스 관련 변경이 수행되지 않습니다. sysprep 실행에 대한 자세한 내용은 [Windows VM 일반화](../../virtual-machines/generalize.md#windows)를 참조하세요.

### <a name="for-linux"></a>Linux의 경우

1. Azure Linux 에이전트를 제거합니다.
    1. SSH 클라이언트를 사용하여 Linux VM에 연결합니다.
    2. SSH 창에서 다음 명령을 입력합니다. `sudo waagent –deprovision+user`
    3. Y를 입력하여 계속합니다(확인 단계를 방지하려면 이전 명령에 -force 매개 변수를 추가할 수 있음).
    4. 명령이 완료되면 **종료** 를 입력하여 SSH 클라이언트를 닫습니다.
2. 가상 머신을 중지합니다.
    1. Azure Portal에서 RG(리소스 그룹)를 선택하고, VM을 할당 취소합니다.
    2. 이제 VM이 일반화되어 이 VM을 사용하여 새 VM을 만들 수 있습니다.

### <a name="capture-image"></a>이미지 캡처

> [!NOTE]
> 게시하려면 Azure Compute 갤러리를 포함하는 Azure 구독이 게시자 계정과 동일한 테넌트 아래에 있어야 합니다. 또한 게시자 계정에는 Azure Compute 갤러리가 포함된 구독에 대한 기여자 이상의 액세스 권한이 있어야 합니다.

VM이 준비되면 Azure Compute 갤러리(이전의 Shared Image Gallery)에서 캡처할 수 있습니다. 캡처하려면 다음 단계를 따릅니다.

1. [Azure Portal](https://ms.portal.azure.com/)에서 Virtual Machine의 페이지로 이동합니다.
2. **캡처** 를 선택합니다.
3. **Azure Compute 갤러리에 이미지 공유** 아래에서 예를 선택하고 **갤러리에 이미지 버전으로 공유합니다.**
4. **운영 체제 상태** 에서 일반화됨을 선택합니다.
5. 대상 이미지 갤러리나 **새로 만들기** 를 선택합니다.
6. 대상 이미지 정의나 **새로 만들기** 를 선택합니다.
7. 이미지에 대한 **버전 번호** 를 제공합니다.
8. **검토 + 만들기** 를 선택하여 선택 사항을 검토합니다.
9. 유효성 검사를 통과하면 **만들기** 를 선택합니다.

## <a name="set-the-right-permissions"></a>올바른 권한 설정

파트너 센터 계정이 Azure Compute 갤러리를 호스트하는 구독의 소유자인 경우 사용 권한에 더 이상 필요하지 않습니다.

구독에 대한 읽기 권한만 있는 경우 다음 두 가지 옵션 중 하나를 사용합니다.

### <a name="option-one--ask-the-owner-to-grant-owner-permission"></a>옵션 1 – 소유자에게 소유자 권한을 부여하도록 요청

소유자가 소유자 권한을 부여하는 단계:

1. Azure Compute 갤러리로 이동합니다.
2. 왼쪽 패널에서 **액세스 제어**(IAM)를 선택합니다.
3. **추가** 를 선택한 다음 **역할 할당 추가** 를 선택합니다.<br>
    :::image type="content" source="../media/create-vm/add-role-assignment.png" alt-text="역할 할당 추가 창이 표시됩니다.":::
1. **역할** 에 대해 **소유자** 를 선택합니다.
1. **다음에 대한 액세스 할당** 에서 **사용자, 그룹 또는 서비스 주체** 를 선택합니다.
1. **선택** 에서 이미지를 게시할 사용자의 Azure 이메일을 입력합니다.
1. **저장** 을 선택합니다.

### <a name="option-two--run-a-command"></a>옵션 2 – 명령 실행

소유자에게 이러한 명령 중 하나를 실행하도록 요청합니다(두 경우 모두 Azure Compute 갤러리를 만든 구독의 SusbscriptionId 사용).

```azurecli
az login
az provider register --namespace Microsoft.PartnerCenterIngestion --subscription {subscriptionId}
```
 
```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionId {subscriptionId}
Register-AzResourceProvider -ProviderNamespace Microsoft.PartnerCenterIngestion
```

> [!NOTE]
> 이제 파트너 센터 Azure Compute 갤러리 이미지를 게시할 수 있기 때문에 SAS URI를 생성할 필요가 없습니다. 그러나 SAS URI 생성 단계를 계속 참조해야 하는 경우 [VM 이미지에 대한 SAS URI를 생성하는 방법](../azure-vm-get-sas-uri.md)을 참조하세요.
