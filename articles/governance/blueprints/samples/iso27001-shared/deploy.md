---
title: ISO 27001 Shared Services 청사진 샘플 배포
description: 청사진 아티팩트 매개 변수 세부 정보를 포함하여 ISO 27001 Shared Services 청사진 샘플에 대한 단계를 배포합니다.
ms.date: 09/08/2021
ms.topic: sample
ms.openlocfilehash: 1eb1e8fef7a83ee3cfdb81514b9002281a3f6fb6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128674968"
---
# <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>ISO 27001 Shared Services 청사진 샘플 배포

Azure Blueprints ISO 27001 Shared Services 청사진 샘플을 배포하려면 다음 단계를 수행해야 합니다.

> [!div class="checklist"]
> - 샘플에서 새 청사진 만들기
> - 샘플 사본을 **게시됨** 으로 표시
> - 기존 구독에 청사진 사본 할당

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free)을 만듭니다.

## <a name="create-blueprint-from-sample"></a>샘플에서 청사진 만들기

먼저 샘플을 스타터로 사용하여 환경 내에 새 청사진을 만들어 청사진 샘플을 구현합니다.

1. 왼쪽 창에서 **모든 서비스** 를 선택합니다. **청사진** 을 검색하고 선택합니다.

1. **시작** 페이지 왼쪽에서 _청사진 만들기_ 아래의 **만들기** 단추를 선택합니다.

1. **ISO 27001: Shared Services** 청사진 샘플을 _다른 샘플_ 아래에서 찾은 다음, **이 샘플 사용** 을 선택합니다.

1. 청사진 샘플의 _기본 사항_ 을 입력합니다.

   - **청사진 이름**: ISO 27001 Shared Services 청사진 샘플 사본의 이름을 지정합니다.
   - **정의 위치**: 줄임표를 사용하고 샘플 사본을 저장할 관리 그룹을 선택합니다.

1. 페이지 위쪽의 _아티팩트_ 탭 또는 페이지 아래쪽의 **다음: 아티팩트** 를 선택합니다.

1. 청사진 샘플을 구성하는 아티팩트 목록을 검토합니다. 아티팩트 대부분에 매개 변수는 나중에 정의할 것입니다. 청사진 샘플 검토가 끝나면 **초안 저장** 을 선택합니다.

## <a name="publish-the-sample-copy"></a>샘플 사본 게시

이제 작업 환경에 청사진 샘플 사본이 만들어졌습니다. 이 청사진 정의는 **초안** 모드로 만들어졌으며 먼저 **게시** 해야만 할당하고 배포할 수 있습니다. 청사진 샘플의 사본을 환경 및 필요에 맞게 사용자 지정할 수 있지만, 해당 수정으로 ISO 27001 표준에서 벗어날 수 있습니다.

1. 왼쪽 창에서 **모든 서비스** 를 선택합니다. **청사진** 을 검색하고 선택합니다.

1. 왼쪽 페이지에서 **청사진 정의** 를 선택합니다. 필터를 사용하여 청사진 샘플의 사본을 찾은 다음, 선택합니다.

1. 페이지 맨 위에서 **청사진 게시** 를 선택합니다. 오른쪽의 새 페이지에서 청사진 샘플 사본의 **버전** 을 지정합니다. 이 속성은 나중에 수정할 경우에 유용합니다. "ISO 27001 청사진 샘플에서 게시된 첫 번째 버전"과 같은 **변경 메모** 를 제공합니다. 그런 후 페이지 아래쪽의 **게시** 를 선택합니다.

## <a name="assign-the-sample-copy"></a>샘플 사본 할당

청사진 샘플 사본이 **게시** 되면 저장된 관리 그룹 내의 구독에 할당할 수 있습니다. 이 단계에서는 청사진 샘플 사본의 배포를 고유하게 만들기 위해 매개 변수가 제공됩니다.

1. 왼쪽 창에서 **모든 서비스** 를 선택합니다. **청사진** 을 검색하고 선택합니다.

1. 왼쪽 페이지에서 **청사진 정의** 를 선택합니다. 필터를 사용하여 청사진 샘플의 사본을 찾은 다음, 선택합니다.

1. 청사진 정의 페이지 위쪽에서 **청사진 할당** 을 선택합니다.

