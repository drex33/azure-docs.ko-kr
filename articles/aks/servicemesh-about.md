---
title: 서비스 메시 정보
description: 서비스 메시, 지원되는 시나리오, 선택 조건 및 살펴볼 다음 단계에 대한 개요를 가져옵니다.
author: pgibson
ms.topic: article
ms.date: 07/29/2021
ms.author: pgibson
ms.openlocfilehash: b77ee6fb25f45b365003850627276c0ecd47c7b5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528941"
---
# <a name="about-service-meshes"></a>서비스 메시 정보

서비스 메시는 트래픽 관리, 복원력, 정책, 보안, 강력한 ID 및 관찰성과 같은 기능을 워크로드에 제공합니다. 애플리케이션은 이러한 운영 기능에서 분리되고, 서비스 메시는 애플리케이션 계층의 외부로 이동하고 인프라 계층으로 하향합니다.

## <a name="scenarios"></a>시나리오

서비스 메시를 사용할 때 워크로드에 대해 사용할 수 있는 몇 가지 시나리오는 다음과 같습니다.

- **클러스터의 모든 트래픽 암호화** -클러스터의 지정된 서비스 간에 상호 TLS를 사용하도록 설정합니다. 네트워크 경계에서 수신 및 송신으로 확장될 수 있습니다. 애플리케이션 코드 및 인프라에 필요한 변경 없이 기본적으로 보안 옵션을 제공합니다.

- **카나리아 및 단계적 롤아웃** -클러스터의 새 서비스 집합으로 라우팅될 트래픽 하위 집합에 대한 조건을 지정합니다. 카나리아 릴리스를 성공적으로 테스트하는 경우, 조건부 라우팅 및 단계를 제거하여 새 서비스에 대한 모든 트래픽의 퍼센트(%)를 점차적으로 늘립니다. 결국 모든 트래픽이 새 서비스로 전송됩니다.

- **트래픽 관리 및 조작** -특정 원본에서 서비스 버전으로의 모든 트래픽을 제한하는 서비스에 대한 정책을 만듭니다. 또는 지정된 서비스 간의 실패 클래스에 다시 시도 전략을 적용하는 정책입니다. 마이그레이션하는 혹은 문제를 디버그하는 동안 새 버전의 서비스로 라이브 트래픽을 미러링합니다. 테스트 환경의 서비스 사이에 오류를 삽입하여 복원력을 테스트합니다.

- **관찰성** -서비스가 그 사이 흘러가는 트래픽에 서로 연결되는 방법에 대한 통찰력을 얻습니다. 클러스터의 모든 트래픽과 수신/송신에 대한 메트릭, 로그 및 추적을 얻습니다. 애플리케이션에 분산 추적 기능을 추가합니다.

## <a name="selection-criteria"></a>선택 조건

서비스 메시를 선택하기 전에 요구 사항 및 서비스 메시를 설치하는 이유를 이해해야 합니다. 다음과 같은 질문을 해 보세요.

- **수신 컨트롤러는 사용자의 요구에 충분한가요?** -때때로 a/b 테스트 또는 트래픽 분할과 같은 기능이 요구되는 시나리오를 지원하기에 충분한 경우도 있습니다. 무턱대고 사용자 환경에 복잡성을 추가하지 마세요.

- **워크로드 및 환경에서 추가 오버헤드를 허용할 수 있나요?** -서비스 메시를 지원하는 데 필요한 모든 추가 구성 요소에는 cpu 및 메모리와 같은 추가 리소스가 필요합니다. 또한 모든 프록시 및 그와 관련된 정책 검사는 트래픽에 대기 시간을 추가합니다. 대기 시간이 매우 중요하거나 서비스 메시 구성 요소를 포함하는 추가 리소스를 제공할 수 없는 워크로드가 있는 경우, 다시 고려해야 합니다.

- **이러한 추가 복잡성을 불필요하게 더하고 있습니까?** -서비스 메시를 설치하는 이유가 비즈니스 또는 운영 팀에 중요하지 않을 수 있는 기능을 확보하는 것이라면, 그 다음 설치, 유지 관리 및 구성에 대한 추가 복잡성이 무엇인지 고려합니다.

- **이는 증분 방식으로 채택될 수 있나요?** - 많은 기능을 제공하는 일부 서비스 메시는 더 큰 증분 방식으로 채택될 수 있습니다. 성공 여부를 확인하는 데 필요한 구성 요소만 설치합니다. 더 확신이 들고 추가적인 기능이 필요한 경우 해당 기능을 탐색합니다. 처음부터 *모든 항목* 을 설치하는 것은 시급하지 않습니다.

## <a name="next-steps"></a>다음 단계

다음 단계로, AKS(Azure Kubernetes Service)에서 OSM(Open Service Mesh)을 살펴보세요.

> [!div class="nextstepaction"]
> [OSM에 대해 자세히 알아보기 ...][osm-about]

각 항목에 대해 사용할 수 있는 포괄적인 프로젝트 설명서를 통해 AKS(Azure Kubernetes Service)에서 다음과 같은 서비스 메시를 탐색할 수도 있습니다.

- [Istio][istio]
- [linkerd][linkerd]
- [Consul 연결][consul]

서비스 메시 가로, 사용 가능한 다양한 서비스 메시, 도구 및 규정 준수 세트에 대해 자세히 알아보려면 다음을 탐색하세요.

- [레이어 5의 서비스 메시 가로][service-mesh-landscape]

다양한 서비스 메시 표준화 작업을 탐색할 수도 있습니다.

- [서비스 메시 인터페이스 (SMI)][smi]
- [서비스 메시 페더레이션][smf]
- [SMP(서비스 메시 성능)][smp]


<!-- LINKS - external -->
[istio]: https://istio.io/latest/docs/setup/install/
[linkerd]: https://linkerd.io/getting-started/
[consul]: https://learn.hashicorp.com/tutorials/consul/service-mesh-deploy
[service-mesh-landscape]: https://layer5.io/service-mesh-landscape
[smi]: https://smi-spec.io/
[smf]: https://github.com/vmware/hamlet
[smp]: https://github.com/service-mesh-performance/service-mesh-performance

<!-- LINKS - internal -->
[osm-about]: ./servicemesh-osm-about.md
