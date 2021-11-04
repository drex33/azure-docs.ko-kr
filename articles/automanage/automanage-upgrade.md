---
title: Azure Automanage 머신을 최신 Automanage 버전으로 업그레이드
description: 머신을 최신 Azure Automanage 버전으로 업그레이드하는 방법 알아보기
author: mmccrory
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 10/20/2021
ms.author: memccror
ms.openlocfilehash: d7cc53c30ead02a2ca4fc40fe00ae2235016336f
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131483090"
---
# <a name="upgrade-your-machines-to-the-latest-automanage-version"></a>머신을 최신 Automanage 버전으로 업그레이드

Automanage는 2021년 11월에 새 버전의 머신 모범 사례 제품을 릴리스했습니다. 이제 새 API는 머신에 적용할 서비스 및 설정을 선택하고 선택할 수 있는 사용자 지정 프로필 만들기를 지원합니다. 또한 이 새 버전에서는 Automanage 계정이 더 이상 필요하지 않습니다. 이 문서에서는 버전과 업그레이드 방법의 차이점을 설명합니다. 

## <a name="how-to-upgrade-your-machines"></a>컴퓨터를 업그레이드하는 방법

다음은 컴퓨터를 Automanage의 최신 API 버전으로 업그레이드하는 방법에 대한 지침 집합입니다. 

### <a name="prerequisites"></a>사전 요구 사항

Azure 구독이 없는 경우 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/).

> [!NOTE]
> 평가판 계정은 이 자습서에서 사용되는 가상 머신에 액세스할 수 없습니다. 종량제 구독으로 업그레이드하세요.

> [!IMPORTANT]
> 새 Automanage 버전이 있는 구독에서 처음으로 Automanage를 사용하도록 설정하려면 다음 Azure RBAC 권한이 필요합니다. **소유자** 역할 또는 **사용자 액세스 관리자** 역할과 함께 **기여자.**

### <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.


### <a name="check-to-see-which-machines-need-to-be-upgraded"></a>업그레이드해야 하는 머신을 확인합니다.

업그레이드해야 하는 모든 컴퓨터의 상태는 **업그레이드 필요** 입니다. 자동 관리 개요 페이지에도 머신을 업그레이드해야 함을 나타내는 배너가 표시됩니다. 

:::image type="content" source="media\automanage-upgrade\overview-blade.png" alt-text="업그레이드 상태가 필요합니다.":::

### <a name="disable-automanage-machines-that-need-to-be-upgrade"></a>업그레이드해야 하는 자동 관리 컴퓨터 사용 안 함

컴퓨터가 새 Automanage 버전으로 업그레이드하려면 이전 버전의 Automanage에서 컴퓨터를 사용하지 않도록 설정해야 합니다. 머신을 사용하지 않도록 설정하려면 다음 단계를 수행합니다.
1. 사용하지 않도록 설정할 가상 머신 옆의 확인란을 선택합니다.
1. **사용 안 함** 단추를 클릭합니다.
1. **사용 안 함** 에 동의하기 전에 결과 팝업의 메시지를 자세히 읽어 보세요.

:::image type="content" source="media\automanage-upgrade\disable-automanage.png" alt-text="자동 관리 사용 안 함":::

### <a name="re-enable-automanage-on-your-machines"></a>머신에서 자동 관리 다시 사용

머신이 Automanage에서 오프로드되면 이제 Automanage를 다시 사용하도록 설정할 수 있습니다. Automanage를 다시 사용하도록 설정하면 Automanage에서 자동으로 최신 Automanage 버전을 사용합니다. 

1. **기존 VM에서 사용** 을 선택합니다.

    :::image type="content" source="media\automanage-upgrade\zero-vm-list-view.png" alt-text="기존 VM에서 사용":::

