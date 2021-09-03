---
title: Azure 응용 프로그램 제품에 대한 Azure 관리되는 응용 프로그램 플랜
description: Microsoft 파트너 센터에서 상용 마켓플레이스 포털을 사용하여 새 Azure 응용 프로그램 제품에 대한 관리되는 응용 프로그램 플랜을 만드는 데 필요한 사항에 대해 알아봅니다.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/13/2021
ms.openlocfilehash: 082b943aef3f82898b80d23d33a90d3f5ec3ebc6
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122539606"
---
# <a name="plan-an-azure-managed-application-for-an-azure-application-offer"></a>Azure 응용 프로그램 제품에 대한 Azure 관리되는 응용 프로그램 플랜

Azure _관리되는 응용 프로그램_ 플랜은 Azure Marketplace에 Azure 응용 프로그램 제품을 게시하는 방법 중 하나입니다. 아직 읽어 보지 않았다면 [상업용 Marketplace를 위한 Azure 애플리케이션 제품 플랜](plan-azure-application-offer.md)을 읽어보세요.

관리되는 응용 프로그램은 Azure Marketplace를 통해 배포되고 청구되는 트랜잭션 제품입니다. 사용자에게 표시되는 목록 옵션은 ‘지금 가져오기’입니다.

다음 조건이 필요한 경우 Azure 응용 프로그램: 관리되는 응용 프로그램 플랜을 사용합니다.

- VM(가상 머신) 또는 전체 IaaS(infrastructure as a service) 기반 솔루션을 사용하여 고객을 위한 구독 기반 솔루션을 배포합니다.
- 본인 또는 고객은 솔루션을 파트너가 관리하기를 요구합니다. 예를 들어, 파트너는 시스템 통합자 또는 MSP(관리되는 서비스 공급자)일 수 있습니다.

## <a name="managed-application-offer-requirements"></a>관리되는 응용 프로그램 제품 요구 사항

