---
title: 쿼리 성능 문제 해결
description: 레지스트리 성능의 일반 문제에 대한 증상, 원인 및 해결 방법
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 4a93df09c8ceb587815227c073ec69dcd1205451
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528108"
---
# <a name="troubleshoot-registry-performance"></a>쿼리 성능 문제 해결

이 문서는 Azure Container Registry의 성능에 발생할 수 있는 문제를 해결하는 데 도움이 됩니다. 

## <a name="symptoms"></a>증상

다음 중 하나 이상을 포함할 수 있습니다.

* Docker CLI를 사용하는 풀 또는 푸시 이미지가 예상보다 오래 걸립니다.
* Azure Kubernetes Service와 같은 서비스에 이미지를 배포하는 데 예상보다 오래 걸립니다.
* 예상 시간 내에 많은 수의 동시 풀 또는 푸시 작업을 완료할 수 없습니다.
* `Too many requests`와 유사한 HTTP 429 오류가 표시됩니다.
* 지역에서 복제된 레지스트리에서 풀 또는 푸시 작업이 예상보다 오래 걸리거나 `Error writing blob` 또는 `Error writing manifest` 오류로 인해 푸시가 실패합니다.

## <a name="causes"></a>원인

* 네트워크 연결 속도로 인해 레지스트리 작업이 느려질 수 있음 - [솔루션](#check-expected-network-speed)
* 클라이언트에서 이미지 레이어 압축 또는 추출 속도가 느릴 수 있음 - [솔루션](#check-client-hardware)  
* 레지스트리 서비스 계층 또는 환경에서 구성된 제한에 도달함 - [솔루션](#review-configured-limits)
* 지역 복제 레지스트리에 인근 지역에 복제본이 있음 - [솔루션](#configure-geo-replicated-registry)
* 지리적으로 멀리 떨어진 레지스트리 복제본에서 풀함 - [솔루션](#configure-dns-for-geo-replicated-registry)

여기서 문제를 해결하지 못한 경우 [고급 문제 해결](#advanced-troubleshooting) 및 [다음 단계](#next-steps)에서 다른 옵션을 참조하세요.

## <a name="potential-solutions"></a>잠재적 해결 방법

### <a name="check-expected-network-speed"></a>예상 네트워크 속도 확인

인터넷 업로드 및 다운로드 속도를 확인하거나 AzureSpeed와 같은 도구를 사용하여 레지스트리 이미지 계층을 호스팅하는 Azure Blob Storage에서 [업로드](https://www.azurespeed.com/Azure/Uploadß) 및 [다운로드](https://www.azurespeed.com/Azure/Download)를 테스트합니다.

사용자의 지원되는 최대 크기와 레지스트리 서비스 계층에 대해 지원되는 다운로드 또는 업로드 대역폭에 대해 이미지 크기를 확인합니다. 레지스트리가 기본 또는 표준 계층에 있는 경우 업그레이드를 고려하여 성능을 향상시킵니다. 

다른 서비스에 대한 이미지 배포의 경우 레지스트리와 대상이 있는 지역을 확인합니다. 성능을 향상시키려면 동일한 네트워크 또는 네트워크에 가까운 지역에서 레지스트리와 배포 대상을 찾는 것이 좋습니다.

관련 링크:

* [Azure Container Registry 서비스 계층](container-registry-skus.md)    
* [컨테이너 레지스트리 FAQ](container-registry-faq.yml)
* [Azure Blob Storage의 성능 및 확장성 목표](../storage/blobs/scalability-targets.md)

### <a name="check-client-hardware"></a>클라이언트 하드웨어 확인

Docker 클라이언트의 디스크 유형 및 CPU는 풀 또는 푸시 작업의 일부로 클라이언트에서 이미지 계층을 추출하거나 압축하는 속도에 영향을 줄 수 있습니다. 예를 들어 하드 디스크 드라이브의 계층 추출은 반도체 디스크보다 오래 걸립니다. Azure Container Registry 및 공용 레지스트리(예: Docker Hub)에서 비교 가능한 이미지에 대한 풀 작업을 비교합니다.

### <a name="review-configured-limits"></a>구성된 제한 검토

여러 개 또는 여러 다중 계층 이미지를 레지스트리에 동시에 푸시하거나 풀하려는 경우 레지스트리 서비스 계층에 대해 지원되는 ReadOps 및 WriteOps 제한을 검토합니다. 레지스트리가 기본 또는 표준 계층에 있는 경우 한도를 늘리려면 업그레이드하는 것이 좋습니다. 동시 작업에서 많이 발생할 수 있는 네트워크 제한에 대해서도 네트워킹 공급자에게 확인합니다. 

클라이언트의 각 푸시 또는 풀 작업에 대한 최대 동시 업로드 또는 다운로드에 대한 Docker 디먼 구성을 검토합니다. 필요한 경우 더 높은 한도를 구성합니다.

각 이미지 계층에는 별도의 레지스트리 읽기 또는 쓰기 작업이 필요하므로 이미지의 레이어 수를 확인합니다. 이미지 계층 수를 줄이기 위한 전략을 고려합니다.

관련 링크:

* [Azure Container Registry 서비스 계층](container-registry-skus.md)
* [dockerd](https://docs.docker.com/engine/reference/commandline/dockerd/)

### <a name="configure-geo-replicated-registry"></a>지역에서 복제된 레지스트리 구성

이미지를 지리적으로 복제된 레지스트리로 푸시하는 Docker 클라이언트는 모든 이미지 계층과 해당 매니페스트를 단일 복제된 지역으로 푸시하지 않을 수 있습니다. 이 상황은 Azure Traffic Manager가 레지스트리 요청을 네트워크에 가장 가까운 복제된 레지스트리로 라우팅하기 때문에 발생할 수 있습니다. 레지스트리에 두 개의 인접 복제 지역이 있는 경우 이미지 레이어와 매니페스트를 두 사이트에 배포할 수 있으며 매니페스트의 유효성을 검사할 때 푸시 작업이 실패합니다.

이미지를 푸시할 때 가장 가까운 복제본에 대한 DNS 확인을 최적화하려면 Azure 외부에서 작업할 때 푸시 작업의 원본과 동일한 Azure 지역 또는 가장 가까운 지역의 지역 복제 레지스트리를 구성합니다.

지역에서 복제된 레지스트리를 사용하여 작업 문제를 해결하기 위해 하나 이상의 복제에 대한 Traffic Manager 라우팅을 일시적으로 사용하지 않도록 설정할 수도 있습니다.

관련 링크:

* [Azure Container Registry의 지리적 복제](container-registry-geo-replication.md)

### <a name="configure-dns-for-geo-replicated-registry"></a>지역에서 복제된 레지스트리에 대한 DNS 구성

지역 복제 레지스트리에서 풀 작업이 느리게 표시되면 클라이언트의 DNS 구성이 지리적으로 멀리 떨어진 DNS 서버로 확인될 수 있습니다. 이 경우 Traffic Manager가 DNS 서버에 네트워크로 연결되어 있지만 클라이언트에서 멀리 떨어진 복제본에 요청을 라우팅하는 것일 수 있습니다. `nslookup` 또는 `dig`(Linux에서)와 같은 도구를 실행하여 Traffic Manager가 레지스트리 요청을 라우팅하는 복제본을 판별합니다. 예를 들면 다음과 같습니다.

```console
nslookup myregistry.azurecr.io
```

잠재적 솔루션은 더 가까운 DNS 서버를 구성하는 것입니다.

관련 링크:

* [Azure Container Registry의 지리적 복제](container-registry-geo-replication.md)
* [지역에서 복제된 레지스트리를 사용하여 푸시 작업 문제 해결](container-registry-geo-replication.md#troubleshoot-push-operations-with-geo-replicated-registries)
* [일시적으로 복제에 대한 라우팅 사용을 비활성화](container-registry-geo-replication.md#temporarily-disable-routing-to-replication)
* [Traffic Manager FAQ](../traffic-manager/traffic-manager-faqs.md)

### <a name="advanced-troubleshooting"></a>고급 문제 해결

레지스트리 리소스에 대한 권한이 허용하는 경우 [레지스트리 환경의 상태를 확인](container-registry-check-health.md)합니다. 오류가 보고되면 [오류 참조](container-registry-health-error-reference.md)에서 잠재적 솔루션을 검토합니다.

레지스트리에서 [리소스 로그 수집](monitor-service.md)이 사용하도록 설정된 경우 ContainterRegistryRepositoryEvents 로그를 검토합니다. 이 로그는 푸시 또는 풀 이벤트와 같은 작업에 대한 정보를 저장합니다. 로그에서 [리포지토리 수준 작업 실패](monitor-service.md#repository-level-operation-failures)를 쿼리합니다. 

관련 링크:

* [진단 평가 및 감사를 위한 로그](./monitor-service.md)
* [컨테이너 레지스트리 FAQ](container-registry-faq.yml)
* [Azure Container Registry의 모범 사례](container-registry-best-practices.md)

## <a name="next-steps"></a>다음 단계

여기에서 문제를 해결하지 않으면 다음 옵션을 참조하세요.

* 기타 레지스트리 문제 해결 항목은 다음과 같습니다.
  * [레지스트리 로그인 문제 해결](container-registry-troubleshoot-login.md)
  * [레지스트리 관련 네트워크 문제 해결](container-registry-troubleshoot-access.md)
* [커뮤니티 지원](https://azure.microsoft.com/support/community/) 옵션
* [Microsoft Q&A](/answers/products/)
* [지원 티켓 열기](https://azure.microsoft.com/support/create-ticket/)