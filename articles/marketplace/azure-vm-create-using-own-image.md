---
title: 사용자 고유의 이미지를 사용하여 Azure Marketplace에서 Azure 가상 머신 제품 만들기
description: 사용자 고유의 이미지를 사용하여 Azure Marketplace에 가상 머신 제품을 게시합니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: krsh
ms.author: krsh
ms.date: 07/22/2021
ms.openlocfilehash: 2ff120fbc95fc96bf39f1648f14a5bdf75ee0db2
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2021
ms.locfileid: "114601157"
---
# <a name="create-a-virtual-machine-using-your-own-image"></a>사용자 고유의 이미지를 사용하여 가상 머신 만들기

이 문서에서는 온-프레미스에서 작성한 VM(가상 머신) 이미지를 게시하는 방법을 설명합니다.

## <a name="bring-your-image-into-azure"></a>이미지를 Azure로 가져오기

Azure 공유 이미지 갤러리에 VHD를 업로드합니다.

1. Azure Portal에서 **공유 이미지 갤러리** 를 검색합니다.
2. 기존 공유 이미지 갤러리를 만들거나 사용합니다. Marketplace에 게시되는 이미지에 대한 별도의 공유 이미지 갤러리를 만드는 것이 좋습니다.
3. 기존 이미지 정의를 만들거나 사용합니다.
4. **버전 만들기** 를 선택합니다.
5. 지역 및 이미지 버전을 선택합니다.
6. VHD가 아직 Azure Portal에 업로드되지 않은 경우 **스토리지 Blob(VHD)** 을 **원본** 으로 선택한 다음 **찾아보기** 를 선택합니다. **스토리지 계정** 및 **스토리지 컨테이너** 를 만들지 않은 경우 만들 수 있습니다. VHD를 업로드합니다.
7. **검토 + 만들기** 를 선택합니다. 유효성 검사가 완료되면 **만들기** 를 선택합니다.

> [!TIP]
> SIG 이미지를 게시하려면 게시자 계정에 "소유자" 액세스 권한이 있어야 합니다. 필요한 경우 **올바른 권한 설정** 섹션의 단계에 따라 액세스 권한을 부여하세요.

## <a name="set-the-right-permissions"></a>올바른 권한 설정

파트너 센터 계정이 Shared Image Gallery를 호스팅하는 구독의 소유자인 경우 추가 권한이 더 이상 필요하지 않습니다.

구독에 대한 읽기 권한만 있는 경우 다음 두 가지 옵션 중 하나를 사용합니다.

### <a name="option-one--ask-the-owner-to-grant-owner-permission"></a>옵션 1 – 소유자에게 소유자 권한을 부여하도록 요청

소유자가 소유자 권한을 부여하는 단계:

1. SIG(Shared Image Gallery)로 이동합니다.
2. 왼쪽 패널에서 **액세스 제어**(IAM)를 선택합니다.
3. **추가** 를 선택한 다음 **역할 할당 추가** 를 선택합니다.<br>
    :::image type="content" source="media/create-vm/add-role-assignment.png" alt-text="역할 할당 추가 창이 표시됩니다.":::
1. **역할** 에 대해 **소유자** 를 선택합니다.
1. **다음에 대한 액세스 할당** 에서 **사용자, 그룹 또는 서비스 주체** 를 선택합니다.
1. **선택** 에서 이미지를 게시할 사용자의 Azure 이메일을 입력합니다.
1. **저장** 을 선택합니다.

### <a name="option-two--run-a-command"></a>옵션 2 – 명령 실행

소유자에게 이러한 명령 중 하나를 실행하도록 요청합니다(두 경우 모두 공유 이미지 갤러리를 만든 구독의 SusbscriptionId 사용).

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
> 이제 API를 사용하지 않고 파트너 센터에서 SIG(공유 이미지 갤러리) 이미지를 게시할 수 있으므로 SAS URI를 생성할 필요가 없습니다. <br/> <br/>API를 사용하여 게시하는 경우 SIG를 사용하는 대신 SAS URI를 생성해야 합니다. [VM 이미지에 대한 SAS URI를 생성하는 방법](azure-vm-get-sas-uri.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [VM 이미지를 테스트](azure-vm-image-test.md)하여 Azure Marketplace 게시 요구 사항을 충족하는지 확인합니다(선택 사항).
- VM 이미지를 테스트하지 않으려면 [파트너 센터](https://go.microsoft.com/fwlink/?linkid=2165935)에 로그인하여 SIG 이미지를 게시합니다.
- 새 Azure 기반 VHD를 만드는 데 어려움이 있는 경우에는 [Azure Marketplace용 VM FAQ](azure-vm-create-faq.yml)를 참조하세요.
