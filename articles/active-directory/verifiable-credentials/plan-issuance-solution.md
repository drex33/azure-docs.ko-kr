---
title: Azure Active Directory 확인 가능한 자격 증명 발급 솔루션 계획(미리 보기)
description: 엔드투엔드 발급 솔루션을 계획하는 방법을 알아봅니다.
documentationCenter: ''
author: barbaraselden
manager: martinco
ms.service: active-directory
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 07/20/2021
ms.author: baselden
ms.custom: references_regions
ms.openlocfilehash: d724c41fd16ea762fc96c36e67fe4a00a5b5e72d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566412"
---
# <a name="plan-your-azure-active-directory-verifiable-credentials-issuance-solution-preview"></a>Azure Active Directory 확인 가능한 자격 증명 발급 솔루션 계획(미리 보기)

 >[!IMPORTANT]
> Azure Active Directory 확인 가능한 자격 증명은 현재 공개 미리 보기로 제공됩니다. 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [**Microsoft Azure Preview에 대한 추가 사용 약관**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

자격 증명을 발급하는 것 외에 솔루션이 아키텍처 및 비즈니스에 미치는 영향을 전체적으로 파악할 수 있도록 발급 솔루션을 계획하는 것이 중요합니다. 아직 기본 정보를 확인하지 않았다면 [Azure Active Directory 확인 가능한 자격 증명 아키텍처 개요](introduction-to-verifiable-credentials-architecture.md)에서 확인하는 것이 좋습니다.

## <a name="scope-of-guidance"></a>지침의 범위

이 문서에서는 Microsoft 제품을 사용하여 ION(Identity Overlay Network)과 상호 운용하는 확인 가능한 자격 증명 발급 솔루션에 대한 계획의 기술적 측면을 다룹니다. 확인 가능한 자격 증명에 대한 Microsoft 솔루션은 W3C(World Wide Web 컨소시엄) [확인 가능한 자격 증명 데이터 모델 1.0](https://www.w3.org/TR/vc-data-model/) 및 [DID(탈중앙화 식별자) V1.0](https://www.w3.org/TR/did-core/) 표준을 따르므로 Microsoft가 아닌 타사 서비스와 상호 운용할 수 있습니다. 그러나 이 콘텐츠의 예제에는 확인 가능한 자격 증명에 대한 Microsoft 솔루션 스택이 반영됩니다. 

발급 솔루션과 관련이 없는 지원 기술을 다루는 항목은 이 콘텐츠에서 자세히 다루지 않습니다. 예를 들어 웹 사이트는 확인 가능한 자격 증명 발급 솔루션에서 사용되지만 웹 사이트 배포 계획은 자세히 다루지 않습니다.

## <a name="components-of-the-solution"></a>솔루션의 구성 요소

발급 솔루션에 대한 계획의 일부로 발급자, 사용자 및 검증 도구 간의 상호 작용을 가능하게 하는 솔루션을 디자인해야 합니다. 확인 가능한 자격 증명을 두 개 이상 발급할 수 있습니다. 다음 다이어그램에서는 발급 아키텍처의 구성 요소를 보여 줍니다.

### <a name="microsoft-vc-issuance-solution-architecture"></a>Microsoft VC 발급 솔루션 아키텍처

![발급 솔루션의 구성 요소](media/plan-issuance-solution/plan-issuance-solution-architecture.png)


### <a name="azure-active-directory-tenant"></a>Azure Active Directory 테넌트

Azure AD 확인 가능한 자격 증명 서비스를 실행하기 위한 필수 조건은 Azure AD(Azure Active Directory) 테넌트에서 호스트됩니다. Azure AD 테넌트는 솔루션의 일부인 Azure 리소스에 대한 IAM(ID 및 액세스 관리) 컨트롤 플레인을 제공합니다.

각 테넌트에는 Azure AD 확인 가능한 자격 증명 서비스의 단일 인스턴스와 단일 DID(탈중앙화 식별자)가 있습니다. DID는 발급자가 DID에 통합된 도메인을 소유한다는 증명을 제공합니다. DID는 주체와 검증 도구에서 발급자의 유효성을 검사하는 데 사용됩니다. 

### <a name="microsoft-azure-services"></a>Microsoft Azure 서비스

![Azure 서비스에 중점을 둔 발급 솔루션의 구성 요소](media/plan-issuance-solution/plan-issuance-solution-azure-services.png)

**Azure Key Vault** 서비스는 Azure AD 확인 가능한 자격 증명 발급 서비스를 시작할 때 생성되는 발급자 키를 저장합니다. 키와 메타데이터는 자격 증명 관리 작업을 실행하고 메시지 보안을 제공하는 데 사용됩니다.

각 발급자마다 서명, 업데이트, 복구에 사용되는 단일 키 집합이 있습니다. 이 키 집합은 생성되는 모든 확인 가능한 자격 증명의 모든 발급에 사용됩니다. 

**Azure Storage** 는 자격 증명 메타데이터 및 정의를 저장하는 데 사용됩니다. 특히 자격 증명에 대한 규칙 및 표시 파일을 저장하는 데 사용됩니다. 

* 표시 파일은 VC에 저장되는 클레임과 소유자의 지갑에 표시되는 방법을 결정합니다. 표시 파일에는 브랜딩 및 기타 요소도 포함됩니다. 규칙 파일의 크기는 50KB로 제한되지만 표시 파일은 150KB로 제한됩니다. [확인 가능한 자격 증명을 사용자 지정하는 방법](../verifiable-credentials/credential-design.md)을 참조하세요.

* 규칙은 확인 가능한 자격 증명의 필수 입력, 입력의 신뢰할 수 있는 원본, 출력 클레임에 대한 입력 클레임의 매핑을 설명하는 발급자 정의 모델입니다. 

   * **입력** – 클라이언트 사용에 대한 규칙 파일에 있는 모델의 하위 집합입니다. 하위 집합은 입력 세트, 입력을 가져올 위치, 확인 가능한 자격 증명을 얻기 위해 호출할 엔드포인트를 설명해야 합니다.

* 서로 다른 자격 증명에 대한 규칙 및 표시 파일은 서로 다른 컨테이너, 구독, 스토리지를 사용하도록 구성할 수 있습니다. 예를 들어 특정 VC의 관리를 소유하는 다른 팀에 권한을 위임할 수 있습니다. 

### <a name="azure-ad-verifiable-credentials-service"></a>Azure AD 확인 가능한 자격 증명 서비스

![Microsoft Azure AD 확인 가능한 자격 증명 서비스](media/plan-issuance-solution/plan-issuance-azure-active-directory-verifiable-credential-services.png)

Azure AD 확인 가능한 자격 증명 서비스를 사용하면 구성에 따라 VC를 발급하고 해지할 수 있습니다. 서비스는 다음을 수행합니다.

* DID(탈중앙화 식별자)를 프로비저닝하고 DID 문서를 ION에 쓰면 주체 및 검증 도구에서 사용할 수 있습니다. 각 발급자에는 테넌트당 단일 DID가 있습니다. 

* Key Vault에 키 집합을 프로비저닝합니다. 

* 발급 서비스 및 Microsoft Authenticator에서 사용하는 구성 메타데이터를 저장합니다.

### <a name="ion"></a>ION

![ION](media/plan-issuance-solution/plan-issuance-solution-ion.png)

Microsoft는 DID(탈중앙화 식별자) 구현을 위해 Bitcoin의 블록체인을 사용하는 [Sidetree 기반 네트워크](https://identity.foundation/sidetree/spec/)인 [ION(Identity Overlay Network)](https://identity.foundation/ion/)을 사용합니다. 발급자의 DID 문서는 ION에 저장되며 트랜잭션 당사자가 암호화 서명 확인을 수행하는 데 사용됩니다.

### <a name="microsoft-authenticator-application"></a>Microsoft Authenticator 애플리케이션

![Microsoft Authenticator 애플리케이션](media/plan-issuance-solution/plan-issuance-solution-authenticator.png)

Microsoft Authenticator는 사용자, Azure AD 확인 가능한 자격 증명 서비스, VC 발급에 사용된 계약에 설명되어 있는 종속성 간의 상호 작용을 오케스트레이션하는 모바일 애플리케이션입니다. 이 애플리케이션은 VC 주체의 프라이빗 키를 포함하여 VC 소유자가 VC를 저장하는 디지털 지갑 역할을 합니다. Authenticator는 검증을 위해 VC를 제공하는 데 사용되는 메커니즘이기도 합니다.

### <a name="issuance-business-logic"></a>발급 비즈니스 논리 

![발급 비즈니스 논리](media/plan-issuance-solution/plan-issuance-solution-business-logic.png)

발급 솔루션에는 사용자가 VC, ID 저장소 및 기타 특성 저장소를 요청하여 주체 및 기타 백 엔드 서비스에 대한 클레임 값을 가져오는 웹 프런트 엔드가 포함됩니다. 

웹 프런트 엔드는 딥 링크 또는 QR 코드를 생성하여 주체의 지갑에 발급 요청을 제공합니다. 계약의 구성에 따라 VC를 만들기 위한 요구 사항을 충족하려면 다른 구성 요소가 필요할 수 있습니다.

이러한 서비스는 ION이나 Azure AD 확인 가능한 자격 증명 발급 서비스와 통합하지 않아도 되는 지원 역할을 제공합니다. 이 계층에는 일반적으로 다음이 포함됩니다.

* **OIDC(Open ID Connect) 규격 서비스** - VC를 발급하는 데 필요한 id_tokens를 가져오는 데 사용됩니다. Azure AD 또는 Azure AD B2C 같은 기존 ID 시스템은 ID 서버와 같은 사용자 지정 솔루션과 마찬가지로 OIDC 규격 서비스를 제공할 수 있습니다.

* **특성 저장소** – 디렉터리 서비스 외부에 있을 수 있으며 VC를 발급하는 데 필요한 특성을 제공합니다. 예를 들어 학생 정보 시스템은 취득한 학위에 대한 클레임을 제공할 수 있습니다. 

* **추가 중간 계층 서비스** - 자격 증명을 발급하는 데 필요한 조회, 유효성 검사, 청구 및 기타 런타임 검사와 워크플로에 대한 비즈니스 규칙을 포함합니다.

웹 프런트 엔드 설정에 대한 자세한 내용은 [확인 가능한 자격 증명을 발급하도록 Azure AD 구성](../verifiable-credentials/enable-your-tenant-verifiable-credentials.md) 자습서를 참조하세요. 

## <a name="credential-design-considerations"></a>자격 증명 디자인 고려 사항

특정 사용 사례에 따라 자격 증명 디자인이 결정됩니다. 사용 사례에 따라 다음이 결정됩니다.

* 상호 운용성 요구 사항

* 사용자가 VC를 얻기 위해 ID를 증명해야 하는 방식

* 자격 증명에 필요한 클레임

* 자격 증명을 해지해야 하는 경우

 

### <a name="credential-use-cases"></a>자격 증명 사용 사례

Azure AD 확인 가능한 자격 증명을 사용하는 가장 일반적인 자격 증명 사용 사례는 다음과 같습니다.

**ID 검증**: 자격 증명이 여러 조건에 따라 발급됩니다. 여기에는 정부에서 발급한 문서(예: 여권 또는 운전 면허증)의 신뢰성 검증 및 해당 문서의 정보를 다음과 같은 다른 정보와 상호 연결하는 작업이 포함될 수 있습니다.

* 사용자의 셀피 

* 활동성 검증

이러한 종류의 자격 증명은 ID 검증이 필수적인 새 직원, 파트너, 서비스 공급자, 학생 및 기타 인스턴스의 ID 온보딩 시나리오에 적합합니다.

 

![ID 검증 사용 사례](media/plan-issuance-solution/plan-issuance-solution-identity-verification-use-cases.png)

**고용/멤버 자격 증명**: 사용자와 기관 간의 관계를 증명하기 위해 자격 증명이 발급됩니다. 이러한 종류의 자격 증명은 직원 또는 학생에게 할인을 제공하는 소매점과 같이 느슨하게 결합된 B2B 애플리케이션에 액세스하는 데 적합합니다. VC의 주요 값 중 하나는 이식성입니다. 발급되면 사용자는 여러 시나리오에서 VC를 사용할 수 있습니다. 

![고용 증명 사용 사례](media/plan-issuance-solution/plan-issuance-solution-employment-proof-use-cases.png)

더 많은 사용 사례는 [Verifiable Credentials Use Cases (w3.org)](https://www.w3.org/TR/vc-use-cases/)(확인 가능한 자격 증명 사용 사례(w3.org))를 참조하세요.

### <a name="credential-interoperability"></a>자격 증명 상호 운용성

디자인 프로세스의 일부로 상호 운용성 및 사용량을 최대화하는 데 적합한 산업별 스키마, 네임스페이스, 식별자를 조사합니다. 예제는 [Schema.org](https://schema.org/) 및 [DIF - Claims and Credentials Working Group](https://identity.foundation/working-groups/claims-credentials.html)(DIF - 클레임 및 자격 증명 작업 그룹)에서 확인할 수 있습니다.

공통 스키마는 표준이 계속 등장하고 있는 영역입니다. 이러한 작업의 한 가지 예는 [교육용 확인 가능한 자격 증명 태스크 포스](https://github.com/w3c-ccg/vc-ed)입니다. 조직의 업계에서 새로 등장하는 표준을 조사하고 기여하는 것이 좋습니다.

### <a name="credential-attributes"></a>자격 증명 특성 

자격 증명에 대한 사용 사례를 설정한 후에는 자격 증명에 포함할 특성을 결정해야 합니다. 검증 도구는 사용자가 제공하는 VC에서 클레임을 읽을 수 있습니다.

시나리오에 적용할 수 있는 산업별 표준과 스키마 외에도 다음 측면을 고려해야 합니다.

* **개인 정보 최소화**: 필요한 최소한의 개인 정보를 사용하여 사용 사례를 충족합니다. 예를 들어 직원과 졸업생에게 할인을 제공하는 전자상거래 웹 사이트에 사용되는 VC는 자격 증명에 성과 이름 클레임만 제공하여 충족할 수 있습니다. 채용 날짜, 직위, 부서 등의 추가 정보는 필요하지 않습니다.

* **추상 클레임 선호**: 각 클레임은 세부 정보를 최소화하면서 요구 사항을 충족해야 합니다. 예를 들어 “13”, “21”, “60” 등의 불연속 값을 포함하는 “ageOver”라는 클레임은 생년월일 클레임보다 더 추상적입니다.

* **해지 가능성 계획**: 자격 증명을 찾아 해지할 수 있는 메커니즘을 사용하도록 인덱스 클레임을 정의하는 것이 좋습니다. 계약당 하나의 인덱스 클레임을 정의하도록 제한됩니다. 인덱싱된 클레임에 대한 값은 클레임 값의 유일한 해시인 백 엔드에 저장되지 않는다는 점에 유의해야 합니다. 자세한 내용은 [이전에 발급한 확인 가능한 자격 증명 해지](../verifiable-credentials/how-to-issuer-revoke.md)를 참조하세요.

자격 증명 특성에 대한 추가 고려 사항은 [Verifiable Credentials Data Model 1.0 (w3.org)](https://www.w3.org/TR/vc-data-model/)(확인 가능한 자격 증명 데이터 모델 1.0(w3.org)) 사양을 참조하세요.

## <a name="plan-quality-attributes"></a>품질 특성 계획

### <a name="plan-for-performance"></a>성능 계획

모든 솔루션과 마찬가지로 성능을 계획해야 합니다. 중점적으로 다루는 핵심 영역은 대기 시간, 처리량 스토리지 및 스케일링 성능입니다. 릴리스 주기의 초기 단계에서는 성능이 문제가 되지 않습니다. 그러나 발급 솔루션을 채택하여 많은 확인 가능한 자격 증명이 발급되면 성능 계획이 솔루션의 중요한 부분이 될 수 있습니다.

다음은 성능을 계획할 때 고려해야 할 영역입니다.

* Azure AD 확인 가능한 자격 증명 발급 서비스는 서유럽, 북유럽, 미국 서부 2, 미국 중서부 Azure 지역에 배포됩니다. 서비스를 배포할 지역을 선택하지 않아도 됩니다. 

* 대기 시간을 제한하려면 위에 나열된 지역 중 요청이 발생될 것으로 예상되는 위치와 가장 가까운 지역에 발급 프런트 엔드 웹 사이트, 키 자격 증명 모음 및 스토리지를 배포합니다.

처리량 기반 모델:
* 발급자 서비스에는 [Azure Key Vault 서비스 제한 사항](../../key-vault/general/service-limits.md)이 적용됩니다. 

*  Azure Key Vault의 경우 다음과 같은 세 가지 서명 작업이 각 VC 발급에 포함됩니다.

      * 웹 사이트의 발급 요청에 대한 서명 작업

      * 만든 VC에 대한 서명 작업

      * 계약 다운로드를 위한 서명 작업

* Key Vault의 최대 서명 성능은 2,000개 서명/~10초입니다. 분당 약 12,000개 서명입니다. 즉, 솔루션은 분당 최대 4,000개의 VC 발급을 지원할 수 있습니다.

* 제한을 제어할 수는 없지만 [Azure Key Vault 제한 지침](../../key-vault/general/overview-throttling.md)을 읽어 보는 것이 좋습니다. 

* VC의 대규모 롤아웃 및 온보딩을 계획하고 있다면 제한을 초과하지 않도록 VC 만들기를 일괄 처리하는 것이 좋습니다.

* 발급 서비스에는 Azure Storage 제한이 적용됩니다. 일반적인 사용 사례에서는 스토리지가 문제가 되지 않습니다. 그러나 스토리지 제한을 초과하거나 스토리지가 병목 상태가 될 수 있다고 생각되면 다음을 검토하세요. 

   * 계획 프로세스의 일부로 [Blob 스토리지의 스케일링 성능 및 성능 목표 ](../../storage/blobs/scalability-targets.md)를 읽어보는 것이 좋습니다. Azure AD 확인 가능한 자격 증명 발급 서비스는 규칙을 읽고 파일을 표시하며 결과는 서비스에 의해 캐시됩니다.

   * [Blob 스토리지에 대한 성능 및 스케일링 성능 검사 목록 - Azure Storage](../../storage/blobs/storage-performance-checklist.md)도 검토하는 것이 좋습니다. 

성능 계획의 일부로 솔루션의 성능을 보다 잘 이해하기 위해 모니터링할 항목을 결정합니다. 애플리케이션 수준 웹 사이트 모니터링 외에도 VC 발급 모니터링 전략을 정의할 때 다음 사항을 고려하세요.

스케일링 성능을 위해 다음에 대한 메트릭을 구현하는 것이 좋습니다.

   * 발급 프로세스의 논리적 단계를 정의합니다. 예를 들면 다음과 같습니다.

   * 초기 요청 

   * QR 코드 또는 딥 링크 서비스

   * 특성 조회

   * Azure AD 확인 가능한 자격 증명 발급 서비스 호출

   * 발급된 자격 증명

   * 단계에 따라 메트릭을 정의합니다.

      * 총 요청 수(볼륨)

      * 시간 단위당 요청(처리량)

      * 소요된 시간(대기 시간)

* 다음을 사용하여 Azure Key Vault 및 Storage를 모니터링합니다.

   * [Azure Key Vault 모니터링 및 경고](../../key-vault/general/alert.md)

   * [Azure Blob Storage 모니터링](../../storage/blobs/monitor-blob-storage.md)

* 비즈니스 논리 계층에 사용되는 구성 요소를 모니터링합니다. 

### <a name="plan-for-reliability"></a>안정성 계획

안정성을 계획하려면 다음을 수행하는 것이 좋습니다.

* 가용성 및 중복성 목표를 정의한 후 다음 가이드를 사용하여 목표를 달성하는 방법을 파악하세요.

   * [Azure Key Vault 가용성 및 중복성 - Azure Key Vault](../../key-vault/general/disaster-recovery-guidance.md)

   * [재해 복구 및 스토리지 계정 장애 조치(failover) - Azure Storage](../../storage/common/storage-disaster-recovery-guidance.md)

* 프런트 엔드 및 비즈니스 계층의 경우 솔루션은 여러 가지 방법으로 매니페스트할 수 있습니다. 모든 솔루션과 마찬가지로 식별되는 종속성에 대해 종속성이 복원력 있고 모니터링되는지 확인합니다. 

드문 경우이지만 Azure AD 확인 가능한 자격 증명 발급 서비스, Azure Key Vault 또는 Azure Storage 서비스를 사용할 수 없게 되면 전체 솔루션을 사용할 수 없게 됩니다.

### <a name="plan-for-compliance"></a>규정 준수 계획

조직에는 산업, 트랜잭션 유형, 작업 국가와 관련된 특정 규정 준수 요구 사항이 있을 수 있습니다. 

**데이터 보존**: Azure AD 확인 가능한 자격 증명 발급 서비스는 일부 Azure 지역에 배포됩니다. 이 서비스는 컴퓨팅 함수에만 사용됩니다. Microsoft는 확인 가능한 자격 증명의 값을 Microsoft 시스템에 저장하지 않습니다. 그러나 VC를 발급할 때 발급 프로세스의 일부로 개인 데이터가 전송되고 사용됩니다. VC 서비스 사용은 데이터 보존 요구 사항에 영향을 주지 않아야 합니다. ID 검증의 일부로 개인 정보를 저장하는 경우 규정 준수 요구 사항을 충족하는 방식으로 해당 지역에 저장해야 합니다. Azure 관련 지침은 Microsoft 보안 센터 웹 사이트를 참조하세요. 

**자격 증명 해지**: 조직에서 자격 증명을 해지해야 하는지 여부를 결정합니다. 예를 들어 직원이 퇴사하는 경우 관리자가 자격 증명을 해지해야 할 수 있습니다. 또는 자격 증명이 운전 면허증 용도로 발급된 경우 소유자가 운전 면허증이 일시 중단되도록 하는 일을 하면 VC를 해지해야 할 수 있습니다. 자세한 내용은 [이전에 발급한 확인 가능한 자격 증명 해지](how-to-issuer-revoke.md)를 참조하세요.

**자격 증명 만료**: 자격 증명을 만료할지 여부와 그럴 경우 어떤 상황에서 만료할지를 결정합니다. 예를 들어 운전 면허증을 증명하기 위해 VC를 발급하는 경우 몇 년 후에 VC가 만료될 수 있습니다. 사용자와의 연결을 검증하기 위해 VC를 발급하는 경우 사용자가 매해 돌아와서 업데이트된 최신 버전의 VC를 받도록 연 단위로 VC를 만료하는 것이 좋습니다.

## <a name="plan-for-operations"></a>작업 계획

작업을 계획할 때는 지원하는 다양한 고객의 문제 해결, 보고, 구분에 사용할 스키마를 개발하는 것이 중요합니다. 또한 운영 팀이 VC 해지 실행을 담당하는 경우 해당 프로세스를 정의해야 합니다. 고유한 각 발급 요청과 연결할 수 있는 로그 항목을 확인할 수 있도록 프로세스의 각 단계를 상호 연결해야 합니다. 감사를 위해 각 자격 증명 발급 시도를 개별적으로 캡처하는 것이 좋습니다. 특히 다음에 대해 주의하세요.

* 고객과 지원 엔지니어가 필요에 따라 참조할 수 있는 고유한 트랜잭션 ID를 생성합니다.

* Azure Key Vault 트랜잭션 로그와 솔루션 발급 부분의 트랜잭션 ID를 상호 연결하는 메커니즘을 고안합니다.

* 여러 고객을 대신하여 VC를 발급하는 ID 검증 서비스인 경우 고객 관련 보고 및 청구를 위해 고객 또는 계약 ID를 모니터링하고 완화합니다.

* 여러 고객을 대신하여 VC를 발급하는 ID 검증 서비스인 경우 고객 관련 보고 및 청구, 모니터링, 완화를 위해 고객 또는 계약 ID를 사용합니다. 

## <a name="plan-for-security"></a>보안 계획

보안에 중점을 둔 디자인 고려 사항의 일부로 다음을 수행하는 것이 좋습니다.

* 키 관리:

   * VC 발급을 위한 전용 Key Vault를 만듭니다. Azure AD 확인 가능한 자격 증명 발급 서비스 및 발급 서비스 프런트 엔드 웹 사이트 서비스 주체로 Azure Key Vault 권한을 제한합니다. 

   * Azure Key Vault를 권한이 높은 시스템으로 처리합니다. Azure Key Vault는 고객에게 자격 증명을 발급합니다. 사용자 ID에는 Azure Key Vault 서비스에 대한 권한이 없도록 하는 것이 좋습니다. 관리자는 Key Vault에 한 번만 액세스하면 됩니다. Azure Key Vault 사용에 대한 자세한 모범 사례는 [Key Vault에 대한 Azure 보안 기준](/security/benchmark/azure/baselines/key-vault-security-baseline)을 참조하세요.

* 발급 프런트 엔드 웹 사이트를 나타내는 서비스 주체:

   * Azure Key Vault에 대한 액세스 권한을 부여하는 전용 서비스 주체를 정의합니다. 웹 사이트가 Azure에 있는 경우 [Azure 관리 ID](../managed-identities-azure-resources/overview.md)를 사용하는 것이 좋습니다. 

   * 웹 사이트와 사용자를 나타내는 서비스 주체를 단일 신뢰 경계로 처리합니다. 여러 웹 사이트를 만들 수 있지만 발급 솔루션에 대한 키 집합은 하나뿐입니다. 

보안 로깅 및 모니터링의 경우 다음을 권장합니다.

* Azure Key Vault의 로깅 및 경고를 사용하도록 설정하여 자격 증명 발급 작업, 키 추출 시도, 권한 변경을 추적하고 구성 변경에 대한 경고를 모니터링하고 보냅니다. 자세한 내용은 [Key Vault 로깅을 사용하도록 설정하는 방법](../../key-vault/general/howto-logging.md)을 참조하세요. 

* Azure Storage 계정의 로깅을 사용하도록 설정하여 구성 변경에 대한 경고를 모니터링하고 보냅니다. 자세한 내용은 [Azure Blob Storage 모니터링](../../storage/blobs/monitor-blob-storage.md)을 참조하세요.

* 장기 보존을 위해 [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel) 같은 SIEM(보안 정보 및 이벤트 관리) 시스템에 로그를 보관합니다.

* 다음을 사용하여 스푸핑 위험을 완화합니다.

   * 고객이 발급자 브랜딩을 식별하는 데 도움이 되는 DNS 검증.

   * 최종 사용자에게 의미 있는 도메인 이름.

   * 최종 사용자가 인식하는 신뢰할 수 있는 브랜딩.

* DDOS(분산형 서비스 거부) 및 Key Vault 리소스 소모 위험을 완화합니다. VC 발급 요청을 트리거하는 모든 요청은 서비스 제한에 따라 발생하는 Key Vault 서명 작업을 생성합니다. 발급 요청을 생성하기 전에 인증 또는 captcha를 통합하여 트래픽을 보호하는 것이 좋습니다.

Azure 환경 관리에 대한 지침은 [Azure Security Benchmark](/security/benchmark/azure/)(Azure 보안 벤치마크) 및 [Securing Azure environments with Azure Active Directory](https://aka.ms/AzureADSecuredAzure)(Azure Active Directory를 사용하여 Azure 환경 보호)를 참조하세요. 이러한 가이드는 Azure Key Vault, Azure Storage, 웹 사이트, 기타 Azure 관련 서비스 및 기능을 포함하여 기본 Azure 리소스 관리에 대한 모범 사례를 제공합니다.

## <a name="additional-considerations"></a>기타 고려 사항

POC를 완료하면 생성된 모든 정보 및 설명서를 수집하고 발급자 구성을 삭제하는 것이 좋습니다. 이렇게 하면 POC 기간이 만료된 후 확인 가능한 자격 증명이 발급되는 것을 방지할 수 있습니다. 

Key Vault 구현 및 운영에 대한 자세한 내용은 [Key Vault 사용에 대한 모범 사례](../../key-vault/general/best-practices.md)를 참조하세요. Active Directory를 사용하여 Azure 환경 보호에 대한 자세한 내용은[Securing Azure environments with Azure Active Directory](https://aka.ms/AzureADSecuredAzure)(Azure Active Directory를 사용하여 Azure 환경 보호)를 참조하세요. 

## <a name="next-steps"></a>다음 단계

[아키텍처 개요 읽어보기](introduction-to-verifiable-credentials-architecture.md)

[확인 솔루션 계획](plan-verification-solution.md)

[확인 가능한 자격 증명 시작](get-started-verifiable-credentials.md)
