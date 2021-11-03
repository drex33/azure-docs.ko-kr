---
title: Service Fabric에서 인증서 관리
description: X.509 인증서를 사용하여 보호되는 Service Fabric 클러스터에서 인증서를 관리하는 방법 알아보기.
ms.topic: conceptual
ms.date: 04/10/2020
ms.custom: sfrev, devx-track-azurepowershell
ms.openlocfilehash: 768fe83a0ce4bc0507f21b72e1c917e60c7ec971
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131075128"
---
# <a name="certificate-management-in-service-fabric-clusters"></a>Service Fabric 클러스터에서 인증서 관리

이 문서는 Azure Service Fabric 클러스터의 통신을 보호하는 데 사용되는 인증서의 관리 측면에 대해 설명하고, [Service Fabric 클러스터 보안](service-fabric-cluster-security.md) 소개 및 [Service Fabric의 X.509 인증서 기반 인증](cluster-security-certificates.md)에 대한 설명을 보완합니다. 여기서 reader는 기본적인 보안 개념뿐만 아니라 클러스터의 보안을 구성하기 위해 Service Fabric이 공개하는 컨트롤에 대해 잘 알고 있다고 가정합니다.  

해당 제목에서 다루는 양상:

* ‘인증서 관리’는 정확히 무엇인가요?
* 인증서 관리에 포함된 역할 및 엔터티
* 인증서 경험
* 예제 심층 분석
* 문제 해결 및 FAQ(자주 묻는 질문)

시작 전 공지: 이 문서는 이론적인 측면을 서비스, 기술 등을 비롯한 관련 세부 정보가 필요한 실습 예제와 쌍으로 연결하는 것을 목표로 합니다. 대상 그룹의 많은 부분이 Microsoft 내부에 있으므로 Microsoft Azure 관련 서비스, 기술, 제품 등을 참조합니다. Microsoft 관련 세부 정보가 적용되지 않는 경우, 확인 또는 지침을 얻으려면 의견 섹션에 질문을 남겨 주세요.

## <a name="defining-certificate-management"></a>인증서 관리의 정의
[도우미 문서](cluster-security-certificates.md)에서 보았듯이 인증서는 기본적으로 비대칭 키 쌍을 나타내는 엔터티를 설명하는 특성을 사용하여 비대칭 키 쌍을 바인딩하는 암호화 개체입니다. 그러나 인증서는 ‘소멸성’ 개체이기도 합니다. 즉, 수명이 유한하고, 우발적 공개나 성공적인 익스플로잇과 같은 위협으로 인해 보안 관점에서 보았을 때 쓸모 없게 될 수 있습니다. 따라서 정기적으로 또는 보안 인시던트 응답으로 인증서를 변경해야 합니다. 관리의 다른 측면(및 자체의 전체 항목)은 인증서 프라이빗 키 또는 인증서를 조달 및 프로비저닝에 관련된 엔터티의 ID을 지키는 비밀을 보호하는 것입니다. 인증서를 가져오고 ‘인증서 관리’로 필요한 곳에 안전하게 전송하는 데 사용되는 프로세스와 프로시저를 참조합니다. 등록, 정책 설정, 권한 부여 컨트롤과 같은 일부 관리 작업은 이 문서에서 다루지 않습니다. 프로비저닝, 갱신, 키 재지정 또는 해지와 같은 사항은 Service Fabric에만 해당되는 것으로서, 그러한 작업을 이해하는 것이 클러스터를 적절하게 보호하는 데 도움이 되기 때문에 일정 부분 다루게 됩니다. 

목표는 프로세스가 사용자-터치-프리이면서 클러스터의 방해 없는 가용성을 보장하고 보안 보증을 제공하기 위해 가능한 최대한 인증서 관리를 자동화하는 것입니다. 현재 Azure Service Fabric 클러스터에서 목표를 달성할 수 있습니다. 이 문서의 나머지 부분은 인증서 관리를 먼저 분해하고 그 다음 오토롤오버를 사용하도록 설정하는 방법을 집중적으로 설명합니다.

구체적인 범위 항목은 다음과 같습니다.
  - 인증서 관리의 컨텍스트에서 owner와 플랫폼 간 속성의 분리와 관련된 가정
  - 발급에서 사용에 이르기까지 긴 인증서 파이프라인
  - 인증서 회전 - 이유, 방법 및 시기
  - 어떤 문제가 발생할 수 있나요?

도메인 이름 보호/관리, 인증서에 등록 또는 인증서 발급을 집행하는 권한 부여 컨트롤 설정 등의 측면은 이 문서에서 다루지 않습니다. 선호하는 PKI(공개 키 인프라) 서비스의 RA(등록 인증 기관)를 참조하세요. Microsoft 내부 소비자: Azure 보안에 연결하세요.