1. 청사진 할당의 매개 변수 값을 다음과 같이 지정합니다.

   - 기본 사항

     - **구독**: 청사진 샘플 사본을 저장한 관리 그룹에 있는 하나 이상의 구독을 선택합니다. 둘 이상의 구독을 선택하면 입력한 매개 변수를 사용하여 각 구독의 할당이 생성됩니다.
     - **할당 이름**: 청사진의 이름에 따라 이 이름이 미리 채워집니다.
       필요에 따라 변경하거나 그대로 둡니다.
     - **위치**: 관리 ID를 만들 지역을 선택합니다. Azure Blueprints는 이 관리 ID를 사용하여 할당된 청사진의 모든 아티팩트를 배포합니다. 자세히 알아보려면 [Azure 리소스의 관리 ID](../../../../active-directory/managed-identities-azure-resources/overview.md)를 참조하세요.
     - **청사진 정의 버전**: 청사진 샘플 사본의 **게시됨** 버전을 선택합니다.

   - 할당 잠금

     환경에 맞는 청사진 잠금 설정을 선택합니다. 자세한 내용은 [청사진 리소스 잠금](../../concepts/resource-locking.md)을 참조하세요.

   - 관리 ID

     기본값 _시스템이 할당한_ 관리 ID 옵션을 유지합니다.

   - 청사진 매개 변수

     이 섹션에 정의된 매개 변수는 일관성을 제공하기 위해 청사진 정의의 아티팩트 대부분에서 사용됩니다.

     - **조직 이름**: 조직에 대한 짧은 이름을 입력합니다. 이 속성은 주로 리소스 이름을 지정하기 위해 사용됩니다.
     - **Shared Services 서브넷 주소 접두사**: 배포된 리소스를 함께 네트워킹하기 위해 CIDR 표기법으로 값을 제공합니다.
     - **Shared Services 위치**: 아티팩트를 배포하는 위치를 결정합니다. 일부 서비스는 모든 위치에서 사용할 수 없습니다. 이러한 서비스를 배포하는 아티팩트는 해당 아티팩트를 배포하는 위치에 대한 매개 변수 옵션을 제공합니다.
     - **허용되는 위치(Azure Policy: ISO 27001의 청사진 이니셔티브)** : 리소스 그룹 및 리소스에 허용되는 위치를 나타내는 값입니다.
     - **VM 에이전트에 대한 Log Analytics 작업 영역(Azure Policy: ISO 27001의 청사진 이니셔티브)** : 작업 영역의 리소스 ID를 지정합니다. 이 매개 변수는 `concat` 함수를 사용하여 리소스 ID를 생성합니다.

   - 아티팩트 매개 변수

     이 섹션에 정의된 매개 변수는 정의된 아티팩트에 적용됩니다. 이러한 매개 변수는 청사진 할당 중에 정의되므로 [동적 매개 변수](../../concepts/parameters.md#dynamic-parameters)입니다. 전체 목록 또는 아티팩트 매개 변수 및 해당 설명은 [아티팩트 매개 변수 테이블](#artifact-parameters-table)을 참조하세요.

1. 모든 매개 변수를 입력한 후에는 페이지 아래쪽에서 **할당** 을 선택합니다. 청사진 할당이 생성되고 아티팩트 배포가 시작됩니다. 배포에는 약 1 시간이 걸립니다. 배포의 상태를 확인하려면 청사진 할당을 엽니다.

> [!WARNING]
> Azure Blueprints 서비스 및 기본 제공 청사진 샘플은 **무료** 입니다. Azure 리소스는 [제품별로 가격이 책정](https://azure.microsoft.com/pricing/)됩니다. 이 청사진 샘플에서 배포되는 리소스를 실행하는 비용을 추정하려면 [가격 계산기](https://azure.microsoft.com/pricing/calculator/)를 사용합니다.

## <a name="artifact-parameters-table"></a>아티팩트 매개 변수 테이블

다음 테이블은 청사진 아티팩트 매개 변수의 목록을 제공합니다.

|아티팩트 이름|아티팩트 형식|매개 변수 이름|Description|
|-|-|-|-|
|\[미리 보기\]: Linux VMSS(VM Scale Sets)용 Log Analytics 에이전트 배포|정책 할당|선택 사항: 범위에 추가할 Linux OS를 지원하는 VM 이미지 목록|(선택 사항) 기본값은 _["없음"]_ 입니다.|
|\[미리 보기\]: Linux VM용 Log Analytics 에이전트 배포|정책 할당|선택 사항: 범위에 추가할 Linux OS를 지원하는 VM 이미지 목록|(선택 사항) 기본값은 _["없음"]_ 입니다.|
|\[미리 보기\]: Windows VMSS(VM Scale Sets)용 Log Analytics 에이전트 배포|정책 할당|선택 사항: 범위에 추가할 Windows OS를 지원하는 VM 이미지 목록|(선택 사항) 기본값은 _["없음"]_ 입니다.|
|\[미리 보기\]: Windows VM용 Log Analytics 에이전트 배포|정책 할당|선택 사항: 범위에 추가할 Windows OS를 지원하는 VM 이미지 목록|(선택 사항) 기본값은 _["없음"]_ 입니다.|
|허용되는 리소스 유형|정책 할당|허용되는 리소스 유형|배포할 수 있는 리소스 종류 목록입니다. 이 목록은 Shared Services에 배포된 모든 리소스 종류로 구성 됩니다.|
|허용되는 스토리지 계정 SKU|정책 할당|허용되는 스토리지 SKU|허용되는 진단 로그 스토리지 계정 SKU의 목록입니다. 기본값은 _["Standard_LRS"]_ 입니다.|
|허용되는 가상 머신 SKU|정책 할당|배포할 수 있는 가상 머신 SKU의 목록입니다. 기본값은 _["Standard_DS1_v2", "Standard_DS2_v2"]_ 입니다.|
|ISO 27001의 청사진 이니셔티브|정책 할당|진단 로그를 감사하는 리소스 종류|진단 로그 설정을 사용하도록 설정하지 않은 경우 감사할 리소스 종류의 목록입니다. 허용되는 값은 [Azure Monitor 진단 로그 스키마](../../../../azure-monitor/essentials/resource-logs-schema.md#service-specific-schemas)에서 확인할 수 있습니다.|
|Log Analytics 리소스 그룹|Resource group|Name|**잠김** - **조직 이름** 을 `-sharedsvsc-log-rg`와 연결하여 리소스 그룹을 고유하게 만듭니다.|
|Log Analytics 리소스 그룹|Resource group|위치|**잠김** - 청사진 매개 변수를 사용합니다.|
|Log Analytics 템플릿|Resource Manager 템플릿|서비스 계층|Log Analytics 작업 영역의 계층을 설정합니다. 기본값은 _PerNode_ 입니다.|
|Log Analytics 템플릿|Resource Manager 템플릿|로그 보존(일)|데이터 보존 기간(일)입니다. 기본값은 _365_ 입니다.|
|Log Analytics 템플릿|Resource Manager 템플릿|위치|Log Analytics 작업 영역을 만드는 데 사용되는 지역입니다. 기본값은 _미국 서부 2_ 입니다.|
|네트워크 리소스 그룹|Resource group|Name|**잠김** - **조직 이름** 을 `-sharedsvcs-net-rg`와 연결하여 리소스 그룹을 고유하게 만듭니다.|
|네트워크 리소스 그룹|Resource group|위치|**잠김** - 청사진 매개 변수를 사용합니다.|
|Azure Firewall 템플릿|Resource Manager 템플릿|Azure Firewall 프라이빗 IP|[Azure Firewall](../../../../firewall/overview.md)의 프라이빗 IP를 구성합니다. 이 값은 Shared Services 서브넷에 대한 기본 경로 테이블로도 사용됩니다. **Azure Firewall 서브넷 주소 접두사** 에 정의된 CIDR 표기법의 일부여야 합니다. 기본값은 _10.0.4.4_ 입니다.|
|Azure Firewall 템플릿|Resource Manager 템플릿|로그 보존(일)|데이터 보존 기간(일)입니다. 기본값은 _365_ 입니다.|
|네트워크 보안 그룹 템플릿|Resource Manager 템플릿|로그 보존(일)|데이터 보존 기간(일)입니다. 기본값은 _365_ 입니다.|
|Virtual Network 및 경로 테이블 템플릿|Resource Manager 템플릿|Virtual Network 주소 접두사|가상 네트워크에 대한 CIDR 표기법입니다. 기본값은 _10.0.0.0/16_ 입니다.|
|Virtual Network 및 경로 테이블 템플릿|Resource Manager 템플릿|Virtual Network DDoS 보호 사용|가상 네트워크에 대한 DDoS 보호를 구성합니다. 기본값은 _True_ 입니다.|
|Virtual Network 및 경로 테이블 템플릿|Resource Manager 템플릿|Shared Services 서브넷 주소 접두사|Shared Services 서브넷에 대한 CIDR 표기법입니다. 기본값은 _10.0.0.0/24_ 입니다.|
|Virtual Network 및 경로 테이블 템플릿|Resource Manager 템플릿|DMZ 서브넷 주소 접두사|DMZ 서브넷에 대한 CIDR 표기법입니다. 기본값은 _10.0.1.0/24_ 입니다.|
|Virtual Network 및 경로 테이블 템플릿|Resource Manager 템플릿|Application Gateway 서브넷 주소 접두사|애플리케이션 게이트웨이 서브넷에 대한 CIDR 표기법입니다. 기본값은 _10.0.2.0/24_ 입니다.|
|Virtual Network 및 경로 테이블 템플릿|Resource Manager 템플릿|Virtual Network Gateway 서브넷 주소 접두사|가상 네트워크 게이트웨이 서브넷에 대한 CIDR 표기법입니다. 기본값은 _10.0.3.0/24_ 입니다.|
|Virtual Network 및 경로 테이블 템플릿|Resource Manager 템플릿|Azure Firewall 서브넷 주소 접두사|[Azure Firewall](../../../../firewall/overview.md) 서브넷에 대한 CIDR 표기법입니다. **Azure Firewall 프라이빗 IP** 매개 변수를 포함해야 합니다.|
|주요 자격 증명 모음 리소스 그룹|Resource group|Name|**잠김** - **조직 이름** 을 `-sharedsvcs-kv-rg`와 연결하여 리소스 그룹을 고유하게 만듭니다.|
|주요 자격 증명 모음 리소스 그룹|Resource group|위치|**잠김** - 청사진 매개 변수를 사용합니다.|
|Key Vault 템플릿|Resource Manager 템플릿|Jumpbox 관리 사용자 이름|Jumpbox의 사용자 이름입니다. **Jumpbox 템플릿** 의 동일한 속성 값과 일치해야 합니다. 기본값은 _jb-admin-user_ 입니다.|
|Key Vault 템플릿|Resource Manager 템플릿|Jumpbox 관리자 SSH 키 또는 암호|Jumpbox의 계정에 대한 키 또는 암호입니다. **Jumpbox 템플릿** 의 동일한 속성 값과 일치해야 합니다. 기본값은 없으며 비워 둘 수 없습니다.|
|Key Vault 템플릿|Resource Manager 템플릿|도메인 관리 사용자 이름|Active Directory VM에 액세스하고 다른 VM을 도메인에 조인하는 데 사용되는 사용자 이름입니다. **Active Directory Domain Services 템플릿** 의 **도메인 관리 사용자** 속성 값과 일치해야 합니다. 기본값은 _domain-admin-user_ 입니다.|
|Key Vault 템플릿|Resource Manager 템플릿|도메인 관리자 암호|도메인 관리 사용자의 암호입니다. 기본값은 없으며 비워 둘 수 없습니다.|
|Key Vault 템플릿|Resource Manager 템플릿|AAD 개체 ID|Key Vault 인스턴스에 액세스해야 하는 계정의 AAD 개체 ID입니다. 기본값은 없으며 비워 둘 수 없습니다. Azure Portal에서 이 값을 찾으려면 _서비스_ 아래에서 "사용자"를 검색하여 선택합니다. _이름_ 상자를 사용하여 계정 이름을 필터링하고 해당 계정을 선택합니다. _사용자 프로필_ 페이지에서 _개체 ID_ 옆의 “복사하려면 클릭” 아이콘을 선택합니다.  |
|Key Vault 템플릿|Resource Manager 템플릿|로그 보존(일)|데이터 보존 기간(일)입니다. 기본값은 _365_ 입니다.|
|Key Vault 템플릿|Resource Manager 템플릿|Key Vault SKU|생성된 Key Vault의 SKU를 지정합니다. 기본값은 _프리미엄_ 입니다.|
|Jumpbox 리소스 그룹|Resource group|Name|**잠김** - **조직 이름** 을 `-sharedsvcs-jb-rg`와 연결하여 리소스 그룹을 고유하게 만듭니다.|
|Jumpbox 리소스 그룹|Resource group|위치|**잠김** - 청사진 매개 변수를 사용합니다.|
|Jumpbox 템플릿|Resource Manager 템플릿|Jumpbox 관리 사용자 이름|Jumpbox VM에 액세스하는 데 사용되는 사용자 이름입니다. **Key Vault 템플릿** 의 동일한 속성 값과 일치해야 합니다. 기본값은 _jb-admin-user_ 입니다.|
|Jumpbox 템플릿|Resource Manager 템플릿|Jumpbox 관리자 암호(Key Vault 리소스 ID)|Key Vault의 리소스 ID입니다. "/subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv"를 사용하고 `{subscriptionId}`를 구독 ID로, `{orgName}`을 **조직 이름** 청사진 매개 변수로 바꿉니다.|
|Jumpbox 템플릿|Resource Manager 템플릿|Jumpbox 관리자 암호(Key Vault 비밀 이름)|Jumpbox 관리자의 사용자 이름입니다. **Key Vault 템플릿** 속성 **Jumpbox 관리 사용자 이름** 의 값과 일치해야 합니다.|
|Jumpbox 템플릿|Resource Manager 템플릿|Jumpbox 운영 체제|Jumpbox VM의 운영 체제를 결정합니다. 기본값은 _Windows_ 입니다.|
|Active Directory Domain Services 리소스 그룹|Resource group|Name|**잠김** - **조직 이름** 을 `-sharedsvcs-adds-rg`와 연결하여 리소스 그룹을 고유하게 만듭니다.|
|Active Directory Domain Services 리소스 그룹|Resource group|위치|**잠김** - 청사진 매개 변수를 사용합니다.|
|Active Directory Domain Services 템플릿|Resource Manager 템플릿|도메인 관리 사용자 이름|ADDS Jumpbox의 사용자 이름입니다. **Key Vault 템플릿** 의 동일한 속성 값과 일치해야 합니다. 기본값은 _adds-admin-user_ 입니다.|
|Active Directory Domain Services 템플릿|Resource Manager 템플릿|도메인 관리자 암호(Key Vault 리소스 ID)|Key Vault의 리소스 ID입니다. "/subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv"를 사용하고 `{subscriptionId}`를 구독 ID로, `{orgName}`을 **조직 이름** 청사진 매개 변수로 바꿉니다.|
|Active Directory Domain Services 템플릿|Resource Manager 템플릿|도메인 관리자 암호(Key Vault 비밀 이름)|도메인 관리자의 사용자 이름입니다. **Key Vault 템플릿** 속성 **도메인 관리 사용자 이름** 의 값과 일치해야 합니다.|
|Active Directory Domain Services 템플릿|Resource Manager 템플릿|도메인 이름|샘플에서 만든 Active Directory의 이름입니다. 기본값은 _contoso.com_ 입니다.|
|Active Directory Domain Services 템플릿|Resource Manager 템플릿|도메인 관리 사용자|관리자 AD 계정 및 AD 도메인에 조인하는 디바이스의 사용자 이름입니다. **Key Vault 템플릿** 의 **AD 관리 사용자 이름** 과 일치해야 합니다. 기본값은 _domain-admin-user_ 입니다.|
|Active Directory Domain Services 템플릿|Resource Manager 템플릿|도메인 관리자 암호|암호를 저장하기 위한 Key Vault 세부 정보를 설정합니다. 기본값은 없으며 비워 둘 수 없습니다.|

## <a name="next-steps"></a>다음 단계

지금까지 ISO 27001 Shared Services 청사진 샘플을 배포하는 단계를 살펴보았으므로 이제 다음 문서를 참조하여 아키텍처 및 컨트롤 매핑에 대해 알아보세요.

> [!div class="nextstepaction"]
> [ISO 27001 Shared Services 청사진 - 개요](./index.md)
> [ISO 27001 Shared Services 청사진 - 컨트롤 매핑](./control-mapping.md)

청사진 및 사용 방법에 대한 추가 문서:

- [청사진 수명 주기](../../concepts/lifecycle.md)에 대해 알아봅니다.
- [정적 및 동적 매개 변수](../../concepts/parameters.md) 사용 방법 이해
- [청사진 시퀀싱 순서](../../concepts/sequencing-order.md)를 사용자 지정하는 방법 알아보기
- [청사진 리소스 잠금](../../concepts/resource-locking.md)을 활용하는 방법 알아보기
- [기존 할당을 업데이트](../../how-to/update-existing-assignments.md)하는 방법 알아보기