2. **구성 프로필에서** 프로필 유형을 선택합니다. **Azure 모범 사례 - 프로덕션** 또는 Azure 모범 사례 - **개발/테스트** 또는 [**사용자 지정 프로필**](virtual-machines-custom-profile.md)

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-quick-create.png" alt-text="환경을 선택합니다.":::

    > [!NOTE]
    > **프로덕션** 환경은 Azure 모범 사례 - 프로덕션 구성 **프로필에 매핑됩니다.** **개발/테스트** 환경은 **Azure 모범 사례 - 개발/테스트** 구성 프로필에 매핑됩니다. **구성 기본 설정** 를 활용한 경우 동일한 수정으로 사용자 지정 **프로필을** 만들 수 있습니다. 

3. **머신 선택** 블레이드에서 다음을 수행합니다.
    1. **구독** 및 **리소스 그룹** 을 기준으로 머신 목록을 필터링합니다.
    1. 온보딩하려는 각 가상 머신의 확인란을 선택합니다.
    1. **선택** 단추를 클릭합니다.
    > [!NOTE]
    > Azure VM과 Azure Arc 지원 서버를 모두 선택할 수 있습니다.

    :::image type="content" source="media\automanage-upgrade\existing-vm-select-machine.png" alt-text="사용 가능한 VM 목록에서 기존 VM 선택":::

4. **사용** 단추를 클릭합니다.

이제 머신이 최신 버전의 Automanage에 온보딩됩니다.

## <a name="differences-in-the-automanage-versions"></a>Automanage 버전의 차이점

### <a name="environment-and-configuration-profiles"></a>환경 및 구성 프로필
이전 버전의 Automanage에서 개발/테스트 또는 프로덕션 환경 유형을 선택했습니다. 새 버전의 Automanage에서 환경은 구성 프로필에 매핑됩니다. 구성 프로필 옵션은 Azure 모범 사례 - 개발/테스트, Azure 모범 사례 - 프로덕션, 사용자 지정 프로필입니다. **개발/테스트** 환경의 서비스 및 설정 집합은 Azure **모범 사례 - 개발/테스트** 구성 프로필에서 동일합니다. 마찬가지로 프로덕션 환경의 서비스 및 설정 집합은 **Azure 모범 사례 -** **프로덕션** 구성 프로필에서 동일합니다. 

### <a name="configuration-preferences-and-custom-profiles"></a>구성 기본 설정 및 사용자 지정 프로필
이전 버전의 Automanage에서는 **구성 기본 설정** 을 통해 설정의 하위 집합을 고객에게 적용할 수 있었습니다. 최신 버전의 Automanage에서는 온보딩하려는 각 서비스를 선택하고 선택할 수 있도록 사용자 지정이 향상되었으며 사용자 지정 프로필을 통해 서비스에 대한 일부 설정을 수정할 수 **있습니다.** 

### <a name="automanage-account-and-first-party-application"></a>Automanage 계정 및 제1자 애플리케이션
이전 버전의 Automanage에서는 Automanage 계정을 MSI로 사용하여 컴퓨터에서 작업을 미리 수행했습니다. 그러나 최신 버전의 Automanage에서 Automanage는 자동 관리 머신에서 작업을 수행하기 위해 타사 애플리케이션(애플리케이션 ID: d828acde-4b48-47f5-a6e8-52460104a052)을 사용합니다. 

이전 버전과 새 버전의 Automanage 모두에 대해 다음 권한이 필요합니다.
* 구독에서 처음으로 Automanage를 온보딩하는 경우 **사용자** **액세스 관리자** 역할과 함께 소유자 역할 또는 **참가자가** 필요합니다.
* Automanage 머신이 이미 있는 구독에서 Automanage를 온보딩하는 경우 머신이 있는 리소스 그룹에 **기여자가** 필요합니다. 
> [!NOTE]
> Automanage에 온보딩하는 컴퓨터가 컴퓨터와 다른 구독의 로그 분석 작업 영역에 이미 연결되어 있는 경우 로그 분석 작업 영역 구독에 대해 위에서 설명한 권한도 필요합니다.

## <a name="next-steps"></a>다음 단계 

FAQ에서 가장 자주 묻는 질문과 대답을 확인하세요. 

> [!div class="nextstepaction"]
> [질문과 대답](faq.yml)

