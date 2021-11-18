---
title: VM 시험판 구성
description: 파트너 센터 VM 시험판 드라이브를 구성합니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/15/2021
ms.openlocfilehash: 74f9017829789f073f8efc5850755e611eb4b788
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132730610"
---
# <a name="configure-a-vm-test-drive"></a>VM 시험판 구성

시험 사용은 고객이 고정된 시간 동안 미리 구성된 환경에 대한 액세스 권한을 부여하여 구매하기 전에 제안을 시도할 수 있도록 하여 잠재 고객을 정규화하고 전환율을 높일 수 있습니다.

VM 제안의 경우 ARM(Azure Resource Manager) 배포가 사용 가능한 **유일한** 시험 사용 옵션입니다. 배포 템플릿에는 솔루션을 구성하는 모든 Azure 리소스가 포함되어야 합니다.

왼쪽 탐색 메뉴에서 **시험 실행** 탭을 보려면 [제안 설정](azure-vm-offer-setup.md#test-drive-optional) 페이지에서 시험 **실행** 확인란을 선택하고 CRM 시스템에 연결합니다. **저장을** 선택하면 두 개의 하위 탭이 있는 **시험판** 탭이 나타납니다.

- **[기술 구성](#technical-configuration)** – 시험 구동을 구성하고 ARM 템플릿을 제공합니다(아래 다음 섹션).
- **[Marketplace 목록](#marketplace-listing)** – 사용자 설명서 및 비디오와 같은 고객 목록 및 추가 리소스에 대한 추가 세부 정보를 제공합니다.

## <a name="technical-configuration"></a>기술 구성

### <a name="regions"></a>영역

**지역 편집을** 선택하고 시험 사용 가능할 각 지역에 대한 확인란을 선택합니다. 또는 오른쪽 위에서 **Select all** 사용하거나 모든 링크를 적절하게 **선택 취소합니다.** 최상의 성능을 위해 가장 많은 수의 고객이 배치될 것으로 예상되는 지역만 선택하고 구독에서 필요한 모든 리소스를 배포할 수 있는지 확인합니다. 지역 선택을 마쳤으면 **저장을** 선택합니다.

### <a name="instances"></a>인스턴스

상자에 0-99 사이의 값을 입력하여 지역별로 사용할 수 있는 핫, 웜 또는 콜드 인스턴스 수를 나타냅니다. 지정한 각 인스턴스 유형의 수에 제안을 사용할 수 있는 지역 수를 곱합니다.

- **핫** – 배포를 기다리지 않고 고객이 즉시 액세스할 수 있도록 항상 실행되고(< 10초 획득 시간) 미리 배포된 인스턴스입니다. 대부분의 고객은 전체 배포를 기다리지 않기 때문에 핫 인스턴스를 하나 이상 사용하는 것이 좋습니다. 그렇지 않으면 고객 사용량이 감소할 수 있습니다. 핫 인스턴스는 항상 Azure 구독에서 실행되므로 더 큰 가동 시간 비용이 발생합니다.
- **웜** – 스토리지에 배치되는 미리 배포된 인스턴스입니다. 핫 인스턴스보다 비용이 적게 들지만 고객을 위해 빠르게 다시 부팅할 수 있습니다(취득 시간 3-10분).
- **콜드** – 각 고객이 요청할 때 시험 사용 ARM 템플릿을 배포해야 하는 인스턴스입니다. 콜드 인스턴스는 핫 및 웜 인스턴스에 비해 로드 속도가 훨씬 느립니다. 대기 시간은 필요한 리소스에 따라 크게 달라집니다(최대 1.5시간). 콜드 인스턴스는 핫 인스턴스와 마찬가지로 항상 Azure 구독에서 실행되는 것에 비해 시험 사용 기간 동안에만 비용이 들기 때문에 비용 효율적입니다.

### <a name="technical-configuration-of-arm-template"></a>ARM 템플릿의 기술 구성

시험 사용용 ARM 템플릿은 솔루션을 구성하는 모든 Azure 리소스의 코딩된 컨테이너입니다. 시험사용에 필요한 ARM 배포 템플릿을 만들려면 [시험](azure-resource-manager-test-drive.md#write-the-test-drive-template)Azure Resource Manager 참조하세요. 템플릿이 완료되면 여기로 돌아와 ARM 템플릿을 업로드하고 구성을 완료하는 방법을 알아봅니다.

성공적으로 게시하려면 ARM 템플릿 서식의 유효성을 검사하는 것이 중요합니다. 이 작업을 수행하는 두 가지 방법은 [온라인 API 도구를](/rest/api/resources/deployments/validate) 사용하거나 [테스트 배포를](/azure/azure-resource-manager/templates/deploy-portal)사용하는 것입니다. 템플릿을 업로드할 준비가 되면 .zip 파일을 표시된 영역으로 끌거나 파일을 **찾습니다.**

시험 **사용** 기간(시간)을 입력합니다. 시험 사용이 활성 상태로 유지되는 시간입니다. 이 기간이 끝나면 시험 사용이 자동으로 종료됩니다.

### <a name="deployment-subscription-details"></a>배포 구독 세부 정보

Microsoft에서 사용자 대신 시험 사용 을 배포하려면 아래 단계를 완료하여 Azure 구독 및 Azure Active Directory(AAD)에 연결한 다음, **초안 저장을** 선택합니다.

1. **Azure 구독 ID** – Azure 서비스 및 Azure Portal 대한 액세스 권한을 부여합니다. 해당 구독에서 리소스 사용량이 보고되며 서비스 대금이 청구됩니다. 시험 사용에 사용할 [별도의 Azure 구독이](/azure/cost-management-billing/manage/create-subscription) 없는 경우 만드는 것이 좋습니다. Azure Portal 로그인하고 검색 창에서 구독을 검색하여 Azure 구독 ID를 찾을 수 *있습니다.*
2. **Azure AD 테넌트 ID** – Azure Portal 내에서 속성 디렉터리 ID를 Azure Active Directory AD(Azure Active Directory) 테넌트   >    >  **ID를** 입력합니다. 테넌트 ID가 없는 경우 Azure Active Directory에서 새로 만듭니다. 테넌트 설정에 대한 도움말은 [빠른 시작: 테넌트 설정](/azure/active-directory/develop/quickstart-create-new-tenant?branch=main)을 참조하세요.
3. 다른 필드를 계속 진행하기 전에 Microsoft Test-Drive 애플리케이션을 테넌트로 프로비전합니다. 이 애플리케이션을 사용하여 시험 사용 리소스에 대한 작업을 수행합니다.
    1. 아직 없는 경우 [Azure Az PowerShell 모듈을](/powershell/azure/install-az-ps?branch=main&view=azps-6.6.0)설치합니다.
    2. Microsoft Test-Drive 애플리케이션에 대한 서비스 주체를 추가합니다.
        1. 를 실행하고 `Connect-AzAccount` 자격 증명을 제공하여 Azure 계정에 로그인하려면 Azure Active Directory **전역 관리자** 기본 제공 [역할](/azure/active-directory/roles/permissions-reference?branch=main)이 필요합니다.
        2. 새 서비스 주체 를 `New-AzADServicePrincipal -ApplicationId d7e39695-0b24-441c-a140-047800a05ede -DisplayName 'Microsoft TestDrive' -SkipAssignment` 만듭니다.
        3. 서비스 주체가 생성되었는지 `Get-AzADServicePrincipal -DisplayName 'Microsoft TestDrive'` 확인합니다.
            :::image type="content" source="media/test-drive/commands-to-verify-service-principal.png" alt-text="보안 주체가 만들어졌는지 확인하는 방법을 보여줍니다.":::
1. **Azure AD 앱 ID** - Microsoft Test-Drive 애플리케이션을 테넌트로 프로비전한 후 애플리케이션 ID 에 붙여넣습니다. `d7e39695-0b24-441c-a140-047800a05ede`  
1. **Azure AD 앱 클라이언트 암호** – 비밀이 필요하지 않습니다. 더미 비밀(예: "비밀 없음")을 삽입합니다.
1. 애플리케이션을 사용하여 구독에 배포하므로 구독에 대한 기여자로 애플리케이션을 추가해야 합니다. Azure Portal 또는 PowerShell을 사용하여 이 작업을 수행합니다.

    **방법 1: Azure Portal**

    1. 시험 사용 시 사용할 구독을 선택합니다.
    2. **액세스 제어(IAM)** 를 선택합니다.
    3. 주 창에서 **역할 할당** 탭을 선택한 다음, **+추가를** 선택하고 드롭다운 메뉴에서 **+ 역할 할당 추가를** 선택합니다.
    4. Azure AD 애플리케이션 `Microsoft TestDrive` 이름()을 입력합니다. **기여자** 역할을 할당하려는 애플리케이션을 선택합니다.
    5. **저장** 을 선택합니다.

    **방법 2: PowerShell**

    1. ServicePrincipal object-id를 얻으려면 다음을 `(Get-AzADServicePrincipal -DisplayName 'Microsoft TestDrive').id` 실행합니다.
    2. ObjectId 및 구독 ID 를 통해 `New-AzRoleAssignment -ObjectId <objectId> -RoleDefinitionName Contributor -Scope /subscriptions/<subscriptionId>` 실행합니다.

왼쪽 탐색 메뉴인 **Marketplace 목록의** 다음 **시험사용** 탭으로 계속 진행하여 시험 사용하기 솔루션을 완료합니다.

## <a name="marketplace-listing"></a>Marketplace 목록

고객의 목록 및 리소스에 대한 추가 세부 정보를 제공합니다.

**설명** – 시험 사용, 시연할 내용, 탐색할 기능, 사용자가 실험할 목표 및 제안이 적합한지 확인하는 데 도움이 되는 기타 관련 정보(최대 3,000자)를 설명합니다.

**액세스 정보** – 시험 사용 전체(최대 10,000자)에서 기능에 액세스하고 사용하기 위해 고객이 알아야 할 사항에 대한 시나리오를 안내합니다.

**사용자 설명서** – 시험 사용 환경을 자세히 설명합니다. 이 설명서에서는 고객이 시험 사용 경험으로 얻을 수 있는 것을 정확히 다루며 질문에 대한 참조 역할을 해야 합니다. 이름이 255자 미만인 PDF 형식이어야 합니다.

**시험 사용 데모 비디오(선택** 사항) – 링크 및 썸네일 이미지를 통해 다른 곳에서 호스트되는 비디오를 참조합니다. 비디오는 제품 기능을 성공적으로 사용하고 혜택을 강조하는 시나리오를 이해하는 방법을 포함하여 고객이 시험 사용을 더 잘 이해할 수 있도록 도와주는 좋은 방법입니다. **비디오 추가를** 선택하고 다음 정보를 포함합니다.

- **이름**
- **URL** – YouTube 또는 Vimeo만 해당
- **썸네일** – 이미지는 533x324픽셀의 PNG 형식이어야 합니다.

아래의 **다음 단계를** 계속하기 전에 **초안 저장을** 선택합니다.

## <a name="next-steps"></a>다음 단계

- [제품 검토 및 게시](review-publish-offer.md)