## <a name="roles-and-entities-involved-in-certificate-management"></a>인증서 관리에 포함된 역할 및 엔터티
Service Fabric 클러스터의 보안 방식은 ‘클러스터 owner가 선언하고 Service Fabric 런타임이 집행’하는 사례입니다. 이를 통해 클러스터의 기능에 참여하는 ID의 인증서, 키 또는 기타 자격 증명은 서비스 자체에서 제공되지 않습니다. 모두 클러스터 owner가 선언합니다. 또한 클러스터 owner는 클러스터에 인증서를 프로비저닝하고, 필요에 따라 갱신하고, 항상 인증서의 보안을 보장해야 합니다. 구체적으로 말하면 클러스터 owner는 다음을 확인해야 합니다.
  - 클러스터 매니페스트의 NodeType 섹션에 선언된 인증서는 [프레젠테이션 규칙](cluster-security-certificates.md#presentation-rules)에 따라 해당 유형의 각 노드에서 찾을 수 있습니다.
  - 위에 선언된 인증서는 해당 개인 키를 포함하여 설치됩니다.
  - 프레젠테이션 규칙에 선언된 인증서는 [유효성 검사 규칙](cluster-security-certificates.md#validation-rules)을 통과해야 합니다. 

Service Fabric은 다음의 책임을 맡습니다.
  - 클러스터 정의의 선언과 일치하는 인증서 찾기/검색  
  - ‘필요’에 따라 Service Fabric으로 제어되는 엔터티에 해당 개인 키에 대한 액세스 권한 부여
  - 설정된 보안 모범 사례 및 클러스터 정의에 따라 엄격한 인증서 유효성 검사
  - 임박한 인증서 만료 시 또는 인증서 유효성 검사의 기본 단계를 수행하는 데 실패할 때 경고
  - 호스트의 기본 구성이 클러스터 정의의 인증서 관련 측면을 (일정 수준) 충족하는지 유효성 검사 

인증서 관리 부담(활성 작업)은 클러스터 owner에게만 있습니다. 다음 섹션에서는 사용 가능한 메커니즘과 클러스터에 미치는 영향에 대한 각 관리 작업에 대해 자세히 알아봅니다.

## <a name="the-journey-of-a-certificate"></a>인증서 경험
Service Fabric 클러스터의 컨텍스트에서 발급에서 사용까지 인증서의 진행 상황을 신속하게 다시 확인해 보겠습니다.

  1. 도메인 owner는 PKI의 RA로 뒤이은 인증서와 연결하고 싶은 도메인 또는 주체를 등록합니다. 그러면 인증서는 전술한 도메인 또는 주체의 소유권에 대한 증거를 구성합니다.
  2. 또한 도메인 owner는 인증된 요청자의 ID, 즉 지정된 도메인 또는 주체를 사용하여 RA에 인증서 등록을 요청할 수 있는 엔터티를 지정합니다. Microsoft Azure에서 기본 ID 공급자는 Azure Active Directory로, 이에 상응하는 AAD ID(또는 보안 그룹)을 통해 권한을 가진 요청자를 지정하게 됩니다.
  3. 그런 다음 인증된 요청자는 비밀 관리 서비스를 통해 인증서에 등록합니다. Microsoft Azure에서 선택한 SMS는 안전하게 저장하고 권한을 가진 엔터티의 비밀/인증서 검색을 허용하는 Azure Key Vault(AKV)입니다. 또한 AKV는 연결된 인증서 정책에 구성된 대로 인증서를 갱신/키 재지정합니다. (AKV는 ID 공급자로 AAD를 사용합니다.)
  4. ‘프로비저닝 에이전트’라고 하는 권한 있는 검색기는 자격 증명 모음에서 개인 키를 포함한 인증서를 검색하고 클러스터를 호스트하는 컴퓨터에 설치합니다.
  5. (각 노드에서 높은 권한으로 실행되는) Service Fabric 서비스는 허용된 Service Fabric 엔터티가 인증서에 액세스할 권한을 부여합니다. 그들은 로컬 그룹에 의해 지정되고 ServiceFabricAdministrators와 ServiceFabricAllowedUsers 간에 분할됩니다.
  6. Service Fabric 런타임은 페더레이션을 설정하거나 인증된 클라이언트에서 인바운드 요청을 인증하기 위해 인증서를 액세스하고 사용합니다.
  7. 프로비저닝 에이전트는 자격 증명 모음 인증서를 모니터링하고 갱신 검색 시 프로비저닝 흐름을 트리거합니다. 그런 다음 클러스터 owner는 필요한 경우 클러스터 정의를 업데이트하여 인증서를 롤오버하겠다는 의사를 표시합니다.
  8. 프로비저닝 에이전트 또는 클러스터 owner는 사용하지 않는 인증서를 정리/삭제해야 합니다.
  
이에 대하여 위의 시퀀스에서 처음 두 단계는 거의 관련이 없습니다. 관련되는 유일한 사항은 인증서의 주체 일반 이름이 클러스터 정의에 선언된 DNS 이름뿐입니다.

실행 단계는 아래에 나와 있습니다. 지문 및 일반 이름으로 선언된 인증서 간에는 각각 프로비저닝 차이가 있습니다.

*그림 1.* 지문으로 선언된 인증서의 발급 및 프로비저닝 흐름입니다.
![지문으로 선언된 인증서 프로비저닝][Image1]

*그림. 2.* 주체 일반 이름으로 선언된 인증서의 발급 및 프로비저닝 흐름입니다.
![주체 일반 이름으로 선언된 인증서 프로비저닝][Image2]

### <a name="certificate-enrollment"></a> 인증서 등록
이 항목은 Key Vault [설명서](../key-vault/certificates/create-certificate.md)에 자세히 나와 있습니다. 연속성 및 간편한 참조를 위해 여기에 개요를 제시합니다. 권한을 가진 인증서 요청자는 Azure를 컨텍스트로 계속 사용하고, Azure Key Vault를 비밀 관리 서비스로 사용하여 자격 증명 모음 owner가 부여하는 자격 증명 모음에 대해 적어도 인증서 관리 권한 이상이 있어야 합니다. 그러면 요청자는 다음과 같이 인증서에 등록합니다.
    - 인증서의 도메인/주체, 원하는 발급자, 키 유형과 길이 원하는 인증서 발급자, 키 사용 및 기간, 의도하는 키 사용량을 지정하는 Azure Key Vault(AKV)에서 인증서 정책을 만듭니다. 자세한 내용은 [Azure Key Vault의 인증서](../key-vault/certificates/certificate-scenarios.md)를 참조하세요. 
    - 위에 지정된 정책을 사용하여 동일한 자격 증명 모음에 인증서를 만듭니다. 이를 통해 자격 증명 모음 개체인 키 쌍을 생성하고, 개인 키로 서명 요청에 서명되고 서명하기 위해 지정된 발급자에게 전달되는 인증서를 생성합니다.
    - 발급자(인증서 인증 기관)가 서명된 인증서를 사용하여 회신하면 결과가 자격 증명 모음에 병합되고 인증서를 다음 작업에 사용할 수 있습니다.
      - {vaultUri}/certificates/{name}: 공개 키와 메타데이터를 포함하는 인증서
      - {vaultUri}/keys/{name}: 인증서의 개인 키를 암호화 작업에 사용할 수 있습니다(래핑/래핑 해제, 서명/확인)
      - {vaultUri}/secrets/{name}: 개인 키가 포함된 인증서로, 보호되지 않는 pfx 또는 pem 파일로 다운로드할 수 있습니다.  
    사실 자격 증명 모음 인증서는 정책을 공유하는 시간순 인증서 인스턴스의 줄입니다. 인증서 버전은 정책의 수명 및 갱신 특성에 따라 만들어집니다. 자격 증명 모음 인증서는 주체 또는 도메인/DNS 이름을 공유하지 않는 것이 좋습니다. 주체는 같지만 발급자, 키 사용과 같은 다른 특성은 상당히 다른 다양한 자격 증명 모음 인증서에서 인증서 인스턴스를 프로비저닝하는 것은 클러스터에서 방해가 될 수 있습니다.

이 시점에서 인증서가 자격 증명 모음에 있으므로 사용할 준비가 됩니다. 이후:

### <a name="certificate-provisioning"></a>인증서 프로비저닝
자격 증명 모음에서 개인 키가 포함된 인증서를 검색하여 클러스터의 각 호스트에 설치하는 엔터티인 ‘프로비저닝 에이전트’를 언급했습니다. (Service Fabric은 인증서를 프로비저닝하지 않습니다.) 해당 컨텍스트에서 클러스터는 Azure VM 및 Virtual Machine Scale Sets의 컬렉션에서 호스트됩니다. Azure에서 다음과 같은 메커니즘을 사용하여 자격 증명 모음에서 VM/VMSS로 인증서를 프로비저닝할 수 있습니다. 이는 위와 같이 프로비저닝 에이전트가 자격 증명 모음 owner에 의해 자격 증명 모음에 대한 ‘get’ 권한을 이전에 부여받은 것으로 가정합니다. 
  - 애드혹: 운영자가 자격 증명 모음에서 인증서를 검색하고 (pfx/PKCS #12 또는 pem) 각 노드에 설치합니다.
  - 배포하는 동안 가상 머신 확장 집합으로서 ‘비밀’: 컴퓨팅 서비스는 운영자 대신 자사 ID를 사용하여 템플릿 배포에 사용되는 자격 증명 모음 인증서를 검색하여 ([이렇게](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.yml)) 가상 머신 확장 집합의 각 노드에 설치합니다. 참고로 이렇게 하면 버전이 지정된 비밀을 프로비저닝할 수 있습니다.
  - [Key Vault VM 확장](../virtual-machines/extensions/key-vault-windows.md)을 사용하면 관찰된 인증서를 정기적으로 새로 고쳐 버전이 없는 선언을 사용하여 인증서를 프로비저닝할 수 있습니다. 이 경우 VM/VMSS는 관찰된 인증서를 포함하여 자격 증명 모음에 대한 액세스 권한이 부여된 ID인 [관리 ID](../virtual-machines/security-policy.md#managed-identities-for-azure-resources)가 있어야 합니다.

보안에서 가용성에 이르는 여러 가지 이유로 애드혹 메커니즘을 사용하지 않는 것이 좋습니다. 이에 대해서는 여기서 자세히 설명하지 않습니다. 자세한 내용은 [가상 머신 확장 집합 인증서](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.yml)를 참조하세요.

VMSS-/컴퓨팅-기반 프로비저닝은 보안 및 가용성 이점을 제공하지만 제한도 있습니다. 버전이 지정된 비밀로 인증서를 사용해야 합니다. 그러면 인증서는 지문으로 선언된 인증서로 보호된 클러스터에만 적합하게 됩니다. 이와 대조적으로 Key Vault VM 확장 기반 프로비저닝은 항상 관찰된 각 인증서의 최신 버전을 설치합니다. 그러면 주체 일반 이름으로 선언된 인증서로 보호된 클러스터에만 적합합니다. 인스턴스(지문)에 의해 선언된 인증서에는 자동 새로 고침 프로비저닝 메커니즘(예: KVVM 확장)을 사용하면 안 됩니다. 가용성이 손실될 위험이 크기 때문입니다.

다른 프로비저닝 메커니즘이 있을 수 있습니다. 위의 사항은 현재 Azure Service Fabric 클러스터에 대하여 허용됩니다.

### <a name="certificate-consumption-and-monitoring"></a>인증서 소비 및 모니터링
앞서 설명한 것처럼 Service Fabric 런타임은 클러스터 정의에 선언된 인증서를 찾고 사용하는 일을 담당합니다. [인증서 기반 인증](cluster-security-certificates.md)에 대한 문서는 Service Fabric이 각각 프레젠테이션 및 유효성 검사 규칙을 구현하는 방법에 대해 자세히 설명하고 있습니다. 액세스, 권한 부여 및 모니터링을 살펴보겠습니다.

Service Fabric의 인증서는 페더레이션 레이어의 상호 인증에서 관리 엔드포인트에 대한 TLS 인증까지 다양한 용도로 사용됩니다. 이렇게 하려면 다양한 구성 요소 또는 시스템 서비스에서 인증서의 개인 키에 액세스할 수 있어야 합니다. Service Fabric 런타임은 인증서 저장소를 정기적으로 검색하여 알려진 각 표시 규칙에 대해 일치하는 항목을 찾습니다. 일치하는 각 인증서에 해당되는 개인 키가 있으며, 임의 액세스 제어 목록이 사용 권한을 포함하도록 업데이트됩니다. 일반적으로 이를 필요로 하는 각 ID에 대한 읽기 및 실행 권한이 부여됩니다. (이 프로세스는 비공식적으로 ‘ACLing’라고 불립니다.) 프로세스는 1분 주기로 실행되며 설정 또는 엔드포인트 인증서로 암호화하는 데 사용되는 인증서와 같은 ‘응용 프로그램’ 인증서도 포함합니다. ACLing는 프레젠테이션 규칙을 따르며, 따라서 지문으로 선언된 인증서와 결과 클러스터 구성 업데이트가 없는 자동으로 새로 고침되는 인증서에는 액세스할 수 없습니다.    

### <a name="certificate-rotation"></a>인증서 회전
참고로, IETF [RFC 3647](https://tools.ietf.org/html/rfc3647)은 [갱신](https://tools.ietf.org/html/rfc3647#section-4.4.6)을 대체 인증서와 같은 특성을 가진 인증서의 발급으로 정의합니다. 즉, 발급자, 주체의 공개 키 및 정보가 유지되고, 새 키 쌍을 사용하여 인증서 발급으로 키가 [재지정](https://tools.ietf.org/html/rfc3647#section-4.4.7)되며, 발급자 변경 여부에 대한 제한은 없습니다. 이 구분이 중요할 수 있는 경우(발급자 고정을 사용하여 주체 일반 이름으로 선언된 인증서의 경우를 고려), 두 시나리오를 모두 포함하기 위해 중립 용어인 ‘회전’을 선택할 것입니다. (비공식적으로 사용하는 경우 ‘갱신’이란 키 재지정을 가리킨다는 것을 염두에 두어야 합니다.) 

이전에는 자동 인증서 회전을 지원하는 Azure Key Vault를 살펴보았습니다. 관련 인증서 정책은 자격 증명 모음에서 인증서를 회전할 때 만료 전 일 수 또는 총 수명의 백분율에 따라 지정 시간을 정의합니다. 프로비저닝 에이전트는 이 시점 이후에 호출되어야 하며, 현재-이전 인증서가 만료되기 전에 이 새 인증서를 클러스터의 모든 노드에 배포해야 합니다. Service Fabric은 (현재 클러스터에서 사용되는) 인증서의 만료 날짜가 미리 결정된 간격보다 빨리 발생하는 경우 상태 경고를 발생시킬 수 있습니다. 자격 증명 모음 인증서를 관찰하도록 구성된 자동 프로비저닝 에이전트(즉, KeyVault VM 확장)는 주기적으로 자격 증명 모음을 설문 조사하여 회전을 검색하고 새 인증서를 검색 및 설치합니다. VM/VMSS ‘비밀’ 기능을 통해 수행된 프로비저닝에는 새 인증서에 해당하는 버전 지정된 KeyVault URI을 사용하여 VM/VMSS를 업데이트하는 권한 있는 운영자가 필요합니다.

두 경우 모두에서 회전되는 인증서가 모든 노드에 프로비저닝된다는 사실과 회전을 검색하는 데 사용할 Service Fabric 메커니즘에 대해 알아보았습니다. 다음에 발생하는 일을 살펴보겠습니다. 클러스터는 물론 Service Fabric 런타임 내 새로운 연결에 대한 주체 일반 이름으로 선언된 클러스터 인증서에 적용되는 회전은
  - 가장 최근에 발급된 일치 인증서(‘NotBefore’ 속성의 가장 큰 값)를 찾아 선택한다고 가정해 봅시다. 이는 Service Fabric 런타임의 이전 버전에서 변경된 사항입니다.
  - 기존 연결은 활성 상태로 유지되어 자연스럽게 만료되거나 종료될 수 있습니다. 그러면 내부 처리기에 새 일치 항목이 있다는 알림이 표시됩니다.

> [!NOTE] 
> 7\.2.445(7.2 CU4) 이전 버전에서 Service Fabric은 곧 만료되는 인증서(가장 먼 ‘NotAfter’ 속성이 있는 인증서)를 선택했습니다.

이는 다음과 같은 중요한 관찰로 변환됩니다.
  - 만료 날짜가 현재 사용 중인 인증서보다 빠른 경우 갱신 인증서를 무시할 수 있습니다.
  - 클러스터 또는 호스트된 응용 프로그램의 가용성은 지시문보다 우선하여 인증서를 회전합니다. 클러스터는 결국 새로운 인증서를 수렴하지만 타이밍을 보장하지는 않습니다. 라디안은 다음과 같이 표시됩니다.
  - 관찰자가 순환되는 인증서가 해당 선행 작업을 완전히 대체했음을 즉시 명확하게 알지 못할 수도 있습니다. (클러스터 인증서의 경우) 호스트 컴퓨터를 다시 부팅하는 것이 확인하는 유일한 방법입니다. 클러스터의 임대 연결을 구성하는 커널 모드 구성 요소는 영향을 받지 않으므로 Service Fabric 노드를 다시 시작하는 것만으로는 충분하지 않습니다. 또한 VM/VMSS를 다시 시작하면 일시적으로 가용성이 손실될 수 있습니다. (애플리케이션 인증서의 경우 각 애플리케이션 인스턴스만 다시 시작해도 됩니다.)
  - 유효성 검사 규칙을 충족하지 않는 키가 재지정된 인증서를 도입하면 효과적으로 클러스터를 중단할 수 있습니다. 이에 대한 가장 일반적인 예는 예기치 않은 발급자의 경우입니다. 클러스터 인증서는 발급자 고정을 사용하여 주체 일반 이름으로 선언되지만, 회전된 인증서는 새로운/미선언 발급자가 발급한 것입니다.     

### <a name="certificate-cleanup"></a>인증서 정리
이번에는 Azure에서 인증서를 명시적으로 제거하기 위한 프로비저닝이 없습니다. 지정된 시간에 지정된 인증서가 사용되고 있는지 여부를 확인하는 것은 일반적으로 중요하지 않은 작업입니다. 이는 클러스터 인증서보다 응용 프로그램 인증서에서 더 어렵습니다. Service Fabric 자체는 프로비저닝 에이전트가 아니라서 어떤 경우에도 사용자가 선언한 인증서를 삭제하지 않습니다. 표준 프로비저닝 메커니즘의 경우:
  - VM/VMSS 비밀로 선언된 인증서는 VM/VMSS 정의에서 참조되는 동안은 프로비저닝되고 자격 증명 모음에서 검색됩니다(자격 증명 모음 비밀/인증서를 삭제하면 후속 VM/VMSS 배포가 실패합니다. 마찬가지로 자격 증명 모음에서 비밀 버전을 사용하지 않도록 설정하면 해당 비밀 버전을 참조하는 VM/VMSS 배포도 실패합니다).
  - KeyVault VM 확장을 통해 프로비저닝된 인증서의 이전 버전은 VM/VMSS 노드에 있을 수도 또는 없을 수도 있습니다. 에이전트는 현재 버전만 검색하고 설치하며 인증서를 제거하지 않습니다. 노드 재이미징(일반적으로 매월)은 인증서 저장소를 OS 이미지의 콘텐츠로 다시 설정하므로 이전 버전이 암묵적으로 제거됩니다. 그러나 가상 머신 확장 집합을 확장하면 현재 버전의 관찰된 인증서만 설치됩니다. 따라서 설치된 인증서와 관련하여 노드가 균질할 것으로 가정해서는 안 됩니다.   

## <a name="simplifying-management---an-autorollover-example"></a>관리 간소화-오토롤오버 예제
메커니즘, 제한 사항, 복잡한 규칙 및 정의에 대해 설명하고 심각한 중단을 예측했습니다. 이는 모든 문제를 방지하기 위해 자동 인증서 관리를 설정하는 방법을 보여 주는 것일 수 있습니다. PaaSv2 가상 머신 확장 집합에서 실행되는 Azure Service Fabric 클러스터의 컨텍스트에서 다음과 같이 비밀 관리를 위해 Azure Key Vault를 사용하고 관리 ID를 활용합니다.
  - 인증서 유효성 검사는 지문 고정에서 제목 + 발급자 고정으로 변경됩니다. 지정된 발급자의 지정된 주체를 사용하는 인증서는 동일하게 신뢰할 수 있습니다.
    - 인증서는 신뢰할 수 있는 저장소(Key Vault)에 등록되고 에이전트, 이 경우에는 KeyVault VM 확장에 의해 새로 고침됩니다.
    - 인증서 프로비저닝은 배포 시간 및 버전 기반(ComputeRP에서 수행됨)에서 배포 후 및 버전 감소 키 KeyVault URI를 사용하는 것으로 변경됩니다.
    - 사용자 할당 관리 ID를 통해 KeyVault에 대한 액세스 권한이 부여됩니다. UA ID는 배포 중에 가상 머신 확장 집합에 대해 생성되고 할당됩니다.
    - 에이전트(KV VM 확장)는 배포 후 가상 머신 확장 집합의 각 노드에서 관찰된 인증서를 폴링하고 새로 고칩니다. SF는 가장 멀리 떨어진 유효한 인증서를 자동으로 선택하기 때문에 인증서 회전이 완전히 자동화됩니다.

이 시퀀스는 완전히 스크립트가 가능하고 자동으로 제공되며, 인증서 오토롤오버로 구성된 클러스터의 사용자-터치-프리 초기 배포를 허용합니다. 자세한 단계는 아래에 나와 있습니다. PowerShell cmdlet과 json 템플릿 조각을 함께 사용해 보겠습니다. Azure와 상호 작용하는 지원되는 모든 방법을 사용하여 동일한 기능을 달성할 수 있습니다.

> [!NOTE]
> 예제에서는 인증서가 이미 자격 증명 모음에 있는 것으로 가정합니다. 키 KeyVault 관리 인증서를 등록하고 갱신하려면 해당 문서의 앞부분에 설명된 필수 구성 요소 수동 단계가 필요합니다. 프로덕션 환경의 경우 KeyVault 관리 인증서를 사용합니다. Microsoft 내부 PKI에 특정된 샘플 스크립트는 아래에 포함되어 있습니다.

> [!NOTE]
> 인증서 오토롤오버는 CA에서 발급한 인증서에만 적합합니다. Azure Portal에서 Service Fabric 클러스터를 배포할 때 생성된 인증서를 포함하여 자체 서명된 인증서를 사용하는 것은 무의미하지만, 발급자 지문을 리프 인증서와 동일하게 선언하여 로컬/개발자 호스트 배포를 할 때 여전히 가능합니다.

### <a name="starting-point"></a>시작 지점
간단하게 하기 위해 다음 시작 상태를 가정합니다.
  - Service Fabric 클러스터가 있고, 지문으로 선언된 CA 발급 인증서로 보안이 유지됩니다.
  - 인증서는 자격 증명 모음에 저장되고 가상 머신 확장 집합 비밀로 프로비저닝됩니다.
  - 동일한 인증서를 사용하여 클러스터를 일반 이름 기반 인증서 선언으로 변환하고, 그에 따라 주체와 발급자의 유효성을 검사할 수 있습니다. 그렇지 않은 경우 [여기](service-fabric-cluster-security-update-certs-azure.md)에 설명된 대로 해당 목적을 위해 발급된 CA 발급 인증서를 획득하고 지문을 사용하여 클러스터 정의에 추가합니다.

다음은 해당 상태에 해당하는 템플릿으로부터 발췌한 json입니다. 참고로, 다음은 많은 필수 설정이 생략되었으며 인증서 관련 측면만 보여 주는 것입니다.
```json
  "resources": [
    {   ## VMSS definition
      "apiVersion": "[variables('vmssApiVersion')]",
      "type": "Microsoft.Compute/virtualMachineScaleSets",
      "name": "[variables('vmNodeTypeName')]",
      "location": "[variables('computeLocation')]",
      "properties": {
        "virtualMachineProfile": {
          "extensionProfile": {
            "extensions": [
            {
                "name": "[concat('ServiceFabricNodeVmExt','_vmNodeTypeName')]",
                "properties": {
                  "type": "ServiceFabricNode",
                  "autoUpgradeMinorVersion": true,
                  "publisher": "Microsoft.Azure.ServiceFabric",
                  "settings": {
                    "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                    "nodeTypeRef": "[variables('vmNodeTypeName')]",
                    "dataPath": "D:\\SvcFab",
                    "durabilityLevel": "Bronze",
                    "certificate": {
                        "thumbprint": "[parameters('primaryClusterCertificateTP')]",
                        "x509StoreName": "[parameters('certificateStoreValue')]"
                    }
                  },
                  "typeHandlerVersion": "1.1"
                }
            },}},
          "osProfile": {
            "adminPassword": "[parameters('adminPassword')]",
            "adminUsername": "[parameters('adminUsername')]",
            "secrets": [
            {
                "sourceVault": {
                    "id": "[resourceId('Microsoft.KeyVault/vaults', parameters('keyVaultName'))]"
                },
                "vaultCertificates": [
                {
                    "certificateStore": "[parameters('certificateStoreValue')]",
                    "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
                },
            ]}]
        },
    },
    {   ## cluster definition
        "apiVersion": "[variables('sfrpApiVersion')]",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "certificate": {
            "thumbprint": "[parameters('primaryClusterCertificateTP')]",
            "x509StoreName": "[parameters('certificateStoreValue')]"
        },
    }
  ]
```   

위의 예에 따르면, 기본적으로 지문이 있고```json [parameters('primaryClusterCertificateTP')] ``` KeyVault URI에 있는 인증서```json [parameters('clusterCertificateUrlValue')] ```가 지문을 통해 클러스터의 유일한 인증서로 선언됩니다. 다음으로 인증서의 오토롤오버를 확인하는 데 필요한 추가 리소스를 설정합니다.

### <a name="setting-up-prerequisite-resources"></a>필수 구성 요소 리소스 설정
Microsoft Compute 리소스 공급자 서비스는 앞서 설명한 것처럼 자사 ID를 사용하여 그리고 배포 운영자를 대신하여 가상 머신 확장 집합 비밀로 프로비저닝된 인증서를 자격 증명 모음에서 검색합니다. 오토롤오버는 변경됩니다. 가상 머신 확장 집합에 대해 할당되어 자격 증명 모음 비밀에 대한 사용 권한이 부여되는 관리 ID를 사용하는 것으로 전환됩니다.

모든 후속 인용은 부수적으로 배포되어야 합니다. 즉, 모든 후속 인용은 재생 후 분석 및 설명을 위해 개별적으로 나열됩니다.

먼저 사용자 할당 ID를 정의합니다(기본값은 예제로 포함됨). 최신 정보는 [공식 설명서](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity)를 참조하세요.
```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "userAssignedIdentityName": {
      "type": "string",
      "defaultValue": "sftstuaicus",
      "metadata": {
        "description": "User-assigned managed identity name"
      }
    },
  },
  "variables": {
      "vmssApiVersion": "2018-06-01",
      "sfrpApiVersion": "2018-02-01",
      "miApiVersion": "2018-11-30",
      "kvApiVersion": "2018-02-14",
      "userAssignedIdentityResourceId": "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('userAssignedIdentityName'))]"
  },    
  "resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('userAssignedIdentityName')]",
      "apiVersion": "[variables('miApiVersion')]",
      "location": "[resourceGroup().location]"
    },
  ]}
```

그런 다음 ID 액세스를 자격 증명 모음 비밀에 부여합니다. 현재 정보를 보려면 [공식 설명서](/rest/api/keyvault/vaults/updateaccesspolicy)를 참조하세요.
```json
  "resources":
  [{
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "name": "[concat(parameters('keyVaultName'), '/add')]",
      "apiVersion": "[variables('kvApiVersion')]",
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[reference(variables('userAssignedIdentityResourceId'), variables('miApiVersion')).tenantId]",
            "objectId": "[reference(variables('userAssignedIdentityResourceId'), variables('miApiVersion')).principalId]",
            "dependsOn": [
              "[variables('userAssignedIdentityResourceId')]"
            ],
            "permissions": {
              "secrets": [
                "get",
                "list"
              ]}}]}}]
```

다음 단계에서 다음을 수행할 것입니다.
  - 사용자 할당 ID를 가상 머신 확장 집합에 할당합니다.
  - 가상 머신 확장 집합이 관리 ID 생성 및 자격 증명 모음에 대한 액세스 권한 부여 결과에 종속성이 있음을 선언합니다.
  - 시작 시 관찰된 인증서를 검색하도록 요구하는 KeyVault VM 확장을 선언합니다([공식 설명서](../virtual-machines/extensions/key-vault-windows.md)).
  - KVVM 확장에 따라 Service Fabric VM 확장의 정의를 업데이트하고 클러스터 인증서를 일반 이름으로 변환합니다(동일한 리소스의 범위에 포함되기 때문에 단일 단계에서 해당 변경을 수행하려는 목적).

```json
  "parameters": {
    "kvvmextPollingInterval": {
      "type": "string",
      "defaultValue": "3600",
      "metadata": {
        "description": "kv vm extension polling interval in seconds"
      }
    },
    "kvvmextLocalStoreName": {
      "type": "string",
      "defaultValue": "MY",
      "metadata": {
        "description": "kv vm extension local store name"
      }
    },
    "kvvmextLocalStoreLocation": {
      "type": "string",
      "defaultValue": "LocalMachine",
      "metadata": {
        "description": "kv vm extension local store location"
      }
    },
    "kvvmextObservedCertificates": {
      "type": "array",
      "defaultValue": [
                "https://sftestcus.vault.azure.net/secrets/sftstcncluster",
                "https://sftestcus.vault.azure.net/secrets/sftstcnserver"
            ],
      "metadata": {
        "description": "kv vm extension observed certificates versionless uri"
      }
    },
    "certificateCommonName": {
      "type": "string",
      "defaultValue": "cus.cluster.sftstcn.system.servicefabric.azure-int",
      "metadata": {
        "description": "Certificate Common name"
      }
    },
  },
  "resources": [
  {
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[variables('vmNodeTypeName')]",
    "location": "[variables('computeLocation')]",
    "dependsOn": [
      "[variables('userAssignedIdentityResourceId')]",
      "[concat('Microsoft.KeyVault/vaults/', concat(parameters('keyVaultName'), '/accessPolicies/add'))]"
    ],
    "identity": {
      "type": "UserAssigned",
      "userAssignedIdentities": {
        "[variables('userAssignedIdentityResourceId')]": {}
      }
    },
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
        {
          "name": "KVVMExtension",
          "properties": {
            "publisher": "Microsoft.Azure.KeyVault",
            "type": "KeyVaultForWindows",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "secretsManagementSettings": {
                    "pollingIntervalInS": "[parameters('kvvmextPollingInterval')]",
                    "linkOnRenewal": false,
                    "observedCertificates": "[parameters('kvvmextObservedCertificates')]",
                    "requireInitialSync": true
                }
            }
          }
        },
        {
        "name": "[concat('ServiceFabricNodeVmExt','_vmNodeTypeName')]",
        "properties": {
          "type": "ServiceFabricNode",
          "provisionAfterExtensions" : [ "KVVMExtension" ],
          "publisher": "Microsoft.Azure.ServiceFabric",
          "settings": {
            "certificate": {
                "commonNames": [
                    "[parameters('certificateCommonName')]"
                ],
                "x509StoreName": "[parameters('certificateStoreValue')]"
            }
            },
            "typeHandlerVersion": "1.0"
          }
        },
  ] } ## extension profile
  },  ## VM profile
  "osProfile": {
    "adminPassword": "[parameters('adminPassword')]",
    "adminUsername": "[parameters('adminUsername')]",
  } 
  }
  ]
```
참고로, 위에 명시된 것은 아니지만, 가상 머신 확장 집합의 ‘OsProfile’ 섹션에서 자격 증명 모음 인증서 URL이 제거되었습니다.
마지막 단계는 인증서 선언을 지문에서 일반 이름으로 변경하도록 클러스터 정의를 업데이트하는 것입니다. 여기에는 발급자 지문이 고정되어 있습니다.

```json
  "parameters": {
    "certificateCommonName": {
      "type": "string",
      "defaultValue": "cus.cluster.sftstcn.system.servicefabric.azure-int",
      "metadata": {
        "description": "Certificate Common name"
      }
    },
    "certificateIssuerThumbprint": {
      "type": "string",
      "defaultValue": "1b45ec255e0668375043ed5fe78a09ff1655844d,d7fe717b5ff3593764f4d90654d86e8362ec26c8,3ac7c3cac8de0dd392c02789c8be97474f456960,96ea05926e2e42cc207e358668be2c316857fb5e",
      "metadata": {
        "description": "Certificate issuer thumbprints separated by comma"
      }
    },
  },
  "resources": [
    {
      "apiVersion": "[variables('sfrpApiVersion')]",
      "type": "Microsoft.ServiceFabric/clusters",
      "name": "[parameters('clusterName')]",
      "location": "[parameters('clusterLocation')]",
      "properties": {
        "certificateCommonNames": {
          "commonNames": [{
              "certificateCommonName": "[parameters('certificateCommonName')]",
              "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprint')]"
          }],
          "x509StoreName": "[parameters('certificateStoreValue')]"
        },
  ]
```

이 시점에서 위에서 언급한 업데이트를 단일 배포에 실행할 수 있습니다. 해당 부분에서 Service Fabric 리소스 공급자 서비스는 [클러스터 인증서를 지문에서 일반 이름으로 변환](cluster-security-certificates.md#converting-a-cluster-from-thumbprint--to-common-name-based-certificate-declarations)하는 세그먼트에 설명된 대로, 클러스터 업그레이드를 여러 단계로 분할합니다.

### <a name="analysis-and-observations"></a>분석 및 관찰
이 섹션은 위에서 자세히 설명하는 단계를 설명하고 중요한 측면에 주목하는 방법을 설명하기 위한 것입니다.

#### <a name="certificate-provisioning-explained"></a>인증서 프로비저닝 설명
프로비저닝 에이전트인 KVVM 확장은 미리 결정된 빈도에 따라 지속적으로 실행됩니다. 관찰된 인증서를 검색하지 못할 경우 다음 줄에서 계속 진행하고 다음 주기까지 최대 절전 모드로 전환합니다. 클러스터 부트스트랩 에이전트인 SFVM 확장에는 클러스터를 구성하기 전에 선언된 인증서가 필요합니다. 즉, SFVM 확장은 해당 ```json "provisionAfterExtensions" : [ "KVVMExtension" ]"```절 및 KeyVaultVM 확장의```json "requireInitialSync": true``` 설정에 따라 여기에 표시되는 클러스터 인증서를 성공적으로 검색한 후에만 실행될 수 있습니다. 이는 KVVM 확장에 첫 번째 실행(배포 후 또는 다시 부팅 후) 시에 모두 성공적으로 다운로드될 때까지 관찰된 인증서를 순환해야 함을 보여 줍니다. 해당 매개 변수를 false로 설정하면 클러스터 인증서를 검색하는 데 실패하고 클러스터 배포가 실패합니다. 반대로 관찰된 인증서의 부정확한/잘못된 목록과 초기 동기화를 요구하면 KVVM 확장 오류가 발생하여 클러스터를 배포하는 데 실패하게 됩니다.  

#### <a name="certificate-linking-explained"></a>인증서 연결 설명
KVVM 확장의 ‘linkOnRenewal’ 플래그를 발견하셨을 것입니다. 해당 플래그는 false로 설정되어 있습니다. 여기서는 이 플래그에 의해 제어되는 동작과 클러스터 기능에 미치는 영향을 자세히 설명합니다. 이 동작은 Windows로 한정됩니다.

[정의](../virtual-machines/extensions/key-vault-windows.md#extension-schema)에 따라 다음을 수행합니다.

```json
"linkOnRenewal": <Only Windows. This feature enables auto-rotation of SSL certificates, without necessitating a re-deployment or binding.  e.g.: false>,
```

TLS 연결을 설정하는 데 사용되는 인증서는 일반적으로 S-채널 보안 지원 공급자를 통해 [핸들로 획득](/windows/win32/api/sspi/nf-sspi-acquirecredentialshandlea)됩니다. 즉, 클라이언트는 인증서 자체의 개인 키에 직접 액세스하지 않습니다. S-채널은 인증서 확장([CERT_RENEWAL_PROP_ID](/windows/win32/api/wincrypt/nf-wincrypt-certsetcertificatecontextproperty#cert_renewal_prop_id)) 형식으로 자격 증명의 리디렉션(연결)을 지원합니다. 이 속성이 설정된 경우 해당 값은 ‘갱신’ 인증서의 지문을 나타내며 따라서 S-채널은 대신 연결된 인증서를 로드하려고 시도합니다. 실제로는 ‘최종’ 인증서(갱신 표시 없음)를 사용하여 종료될 때까지 링크된 (그리고 아마 비순환적인) 목록을 트래버스합니다. 해당 기능은 신중하게 사용할 경우 (예를 들어) 만료된 인증서로 인해 발생하는 가용성 손실을 크게 완화해줍니다. 다른 경우에는 진단 및 완화하기 어려운 중단의 원인일 수 있습니다. S 채널은 주체, 발급자 또는 클라이언트에서 결과 인증서의 유효성 검사에 참여하는 기타 특정 특성과 관계없이 갱신 속성의 인증서 트래버스를 실행합니다. 실제로 결과 인증서에 연결된 개인 키가 없거나 키가 해당 소비자에게 ACL되지 않았을 수 있습니다. 
 
연결을 사용하는 경우 KeyVault VM 확장은 자격 증명 모음에서 관찰된 인증서를 검색할 때 일치하는 기존 인증서를 찾아 갱신 확장 속성을 통해 연결합니다. 일치는 (독점적으로) SAN(주체 대체 이름)을 기반으로 하며 아래 예시와 같이 작동합니다.
다음과 같이 기존의 두 인증서를 가정합니다. A: CN = “Alice의 액세서리”, SAN = {"alice.universalexports.com"}, 갱신 = ‘ ’ B: CN = “Bob의 비트”, SAN = {"bob.universalexports.com", "bob.universalexports.net"}, 갱신 = ‘ ’
 
KVVM ext가 인증서 C를 검색한다고 가정합니다. CN = "Mallory 's malware", SAN = {"alice.universalexports.com", "bob.universalexports.com", "mallory.universalexports.com"}
 
A의 SAN 목록은 C에 완벽하게 포함되어 있으므로 A.갱신 = C.지문이며, B의 SAN 목록은 C와 공통된 교차를 갖지만 완전히 포함되지는 않으므로 B.갱신은 비어 있습니다.
 
이 상태에서 인증서 A에 대해 AcquireCredentialsHandle(S-채널)을 호출하려고 하면 실제로 원격 파티에 C를 전송하게 됩니다. Service Fabric의 경우 클러스터의 [전송 하위 시스템](service-fabric-architecture.md#transport-subsystem)은 상호 인증을 위해 S-채널을 사용하므로 위에 설명된 동작은 클러스터의 기본 통신에 직접 영향을 줍니다. 위의 예제를 계속 진행했을 때 클러스터 인증서가 A라고 가정하면 다음 작업은 다음에 따라 달라집니다.
  - Fabric이 실행되는 계정에 C의 개인 키가 ACL되지 않은 경우, 개인 키를 가져오는 동안 오류가 표시됩니다(SEC_E_UNKNOWN_CREDENTIALS 또는 유사한).
  - C의 개인 키에 액세스할 수 있는 경우 다른 노드에서 반환된 권한 부여 오류(CertificateNotMatched, 권한 없음 등)가 표시됩니다. 
 
두 경우 모두 전송에 실패하고 클러스터의 작동이 중단될 수 있습니다. 증상은 다양합니다. 이 문제를 방지하기 위해 갱신 순서에 따라 링크가 달라집니다. 이는 KVVM 확장의 관찰된 인증서 목록, 자격 증명 모음의 갱신 일정 또는 검색 순서를 변경하는 일시적인 오류 목록 순서에 따라 결정됩니다.

해당 인시던트를 완화하기 위해 다음을 수행하는 것이 좋습니다.
  - 여러 자격 증명 모음 인증서의 SAN을 혼용해서는 안 됩니다. 각 자격 증명 모음 인증서는 고유한 용도가 있으며, 주체와 SAN은 특이성과 함께 이를 반영해야 합니다.
  - SAN 목록에 주체 일반 이름을 포함합니다(문자 그대로 `CN=<subject common name>` ).  
  - 확실하지 않은 경우 KVVM 확장으로 프로비저닝된 인증서 갱신 시 링크 사용하지 않도록 설정하세요. 

#### <a name="why-use-a-user-assigned-managed-identity-what-are-the-implications-of-using-it"></a>사용자 할당 관리 ID를 사용하는 이유는 무엇인가요? 사용자 할당 관리 ID를 사용할 때의 결과는 어떻게 되나요?
위의 json 코드 조각에서와 같이, 작업/업데이트의 특정 시퀀싱은 전환 성공 여부를 보장하고 클러스터의 가용성을 유지하는 데 필요합니다. 특히 가상 머신 확장 집합 리소스는 해당 ID를 선언하고 이를 사용하여 단일(사용자 관점에서) 업데이트에서 비밀을 검색합니다. 클러스터를 부트스트랩하는 Service Fabric VM 확장은 KeyVault VM 확장이 완료되는지에 따라 달라지고, 이는 또한 관찰된 인증서의 성공적인 검색에 따라 달라집니다. KVVM 확장은 가상 머신 확장 집합 ID를 사용하여 자격 증명 모음에 액세스합니다. 즉, 가상 머신 확장 집합을 배포하기 전에 자격 증명 모음에 대한 액세스 정책이 이미 업데이트된 것입니다. 

관리 ID 생성을 삭제하거나 다른 리소스에 할당하려면 배포 운영자에게 템플릿에서 참조되는 다른 리소스를 관리하는 데 필요한 역할 외에도 구독 또는 리소스 그룹에 필요한 역할(ManagedIdentityOperator)이 있어야 합니다. 

보안 관점에서 볼 때, 가상 머신(확장 집합)은 Azure ID와 관련하여 보안 경계로 간주됩니다. 즉, 사용자는 VM에서 호스트되는 모든 응용 프로그램에서 VM 관리 ID 액세스 토큰을 나타내는 액세스 토큰을 가져올 수 있습니다. 해당 토큰은 인증되지 않은 IMDS 엔드포인트에서 가져옵니다. VM을 공유하거나 다중 테넌트 환경으로 간주하려는 경우 클러스터 인증서를 검색하는 방법이 표시되지 않을 수 있습니다. 하지만 이는 인증서 오토롤오버에 적합한 유일한 프로비저닝 메커니즘입니다.

## <a name="troubleshooting-and-frequently-asked-questions"></a>문제 해결 및 FAQ(자주 묻는 질문)

*Q*: KeyVault 관리 인증서를 프로그래밍 방식으로 등록하는 방법은 무엇인가요?
*A*: KeyVault 설명서에서 발급자의 이름을 찾은 다음 아래 스크립트에서 바꿉니다.  
```PowerShell
  $issuerName=<depends on your PKI of choice>
    $clusterVault="sftestcus"
    $clusterCertVaultName="sftstcncluster"
    $clusterCertCN="cus.cluster.sftstcn.system.servicefabric.azure-int"
    Set-AzKeyVaultCertificateIssuer -VaultName $clusterVault -Name $issuerName -IssuerProvider $issuerName
    $distinguishedName="CN=" + $clusterCertCN
    $policy = New-AzKeyVaultCertificatePolicy `
        -IssuerName $issuerName `
        -SubjectName $distinguishedName `
        -SecretContentType 'application/x-pkcs12' `
        -Ekus "1.3.6.1.5.5.7.3.1", "1.3.6.1.5.5.7.3.2" `
        -ValidityInMonths 4 `
        -KeyType 'RSA' `
        -RenewAtPercentageLifetime 75        
    Add-AzKeyVaultCertificate -VaultName $clusterVault -Name $clusterCertVaultName -CertificatePolicy $policy
    
    # poll the result of the enrollment
    Get-AzKeyVaultCertificateOperation -VaultName $clusterVault -Name $clusterCertVaultName 
```

*Q*: 선언되지 않은/지정되지 않은 발급자가 인증서를 발급하면 어떻게 되나요? 지정된 PKI에 대한 활성 발급자의 완전한 목록을 어디에서 얻을 수 있나요?
*A*: 인증서 선언이 발급자 지문을 지정하고 인증서의 직접 발급자가 고정 발급자 목록에 포함되지 않은 경우, 해당 루트를 클라이언트에서 신뢰하는지 여부에 관계없이 인증서가 잘못된 것으로 간주됩니다. 따라서 발급자 목록이 최신인지 확인하는 것이 중요합니다. 새 발급자를 도입하는 것은 드물게 발생하며, 인증서를 발급하기 전에 광범위하게 공개되어야 합니다. 

일반적으로 PKI는 IETF [RFC 7382](https://tools.ietf.org/html/rfc7382)에 따라 인증 사용 약관을 게시하고 유지관리합니다. 여기에는 다른 정보 중에 모든 활성 발급자가 포함됩니다. 해당 목록을 프로그래밍 방식으로 검색하는 것은 PKI와는 다를 수 있습니다.   

Microsoft 내부 PKI의 경우 공인 발급자를 검색하는 데 사용되는 엔드포인트/SDK에 대한 내부 설명서를 참조하세요. 이 목록을 주기적으로 검색하고 해당 클러스터 정의에 예상되는 *모든* 발급자가 포함되어 있는지 확인하는 것은 클러스터 owner의 책임입니다.

*Q*: 여러 PKI가 지원되나요? 
*A*: 예, 동일한 값을 사용하여 클러스터 매니페스트에서 여러 CN 항목을 선언할 수 없지만, 동일한 CN에 해당되는 여러 PKI의 발급자를 나열할 수는 있습니다. 이는 권장되는 방법이 아니며, 인증서 투명성 관행으로 인해 해당 인증서를 발급받지 못할 수도 있습니다. 그러나 한 PKI에서 다른 PKI로 마이그레이션하는 것처럼 이는 허용 가능한 메커니즘입니다.

*Q*: 현재 클러스터 인증서가 CA에서 발급되지 않았거나 의도된 주체가 없는 경우는 어떻게 되나요? 
*A*: 원하는 주체를 사용하여 인증서를 획득하고 지문을 사용하여 클러스터 정의에 부수적으로 추가합니다. 업그레이드가 성공적으로 완료되면 다른 클러스터 구성 업그레이드를 시작하여 인증서 선언을 일반 이름으로 변환합니다. 

[Image1]:./media/security-cluster-certificate-mgmt/certificate-journey-thumbprint.png
[Image2]:./media/security-cluster-certificate-mgmt/certificate-journey-common-name.png
