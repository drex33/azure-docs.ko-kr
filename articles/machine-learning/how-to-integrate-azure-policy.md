---
title: Azure Machine Learning 감사 및 관리
titleSuffix: Azure Machine Learning
description: Azure Policy를 사용하여 Azure Machine Learning에 대한 기본 제공 정책을 사용하여 작업 영역이 요구 사항을 준수하는지 확인하는 방법을 알아봅니다.
author: aashishb
ms.author: aashishb
ms.date: 05/10/2021
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.openlocfilehash: e26fbf9168d0c94666969721af70dde30f3e7ef9
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129058864"
---
# <a name="audit-and-manage-azure-machine-learning"></a>Azure Machine Learning 감사 및 관리

팀이 Azure Machine Learning에 대해 공동으로 작업 하는 경우 구성 및 리소스 구성에 대 한 다양 한 요구 사항을 직면 하 게 될 수 있습니다. 기계 학습 팀은 공동 작업을 위해 작업 영역을 구성 하는 방법에 대 한 유연성을 찾거나 계산 클러스터를 사용 사례의 요구 사항으로 크기를 조정할 수 있습니다. 이러한 시나리오에서는 응용 프로그램 팀에서 자체 인프라를 관리할 수 있는 경우 생산성이 극대화 될 수 있습니다.

플랫폼 관리자는 정책을 사용 하 여 팀이 자신의 리소스를 관리할 guardrails의 레이아웃을 지정할 수 있습니다. [Azure Policy](../governance/policy/index.yml) 는 리소스 상태를 감사 하 고 제어 하는 데 도움이 됩니다. 이 문서에서는 Azure Machine Learning에 대 한 사용 가능한 감사 제어 및 거 버 넌 스 관행에 대해 알아봅니다.

## <a name="policies-for-azure-machine-learning"></a>Azure Machine Learning에 대 한 정책

[Azure Policy](../governance/policy/index.yml) 는 Azure 리소스가 정책을 준수하는지 확인할 수 있도록 하는 거버넌스 도구입니다.

Azure Machine Learning은 Azure Machine Learning과 관련된 일반적인 시나리오에 사용할 수 있는 정책 집합을 제공합니다. 이러한 정책 정의를 기존 구독에 할당하거나 고유한 사용자 지정 정의를 만드는 기준으로 사용할 수 있습니다.

