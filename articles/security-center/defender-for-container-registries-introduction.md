---
title: 컨테이너 레지스트리용 Azure Defender - 이점 및 특징
description: 컨테이너 레지스트리용 Azure Defender의 이점 및 특징에 대해 알아봅니다.
author: memildin
ms.author: memildin
ms.date: 09/05/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 5c9407e33cefc9c604192252a119db5a42851949
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123541408"
---
# <a name="introduction-to-azure-defender-for-container-registries"></a>컨테이너 레지스트리용 Azure Defender 소개

ACR(Azure Container Registry)은 중앙 레지스트리에서 Azure 배포에 대한 컨테이너 이미지를 저장하고 관리하는 관리형 프라이빗 Docker 레지스트리 서비스입니다. 오픈 소스 Docker 레지스트리 2.0을 기반으로 합니다.

구독의 Azure Resource Manager 기반 레지스트리를 보호하려면 구독 수준에서 **컨테이너 레지스트리용 Azure Defender** 를 사용하도록 설정합니다. 그러면 Azure Defender가 최근 30일 이내에 레지스트리에 푸시한 이미지, 레지스트리로 가져온 이미지 또는 풀한 이미지를 모두 검사합니다. 검사되는 모든 이미지에 대해 이미지당 한 번씩 요금이 청구됩니다.

## <a name="availability"></a>가용성