| 요구 사항 | 세부 정보 |
| ------------ | ------------- |
| Azure 구독 | 관리되는 응용 프로그램은 고객의 구독에 배포되어야 하지만 타사에서 관리할 수 있습니다. |
| 청구 및 계량 | 리소스는 고객의 Azure 구독에서 제공됩니다. 종량제 지불 모델을 사용하는 VM은 Microsoft를 통해 고객과 거래되며 고객의 Azure 구독을 통해 청구됩니다. <br><br> 사용자 라이선스 필요 VM의 경우 Microsoft는 고객 구독에서 발생하는 모든 인프라 비용을 청구하지만, 사용자는 고객과 직접 소프트웨어 라이선스 요금을 거래합니다. |
| Azure 호환 VHD(가상 하드 디스크) | VM은 Windows 또는 Linux에서 빌드해야 합니다. 자세한 내용은 다음을 참조하세요.<br> * [Azure VM 기술 자산 만들기](./azure-vm-create-certification-faq.yml#address-a-vulnerability-or-an-exploit-in-a-vm-offer)(Windows VHD용).<br> *  [Azure에서 보증된 Linux 배포](../virtual-machines/linux/endorsed-distros.md)(Linux VHD용). |
| 고객 사용량 특성 | 모든 새 Azure 애플리케이션 제품은 [Azure 파트너 고객 사용량 기여](azure-partner-customer-usage-attribution.md) GUID도 포함해야 합니다. 고객 사용량 특성 및 사용 설정 방법에 대한 자세한 내용은 [Azure 파트너 고객 사용량 특성](azure-partner-customer-usage-attribution.md)을 참조하세요. |
| 배포 패키지 | 고객이 플랜을 배포할 수 있도록 하는 배포 패키지가 필요합니다. 동일한 기술 구성이 필요한 여러 플랜을 만드는 경우 동일한 패키지를 사용할 수 있습니다. 자세한 내용은 다음 섹션: 배포 패키지를 참조하세요. |
|||

> [!NOTE]
> 관리되는 응용 프로그램은 Azure Marketplace를 통해 배포 가능해야 합니다. 고객 커뮤니케이션이 중요한 경우, 잠재 고객 공유를 사용하도록 설정한 후 관심 있는 고객에게 연결해야 합니다.

## <a name="usage-of-azure-kubernetes-service-aks-and-containers-in-managed-application"></a>관리되는 애플리케이션에서 AKS(Azure Kubernetes Service) 및 컨테이너 사용

### <a name="azure-application-offers-fall-into-two-categories"></a>Azure 애플리케이션 제품은 두 가지 범주로 나뉩니다.

- 게시자가 액세스할 수 없는 솔루션 템플릿
- 배포 시 고객이 부여한 사전 정의된 권한 부여를 통해 게시자가 액세스할 수 있는 관리되는 애플리케이션

**솔루션 템플릿:** 솔루션 템플릿 제품은 고객 배포 후 게시자가 변경할 수 없습니다. 따라서 컨테이너 및 AKS(Azure Kubernetes Service) 리소스는 현재 이 제품 범주에서 허용되지 않습니다.

**관리되는 애플리케이션:** 관리되는 애플리케이션 제품을 통해 게시자는 고객 구독에서 배포 중에 생성된 리소스에 액세스하고 이를 제어할 수 있습니다. 따라서 컨테이너 및 AKS(Azure Kubernetes Service) 리소스는 이 제품 카테고리에서 *<u>잠정적으로 허용</u>* 됩니다.

### <a name="rules-and-known-issues-for-aks-and-containers-in-managed-applications"></a>관리되는 애플리케이션의 AKS 및 컨테이너에 대한 규칙 및 알려진 문제

- AKS 노드 리소스 그룹은 할당 거부를 Azure 관리되는 애플리케이션의 일부로 상속하지 않습니다. 이는 고객이 관리되는 애플리케이션에 포함될 때 AKS 리소스에 의해 생성된 AKS 노드 리소스 그룹에 대한 전체 액세스 권한을 갖는 반면 관리되는 리소스 그룹에는 적절한 거부 할당이 있음을 의미합니다.
 
- 게시자는 Helm 차트 및 기타 스크립트를 Azure 관리되는 애플리케이션의 일부로 포함할 수 있습니다. 그러나 제품은 일반적인 관리되는 애플리케이션 배포처럼 취급되며 배포 시 자동 컨테이너별 처리 또는 Helm 차트 설치는 없습니다. VM 사용자 지정 스크립트 확장 또는 Azure 배포 스크립트와 같은 일반적인 기술을 사용하여 배포 시 또는 배포 후에 관련 스크립트를 실행하는 것은 게시자의 책임입니다.
 
- 일반 Azure 관리되는 애플리케이션과 마찬가지로 솔루션이 성공적으로 배포되고 모든 구성 요소가 올바르게 구성, 보안 및 작동되도록 하는 것은 게시자의 책임입니다. 예를 들어 게시자는 자체 컨테이너 레지스트리를 이미지 원본으로 사용할 수 있지만 컨테이너 보안 및 지속적인 취약성 검사에 대한 전적인 책임이 있습니다.

> [!NOTE]
> Azure 관리되는 애플리케이션 제품의 컨테이너 및 AKS에 대한 지원은 Marketplace에서 공식 컨테이너 애플리케이션 제품 유형을 사용할 수 있게 되면 철회될 수 있습니다. 이때 새 제품 유형을 사용하여 모든 향후 제품을 게시해야 할 수 있으며 기존 제품을 새 제품 유형으로 마이그레이션하고 사용 중지해야 할 수 있습니다.

## <a name="deployment-package"></a>배포 패키지

배포 패키지에는 이 플랜에 필요한 모든 템플릿 파일이 다른 추가적인 리소스와 함께 .zip 파일로 패키징되어 있습니다.

모든 Azure 응용 프로그램은 .zip 보관 파일의 루트 폴더에 다음 두 개의 파일을 포함해야 합니다.

- [mainTemplate.json](../azure-resource-manager/managed-applications/publish-service-catalog-app.md?tabs=azure-powershell#create-the-arm-template)이라는 Resource Manager 템플릿 파일. 이 템플릿은 고객의 Azure 구독에 배포할 리소스를 정의합니다. Resource Manager 템플릿의 예는 [Azure 빠른 시작 템플릿 갤러리](https://azure.microsoft.com/resources/templates/) 또는 해당 [GitHub: Azure Resource Manager 빠른 시작 템플릿](https://github.com/azure/azure-quickstart-templates) 리포지토리를 참조하세요.
- [createUiDefinition.json](../azure-resource-manager/managed-applications/create-uidefinition-overview.md)이라는 Azure 애플리케이션 만들기 환경의 사용자 인터페이스 정의. 사용자 인터페이스에서 소비자가 매개 변수 값을 제공할 수 있도록 하는 요소를 지정합니다.

지원되는 최대 파일 크기는 다음과 같습니다.

- 전체 압축 .zip 보관 파일 크기로 최대 1GB
- .zip 보관 파일 내에서 압축되지 않은 개별 파일에 대해 최대 1GB

모든 새 Azure 애플리케이션 제품은 [Azure 파트너 고객 사용량 기여](azure-partner-customer-usage-attribution.md) GUID도 포함해야 합니다.

## <a name="azure-regions"></a>Azure 지역

Azure 공용 지역이나 Azure Government 지역 또는 둘 다에 플랜을 게시할 수 있습니다. [Azure Government](../azure-government/documentation-government-manage-marketplace-partners.md)에 게시하기 전에 특정 엔드포인트가 다를 수 있으므로 환경에서 플랜을 테스트하고 유효성을 검사합니다. 플랜을 설정하고 테스트하려면 [Microsoft Azure Government 평가판](https://azure.microsoft.com/global-infrastructure/government/request/)에서 평가판 계정을 요청합니다.

게시자는 규정 준수 제어, 보안 조치 및 모범 사례를 담당합니다. Azure Government는 물리적으로 격리되어 미국에만 있는 데이터 센터와 네트워크를 사용합니다.

상업용 마켓플레이스에서 지원되는 국가 및 지역 목록은 [지리적 가용성 및 통화 지원](marketplace-geo-availability-currencies.md)을 참조하세요.

Azure Government 서비스는 특정 정부 규정 및 요구 사항이 적용되는 데이터를 처리합니다. 예를 들어 FedRAMP, NIST 800.171(DIB), ITAR, IRS 1075, DoD L4 및 CJIS가 있습니다. 관련 프로그램에 대한 인증을 알리기 위해 해당 프로그램을 설명하는 최대 100개의 링크를 제공할 수 있습니다. 이 링크는 프로그램의 목록에 직접 연결되는 링크이거나 사용자 고유의 웹 사이트에 있는 목록의 규정 준수에 관한 설명에 연결되는 링크일 수 있습니다. 이 링크는 Azure Government 고객에게만 표시됩니다.

## <a name="choose-who-can-see-your-plan"></a>플랜을 볼 수 있는 사용자 선택

제한 없음(공개) 또는 선택한 특정 대상 그룹(프라이빗)에만 표시되도록 각 플랜을 구성할 수 있습니다. 최대 100개의 플랜을 만들 수 있으며 여기서 최대 45개의 플랜을 프라이빗으로 지정할 수 있습니다. 특정 고객에게 다양한 가격 책정 옵션이나 기술 구성을 제공하기 위해 프라이빗 플랜을 만들 수 있습니다.

사용자가 할당한 각 구독 ID에 대한 설명을 포함하는 옵션과 함께 Azure 구독 ID를 사용하여 프라이빗 플랜에 대한 액세스 권한을 부여합니다. 최대 10개의 구독 ID를 수동으로 추가하거나 또는 .CSV 파일을 사용하여 최대 1만 개의 구독 ID를 추가할 수 있습니다. Azure 구독 ID는 GUID로 표시되며 문자는 소문자여야 합니다.

CSP(클라우드 솔루션 공급자 프로그램)의 재판매인을 통해 설정된 Azure 구독은 프라이빗 플랜을 지원하지 않습니다. 자세한 내용은 [Microsoft 상업용 마켓플레이스의 프라이빗 제품](private-offers.md)을 참조하세요.

> [!NOTE]
> 프라이빗 플랜을 게시하는 경우 나중에 표시 유형을 퍼블릭으로 변경할 수 있습니다. 그러나 퍼블릭 플랜을 게시한 후에는 표시 유형을 프라이빗으로 변경할 수 없습니다.

## <a name="define-pricing"></a>가격 책정 정의

각 플랜에 대한 월별 가격을 제공해야 합니다. 이 가격은 이 솔루션을 통해 배포된 리소스에 의해 발생하는 Azure 인프라 또는 종량제 소프트웨어 비용에 추가됩니다.

또한 월별 가격 외에 [요금 청구](marketplace-metering-service-apis.md)를 사용하여 비표준 단위의 사용량에 해당하는 가격을 설정할 수 있습니다. 월별 가격을 0으로 설정하고 원하는 경우 요금 청구를 사용하여 전용 요금을 청구할 수 있습니다.

가격은 USD(미국 달러)로 설정되며 저장 시 현재 환율을 사용하여 선택한 모든 시장의 현지 통화로 변환됩니다. 그러나 각 시장에 대한 고객 가격을 설정하도록 선택할 수 있습니다.

## <a name="just-in-time-jit-access"></a>JIT(Just In Time) 액세스

JIT 액세스를 사용하면 문제 해결 또는 유지 관리를 위해 관리형 애플리케이션의 리소스에 대한 높은 액세스 권한을 요청할 수 있습니다. 항상 리소스에 대한 읽기 전용 액세스 권한을 가지지만 특정 기간 동안 더 큰 액세스 권한을 가질 수 있습니다. 자세한 내용은 [Azure Managed Applications의 JIT(Just-In-Time) 액세스 사용 및 요청](../azure-resource-manager/managed-applications/request-just-in-time-access.md)을 참조하세요.

> [!NOTE]
> 이 기능을 지원하려면 `createUiDefinition.json` 파일을 업데이트해야 합니다.

## <a name="deployment-mode"></a>배포 모드

**전체** 또는 **증분** 배포 모드를 사용하도록 관리되는 응용 프로그램 플랜을 구성할 수 있습니다. 완료 모드에서 고객이 응용 프로그램을 다시 배포하면 리소스가 [mainTemplate.json](../azure-resource-manager/managed-applications/publish-service-catalog-app.md?tabs=azure-powershell#create-the-arm-template)에 정의되지 않았을 경우 관리되는 리소스 그룹의 리소스가 제거됩니다. 증분 모드에서 애플리케이션을 다시 배포하면 기존 리소스가 변경되지 않습니다. 자세한 내용은 [Azure Resource Manager 배포 모드](../azure-resource-manager/templates/deployment-modes.md)를 참조하세요.

## <a name="notification-endpoint-url"></a>알림 엔드포인트 URL

선택 사항으로, 이 플랜의 관리되는 응용 프로그램 인스턴스에서 모든 CRUD 작업에 관한 알림을 수신하려면 HTTPS 웹후크 엔드포인트를 지정할 수 있습니다.

## <a name="customize-allowed-customer-actions-optional"></a>허용되는 고객 작업 사용자 지정(선택 사항)

기본적으로 사용할 수 있는 `*/read` 작업 외에 관리되는 리소스에서 고객이 할 수 있는 작업을 지정하려면 이 옵션을 선택합니다.

이 옵션을 선택하는 경우 제어 작업이나 허용된 데이터 작업, 또는 둘 다 제공해야 합니다. 자세한 내용은 [Azure 리소스에 대한 거부 할당 이해](../role-based-access-control/deny-assignments.md)를 참조하세요. 사용 가능한 작업에 대해서는 [Azure Resource Manager 리소스 공급자 작업](../role-based-access-control/resource-provider-operations.md)을 참조하세요. 예를 들어 소비자가 가상 머신을 다시 시작하도록 허용하려면 허용되는 작업에 `Microsoft.Compute/virtualMachines/restart/action`을 추가합니다.

## <a name="choose-who-can-manage-the-application"></a>응용 프로그램을 관리할 수 있는 사용자 선택

선택한 각 클라우드 _퍼블릭 Azure_ 과 _Azure Government Cloud_ 에서 관리되는 응용 프로그램을 관리할 수 있는 사람을 지정해야 합니다. 다음과 같은 정보를 수집합니다.

- **Azure Active Directory 테넌트 ID** – 권한을 부여하려는 사용자, 그룹 또는 응용 프로그램의 ID가 포함된 Azure AD 테넌트 ID입니다(디렉터리 ID라고도 함). Azure Portal의 [Azure Active Directory 속성](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties)에서 Azure AD 테넌트 ID를 찾을 수 있습니다.
- **권한 부여** – 관리되는 리소스 그룹에 권한을 부여하려는 사용자, 그룹 또는 응용 프로그램의 Azure Active Directory 개체 ID를 추가합니다. 사용자를 해당 보안 주체 ID로 식별하며, 이 ID는 [Azure Portal의 Azure Active Directory 사용자 블레이드](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers)에서 찾을 수 있습니다.

각 보안 주체의 ID에 대해 Azure AD 기본 제공 역할(소유자 또는 참가자) 중 하나에 연결합니다. 선택하는 역할은 보안 주체가 고객 구독의 리소스에 대해 가질 수 있는 권한을 설명합니다. 자세한 정보는 [Azure 기본 제공 역할](../role-based-access-control/built-in-roles.md)을 참조하세요. RBAC(역할 기반 액세스 제어)에 관한 자세한 내용은 [Azure Portal에서 RBAC 시작](../role-based-access-control/overview.md)을 참조하세요.

> [!NOTE]
> Azure 지역마다 100개까지 권한 부여를 추가할 수 있지만 일반적으로 Active Directory 사용자 그룹을 만들고 “보안 주체 ID”에 해당 ID를 지정하는 것이 더 쉽습니다. 이렇게 하면 플랜이 배포된 후 관리 그룹에 더 많은 사용자를 추가하고 더 많은 권한 부여만을 추가하기 위해 플랜을 업데이트할 필요성을 줄일 수 있습니다.

## <a name="policy-settings"></a>정책 설정

배포된 솔루션의 규정 준수 요구 사항을 지정하려면 [Azure Policy](../governance/policy/index.yml)를 관리되는 응용 프로그램에 적용합니다. 매개 변수 값의 형식 및 정책 정의는 [Azure Policy 샘플](../governance/policy/samples/index.md)을 참조하세요.

최대 5개의 정책, 그리고 각 정책 유형에 인스턴스 하나만 구성할 수 있습니다. 일부 정책 유형에는 추가 매개 변수가 필요합니다.

| 정책 유형 | 정책 매개 변수 필요 |
| ------------ | ------------- |
| Azure SQL Database 암호화 | 예 |
| Azure SQL Server 감사 설정 | 예 |
| Azure Data Lake Store 암호화 | 예 |
| 진단 설정 감사 | 예 |
| 감사 리소스 위치 준수 | 예 |
|||

추가하는 각 정책 유형에 대해 Standard 또는 Free 정책 SKU를 연결해야 합니다. 표준 SKU는 감사 정책에 필요합니다. 정책 이름은 50자로 제한됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure 애플리케이션 제품 만들기](azure-app-offer-setup.md)