아래 표에는 Azure Machine Learning을 사용 하 여 할당할 수 있는 정책 선택이 포함 되어 있습니다. Azure Machine Learning에 대한 기본 제공 정책의 전체 목록은 [Azure Machine Learning에 대한 기본 제공 정책](../governance/policy/samples/built-in-policies.md#machine-learning)을 참조하세요.

| 정책 | 설명 |
| ----- | ----- |
| **고객 관리형 키** | 작업 영역에서 고객이 관리하는 키를 사용해야 하는지 여부를 감사하거나 적용합니다. |
| **프라이빗 링크** | 작업 영역에서 프라이빗 엔드포인트를 사용하여 가상 네트워크와 통신하는지 여부를 감사하거나 적용합니다. |
| **프라이빗 엔드포인트** | 프라이빗 엔드포인트를 만들어야 하는 Azure Virtual Network 서브넷을 구성합니다. |
| **프라이빗 DNS 영역** | 프라이빗 링크에 사용할 프라이빗 DNS 영역을 구성합니다. |
| **사용자 할당 관리 ID** | 작업 영역에서 사용자 할당 관리 ID를 사용하는지 여부를 감사하거나 적용합니다. |
| **로컬 인증 사용 안 함** | Azure Machine Learning 컴퓨팅 리소스에 로컬 인증 방법을 사용하지 않도록 설정해야 하는지 여부를 감사하거나 적용합니다. |
| **로컬 인증 수정/사용 안 함** | 로컬 인증 방법을 사용하지 않도록 컴퓨팅 리소스를 구성합니다. |

구독 또는 리소스 그룹 수준 등의 여러 범위에서 정책을 설정할 수 있습니다. 자세한 내용은 [Azure 정책 설명서](../governance/policy/overview.md)를 참조하세요.

## <a name="assigning-built-in-policies"></a>기본 제공 정책 할당

Azure Machine Learning과 관련된 기본 제공 정책 정의를 보려면 다음 단계를 사용합니다.

1. [Azure Portal](https://portal.azure.com)에서 __Azure Policy__ 로 이동합니다.
1. __정의__ 를 선택합니다.
1. __형식__ 에서 _기본 제공_ 을 선택하고 __범주__ 에 대해 __Machine Learning__ 를 선택합니다.

여기에서 정책 정의를 선택하여 볼 수 있습니다. 정의를 보는 동안 __할당__ 링크를 사용하여 특정 범위에 정책을 할당하고 정책에 대한 매개 변수를 구성할 수 있습니다. 자세한 내용은 [정책 할당-포털](../governance/policy/assign-policy-portal.md)을 참조하세요.

[Azure PowerShell](../governance/policy/assign-policy-powershell.md), [Azure CLI](../governance/policy/assign-policy-azurecli.md) 및 [템플릿](../governance/policy/assign-policy-template.md)을 사용하여 정책을 할당할 수도 있습니다.

## <a name="conditional-access-policies"></a>조건부 액세스 정책

Azure Machine Learning 작업 영역에 액세스할 수 있는 사용자를 제어하려면 Azure Active Directory [조건부 액세스](../active-directory/conditional-access/overview.md)를 사용합니다.

## <a name="enable-self-service-using-landing-zones"></a>랜딩 영역을 사용 하 여 셀프 서비스 사용

랜딩 영역은 규모, 거 버 넌 스, 보안 및 생산성을 위해 Azure 환경을 설정 하는 아키텍처 패턴입니다. 데이터 방문 영역은 응용 프로그램 팀에서 데이터 및 분석 워크 로드를 호스트 하는 데 사용 하는 관리자로 구성 환경입니다.

방문 영역의 목적은 팀이 Azure 환경에서 시작 될 때 모든 인프라 구성 작업이 완료 되도록 하는 것입니다. 예를 들어 보안 제어는 조직의 표준을 준수 하 고 네트워크 연결이 설정 된 상태에서 설정 됩니다.

랜딩 영역 패턴을 사용 하 여 기계 학습 팀을 사용 하도록 설정 하 여 셀프 서비스에서 자체 리소스를 배포 및 관리할 수 있습니다. Azure policy를 사용 하 여 관리자는 규정 준수를 위해 Azure 리소스를 감사 하 고 관리할 수 있으며, 작업 영역이 요구 사항에 부합 하는지 확인할 수 있습니다. 

Azure Machine Learning는 [클라우드 채택 프레임 워크 데이터 관리 및 분석 시나리오](/azure/cloud-adoption-framework/scenarios/data-management/)에서 [데이터 방문 영역과](https://github.com/Azure/data-landing-zone) 통합 됩니다. 이 참조 구현은 machine learning 작업을 마이그레이션할 최적화 된 환경을 제공 하며 미리 구성 된 Azure Machine Learning에 대 한 정책을 포함 합니다.

## <a name="configure-built-in-policies"></a>기본 제공 정책 구성

### <a name="workspace-encryption-with-customer-managed-key"></a>고객 관리 키로 작업 영역 암호화

작업 영역을 고객이 관리하는 키로 암호화할지 아니면 Microsoft에서 관리되는 키를 사용하여 메트릭과 메타데이터를 암호화해야 하는지 여부를 제어합니다. 고객 관리 키를 사용하는 방법에 대한 자세한 내용은 데이터 암호화 문서의 [Azure Cosmos DB](concept-data-encryption.md#azure-cosmos-db) 섹션을 참조하세요.

이 정책을 구성하려면 효과 매개 변수를 __audit__ 또는 __deny__ 로 설정합니다. __audit__ 으로 설정하면 고객이 관리하는 키를 사용하지 않고 작업 영역을 만들 수 있으며 활동 로그에 경고 이벤트가 생성됩니다.

정책이 __deny__ 로 설정된 경우 고객이 관리하는 키를 지정하지 않으면 작업 영역을 만들 수 없습니다. 고객 관리 키를 사용하지 않고 작업 영역을 만들려 하면 `Resource 'clustername' was disallowed by policy`와 유사한 오류가 발생하고 활동 로그에 오류가 생성됩니다. 이 오류의 일부로 정책 식별자도 반환됩니다.

### <a name="workspace-should-use-private-link"></a>작업 영역은 프라이빗 링크를 사용해야 함

작업 영역에서 Azure Private Link를 사용하여 Azure Virtual Network와 통신해야 하는지 여부를 제어합니다. 프라이빗 링크를 사용하는 방법에 대한 자세한 내용은 [작업 영역에 대한 프라이빗 링크 구성](how-to-configure-private-link.md)을 참조하세요.

이 정책을 구성하려면 효과 매개 변수를 __audit__ 또는 __deny__ 로 설정합니다. __audit__ 으로 설정하면 프라이빗 링크를 사용하지 않고 작업 영역을 만들 수 있으며 활동 로그에 경고 이벤트가 생성됩니다.

정책이 __deny__ 로 설정된 경우 프라이빗 링크를 사용하지 않으면 작업 영역을 만들 수 없습니다. 프라이빗 링크 없이 작업 영역을 만들려 하면 오류가 발생합니다. 이 오류는 활동 로그에도 로그됩니다. 정책 식별자는 이 오류의 일부로 반환됩니다.

### <a name="workspace-should-use-private-endpoint"></a>작업 영역에서 프라이빗 엔드포인트 사용

Azure Virtual Network의 지정된 서브넷 내에 프라이빗 엔드포인트를 만들도록 작업 영역을 구성합니다.

이 정책을 구성하려면 effect 매개 변수를 __Deployifnotexists__ 로 설정합니다. __privateEndpointSubnetID__ 를 서브넷의 Azure Resource Manager ID로 설정합니다.

### <a name="workspace-should-use-private-dns-zones"></a>작업 영역은 프라이빗 DNS 영역을 사용해야 함

프라이빗 DNS 영역을 사용하여 프라이빗 엔드포인트에 대한 기본 DNS 확인을 재정의하는 작업 영역을 구성합니다.

이 정책을 구성하려면 effect 매개 변수를 __Deployifnotexists__ 로 설정합니다. __privateDnsZoneId__ 를 사용할 프라이빗 DNS 영역의 Azure Resource Manager ID로 설정합니다. 

### <a name="workspace-should-use-user-assigned-managed-identity"></a>작업 영역은 사용자가 할당한 관리 ID를 사용해야 함

시스템 할당 관리 ID(기본값) 또는 사용자 할당 관리 ID를 사용하여 작업 영역을 만들지 여부를 제어합니다. 작업 영역에 대한 관리 ID는 Azure Storage, Azure Container Registry, Azure Key Vault 및 Azure Application Insights와 같은 관련 리소스에 액세스하는 데 사용됩니다. 자세한 내용은 [Azure Machine Learning에서 관리상 ID 사용](how-to-use-managed-identities.md)을 참조하세요.

이 정책을 구성하려면 effect 매개 변수를 __audit__, __deny__ 또는 __disabled__ 로 설정합니다. __audit__ 으로 설정된 경우 사용자 할당 관리 ID를 지정하지 않고 작업 영역을 만들 수 있습니다. 시스템 할당 ID가 사용되고 활동 로그에 경고 이벤트가 생성됩니다.

정책이 __deny__ 로 설정되어 있으면 생성 프로세스 중 사용자에게 할당된 ID를 제공하지 않는 한 작업 영역을 만들 수 없습니다. 사용자 할당 ID를 제공하지 않고 작업 영역을 만들려 하면 오류가 발생합니다. 이 오류는 활동 로그에도 로그됩니다. 정책 식별자는 이 오류의 일부로 반환됩니다.

### <a name="disable-local-authentication"></a>로컬 인증 사용 안 함

Azure Machine Learning 컴퓨팅 클러스터 또는 인스턴스에서 로컬 인증(SSH)을 사용하지 않도록 설정할지 여부를 제어합니다.

이 정책을 구성하려면 effect 매개 변수를 __audit__, __deny__ 또는 __disabled__ 로 설정합니다. __감사__ 로 설정하면 SSH를 사용하도록 설정한 컴퓨팅을 만들 수 있으며 활동 로그에 경고 이벤트가 생성됩니다.

정책이 __거부__ 로 설정된 경우 SSH를 사용하지 않는 한 컴퓨팅을 만들 수 없습니다. SSH를 사용하도록 설정한 컴퓨팅을 만들려고 시도하면 오류가 발생합니다. 이 오류는 활동 로그에도 로그됩니다. 정책 식별자는 이 오류의 일부로 반환됩니다.

### <a name="modifydisable-local-authentication"></a>로컬 인증 수정/사용 안 함

Azure Machine Learning 컴퓨팅 클러스터 또는 인스턴스 만들기 요청을 수정하여 로컬 인증(SSH)을 사용하지 않도록 설정합니다.

이 정책을 구성하려면 effect 매개 변수를 __Modify__ 또는 __Disabled__ 로 설정합니다. __수정__ 을 설정한 경우 정책이 적용되는 범위 내에서 컴퓨팅 클러스터 또는 인스턴스를 만들면 자동으로 로컬 인증을 사용하지 않습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Policy 설명서](../governance/policy/overview.md)
* [Azure Machine Learning에 대한 기본 제공 정책](policy-reference.md)
* [Azure Security Center로 보안 정책 작업](../security-center/tutorial-security-policy.md)
* [데이터 관리 및 분석에 대한 클라우드 채택 프레임워크 시나리오에서는](/azure/cloud-adoption-framework/scenarios/data-management/) 클라우드에서 데이터 및 분석 워크로드를 실행할 때 고려해야 할 사항을 간략하게 설명합니다.
* [클라우드 채택 프레임워크 데이터 랜딩 존은](https://github.com/Azure/data-landing-zone) Azure에서 데이터 및 분석 워크로드를 관리하기 위한 참조 구현을 제공합니다.
* [정책을 사용하여 Azure 프라이빗 DNS 영역 과 Azure Private Link 통합하여](/azure/cloud-adoption-framework/ready/azure-best-practices/private-link-and-dns-integration-at-scale)작업 영역 및 종속 리소스에 대한 프라이빗 링크 구성을 관리하는 방법을 알아봅니다.