|양상|세부 정보|
|----|:----|
|릴리스 상태:|GA(일반 공급)|
|가격 책정:|**컨테이너 레지스터리용 Azure Defender** 는 [가격 책정 페이지](security-center-pricing.md)에 표시된 대로 요금이 청구됩니다.|
|지원되는 레지스트리 및 이미지:|셸 액세스를 사용하여 공용 인터넷에서 액세스할 수 있는 ACR 레지스트리의 Linux 이미지<br>[Azure Private Link로 보호되는 ACR 레지스트리](../container-registry/container-registry-private-link.md)|
|지원되지 않는 레지스트리 및 이미지:|Windows 이미지<br>'프라이빗' 레지스트리([신뢰할 수 있는 서비스](../container-registry/allow-access-trusted-services.md#trusted-services)에 대한 액세스 권한이 부여된 경우는 제외)<br>[Docker 스크래치](https://hub.docker.com/_/scratch/) 이미지와 같은 초 미니멀 이미지 또는 패키지 관리자, 셸 또는 OS 없이 애플리케이션과 그 런타임 종속성만 포함하는 "Distroless" 이미지<br>[OCI(Open Container Initiative) 이미지 형식 사양](https://github.com/opencontainers/image-spec/blob/master/spec.md)을 사용하는 이미지|
|필요한 역할 및 권한:|**보안 읽기 권한자** 및 [Azure Container Registry 역할 및 권한](../container-registry/container-registry-roles.md)|
|클라우드:|:::image type="icon" source="./media/icons/yes-icon.png" border="false"::: 상용 클라우드<br>:::image type="icon" source="./media/icons/yes-icon.png" border="false"::: 미국 정부 및 중국 정부 - 현재 푸시 기능의 검색만 지원됩니다. [이미지가 언제 검사되나요?](#when-are-images-scanned)에서 자세히 알아보기|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-container-registries"></a>컨테이너 레지스트리용 Azure Defender의 이점은?

Security Center는 구독의 Azure Resource Manager 기반 ACR 레지스트리를 식별하고 레지스트리 이미지에 대한 Azure 네이티브 취약성 평가 및 관리를 원활하게 제공합니다.

**컨테이너 레지스트리용 Azure Defender** 에는 Azure Resource Manager 기반 Azure Container Registry 레지스트리의 이미지를 검사하고 이미지의 취약성에 대한 보다 심층적인 가시성을 제공하는 취약성 검사기가 포함되어 있습니다. 통합 검사기는 업계 최고의 취약성 검사 공급업체인 Qualys의 제품입니다.

Qualys 또는 Security Center에서 문제가 발견되면 Security Center 대시보드에 알림이 표시됩니다. Security Center는 모든 취약성에 대해 심각도 분류 및 문제 해결 방법에 대한 지침과 실행 가능한 권장 사항을 제공합니다. Security Center의 컨테이너 관련 권장 사항에 대한 자세한 내용은 [권장 사항 참조 목록](recommendations-reference.md#recs-container)을 참조하세요.

Security Center는 검사기의 검사 결과를 필터링하고 분류합니다. 이미지가 정상이면 Security Center는 이미지를 정상으로 표시합니다. Security Center는 해결해야 할 문제가 있는 이미지에 대해서만 보안 권장 사항을 생성합니다. Security Center는 보고된 각 취약성 및 심각도 분류의 세부 정보를 제공합니다. 또한 각 이미지에 있는 특정 취약성을 해결하는 방법에 대한 지침을 제공합니다.

Security Center는 문제가 있을 경우에만 알리는 방식으로 원치 않는 정보 알림이 발생할 가능성을 줄입니다.


> [!TIP]
> Security Center의 컨테이너 보안 기능에 대해 자세히 알아보려면 다음을 참조하세요.
>
> - [Azure Security Center 및 컨테이너 보안](container-security.md)
> - [Azure Defender for Kubernetes 소개](defender-for-kubernetes-introduction.md)

## <a name="when-are-images-scanned"></a>이미지가 언제 검사되나요?

이미지 검사에 대한 트리거는 세 가지가 있습니다.

- **푸시할 때** - 이미지를 레지스트리에 푸시할 때마다 Security Center에서 해당 이미지를 자동으로 검사합니다. 이미지 검사를 트리거하려면 리포지토리에 푸시하세요.

- **최근에 끌어온 경우** - 새로운 취약성은 매일 검색되므로 **컨테이너 레지스트리용 Azure Defender** 는 최근 30일 이내에 가져온 이미지를 매주 검색합니다. 위에서 설명한 것처럼 이미지마다 한 번씩 요금이 청구되므로 이러한 재검사에 대한 추가 비용은 없습니다.

- **가져올 때** - Azure Container Registry에는 Docker Hub, Microsoft Container Registry 또는 다른 Azure Container Registry의 이미지를 사용자 레지스트리로 가져오는 가져오기 도구가 있습니다. **컨테이너 레지스트리용 Azure Defender** 는 사용자가 가져온 모든 지원 이미지를 검사합니다. [컨테이너 이미지를 컨테이너 레지스트리로 가져오기](../container-registry/container-registry-import-images.md)에서 자세히 알아보세요.
 
검사는 일반적으로 2분 이내에 완료되지만 최대 15분이 걸릴 수 있습니다. 검사 결과는 다음과 같은 Security Center 권장 사항으로 제공됩니다.

[![ACR(Azure Container Registry) 호스트 이미지에서 발견된 취약성에 대한 샘플 Azure Security Center 권장 사항.](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)


## <a name="how-does-security-center-work-with-azure-container-registry"></a>Azure Container Registry에서 Security Center가 작동하는 방법

다음은 Security Center를 사용하여 레지스트리를 보호하는 경우의 구성 요소 및 이점에 대한 개략적인 다이어그램입니다.

![Azure Security Center 및 ACR(Azure Container Registry)의 대략적인 개요.](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)




## <a name="faq---azure-container-registry-image-scanning"></a>FAQ - Azure Container Registry 이미지 검사

### <a name="how-does-security-center-scan-an-image"></a>Security Center는 이미지를 어떻게 검사하나요?
Security Center는 레지스트리에서 이미지를 가져와 Qualys 스캐너를 사용하여 격리된 샌드박스에서 실행합니다. 스캐너는 알려진 취약점 목록을 추출합니다.

Security Center는 검사기의 검사 결과를 필터링하고 분류합니다. 이미지가 정상이면 Security Center는 이미지를 정상으로 표시합니다. Security Center는 해결해야 할 문제가 있는 이미지에 대해서만 보안 권장 사항을 생성합니다. Security Center는 문제가 있는 경우에만 알리는 방식으로 원치 않는 정보 알림이 발생할 가능성을 줄입니다.

### <a name="can-i-get-the-scan-results-via-rest-api"></a>REST API를 통해 검사 결과를 가져올 수 있나요?
예. 결과는 [Sub-Assessments Rest API](/rest/api/securitycenter/subassessments/list/)에 있습니다. 또한 모든 리소스에 대한 Kusto 같은 API인 ARG(Azure Resource Graph)를 사용할 수 있습니다. 쿼리로 특정 검사를 페치할 수 있습니다.

### <a name="what-registry-types-are-scanned-what-types-are-billed"></a>어떤 레지스트리 유형이 검사되나요? 어떤 유형에 요금이 청구되나요?
컨테이너 레지스트리용 Azure Defender에서 지원하는 컨테이너 레지스트리 유형의 목록은 [가용성](#availability)을 참조하세요.

지원되지 않는 레지스트리를 Azure 구독에 연결하는 경우 Azure Defender는 해당 레지스트리를 검색하지 않고 요금을 청구하지 않습니다.

### <a name="can-i-customize-the-findings-from-the-vulnerability-scanner"></a>취약성 검사기의 결과를 사용자 지정할 수 있나요?
예. 조직에서 결과를 수정하지 않고 무시해야 하는 요구 사항이 있으면 필요에 따라 이 결과를 사용하지 않도록 설정할 수 있습니다. 사용하지 않도록 설정된 결과는 보안 점수에 영향을 주거나 원치 않는 노이즈를 생성하지 않습니다.

[통합된 취약성 평가 도구의 결과를 사용하지 않는 규칙을 만드는 방법에 대해 알아봅니다](defender-for-container-registries-usage.md#disable-specific-findings-preview).

### <a name="why-is-security-center-alerting-me-to-vulnerabilities-about-an-image-that-isnt-in-my-registry"></a>Security Center에서 레지스트리에 없는 이미지에 대한 취약성을 경고하는 이유는 무엇인가요?
Security Center는 레지스트리에서 푸시하거나 끌어온 모든 이미지에 대한 취약성 평가를 제공합니다. 일부 이미지는 이미 스캔된 이미지에서 태그를 재사용할 수 있습니다. 예를 들어 다이제스트에 이미지를 추가할 때마다 “최신” 태그를 다시 할당할 수 있습니다. 이러한 경우 ‘이전’ 이미지는 여전히 레지스트리에 존재하며 해당 다이제스트에 의해 계속 끌어올 수 있습니다. 이미지에 보안 결과가 있고 끌어온 경우 보안 취약성이 노출됩니다.


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [이미지 취약성 검색](defender-for-container-registries-usage.md)
