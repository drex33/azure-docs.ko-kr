---
title: AKS (Azure Kubernetes Service) 개요 (미리 보기)에 대 한 4apr 확장
description: AKS (Azure Kubernetes Service) 클러스터에서 d 4를 사용 하 여 응용 프로그램을 개발 하는 방법에 대해 자세히 알아보세요.
ms.author: nickoman
ms.service: container-service
ms.topic: article
ms.date: 10/15/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: fee44a5c346fd5c148cad599eb44680506d012e0
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133271953"
---
# <a name="dapr"></a>Dapr

Eapr (Distributed Application Runtime)는 마이크로 서비스 개발 및 구현을 간소화 하는 Api를 제공 합니다. 응용 프로그램과 함께 사이드카 프로세스로 실행 하는 경우에는 서비스 검색, 메시지 브로커 통합, 암호화, 관찰성 및 비밀 관리와 같이 분산 응용 프로그램을 빌드할 때 일반적으로 복잡 한 복잡 한 개발자가 복잡 하 게 발생 합니다. 응용 프로그램 간 통신이 direct service to service 또는 pub/sub messaging 인지 여부에 관계 없이, Aapr는 간단 하 고, 복원 력 및 보안 된 마이크로 서비스를 작성 하는 데 도움이 됩니다.

Adoptable는 증분 방식으로 수행 되며, API 구성 요소는 필요에 따라 사용할 수 있습니다. 하나, 여러 개 또는 모두를 사용 하 여 응용 프로그램을 더 빠르게 개발할 수 있습니다.

:::image type="content" source="./media/dapr-overview/dapr-building-blocks.png" alt-text="HTTP 또는 gRPC를 통해 다양 한 코드 프레임 워크의 다양 한 구성 요소를 사용 하 여 다양 한 코드 프레임 워크를 사용할 수 있는지 보여 주는 다이어그램입니다.":::

## <a name="capabilities-and-features"></a>기능 및 기능

AKS의 마이크로 서비스 개발에 도움이 되는 다음과 같은 기능 집합을 제공 합니다.

* [클러스터 확장][cluster-extensions]을 통해 AKS에서 d 4를 쉽게 프로 비전 할 수 있습니다.
* 기본 기술 선택을 추상화 하는 HTTP 및 gRPC Api를 통해 이식성 사용
* HTTP 및 gRPC Api를 통한 안정적이 고, 안전 하 고, 복원 력 있는 서비스 호출
* 메시지 배달에 대 한 CloudEvent 필터링 및 "최소 한 번" 의미 체계를 지원 하 여 쉽게 메시지를 게시 하 고 구독할 수 있습니다.
* 개방형 원격 분석 API 수집기를 통한 플러그형 관찰성 및 모니터링
* 언어와는 독립적으로 작동 하지만 언어별 Sdk도 제공 합니다.
* eapr 확장을 통해 VS Code와 통합
* [배포 응용 프로그램 문제 해결을 위한 추가 Api][dapr-blocks]

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="how-do-dapr-and-service-meshes-compare"></a>Eapr 및 서비스 메시는 어떻게 비교 되나요?

A: 서비스 메쉬가 네트워킹 서비스 메시로 정의 되는 경우에는 서비스 메쉬가 아닙니다. D 4 및 서비스 메시는 일부 겹치는 기능을 제공 하지만, 서비스 메시는 네트워킹 문제에 중점을 두고 있지만,이는 개발자가 쉽게 마이크로 서비스로 응용 프로그램을 빌드할 수 있도록 하는 구성 요소를 제공 하는 데 중점을 두었습니다. 6apr는 개발자 중심 이지만 서비스 메시는 인프라 중심입니다.  

Service 메시와 함께. d 4에서 공유 하는 몇 가지 일반적인 기능은 다음과 같습니다.

* MTLS 암호화를 사용 하 여 서비스 간 보안 통신
* 서비스 간 메트릭 수집
* 서비스 간 분산 추적
* 재시도를 통한 복원 력

또한, d p 4는 상태 관리, pub/sub messaging, 행위자 등에 대해 다른 응용 프로그램 수준 구성 요소를 제공 합니다. 그러나 라우팅 또는 트래픽 분할과 같은 트래픽 동작에 대 한 기능은 제공 하지 않습니다. 솔루션이 제공 하는 트래픽 분할의 이점을 활용 하는 경우 [개방형 서비스 메시][osm-docs]를 사용 하는 것이 좋습니다.  

Eapr 및 service 메시에 대 한 자세한 내용과 함께 사용할 수 있는 방법에 대 한 자세한 내용은 다음을 참조 [하세요.][dapr-docs]

### <a name="how-does-the-dapr-secrets-api-compare-to-the-secrets-store-csi-driver"></a>Eapr 비밀 API는 암호 저장소 CSI 드라이버와 어떻게 비교 되나요?

CSI 드라이버를 사용 하 여 외부 저장소에 저장 된 암호를 통합 하 고, 응용 프로그램 코드에서 비밀 저장소 기술을 추상화 합니다. 비밀 저장소 CSI 드라이버는 응용 프로그램에서 사용 하기 위해 Azure Key Vault에 보관 된 비밀을 CSI 볼륨으로 탑재 합니다. 6apr는 응용 프로그램 코드에서 호출할 수 있는 RESTful API를 통해 암호를 노출 하며, 다양 한 비밀 저장소를 사용 하 여 구성할 수 있습니다. 다음 표에서는 각 제품의 기능을 나열 합니다.

| | Eapr 비밀 API | 비밀 저장소 CSI 드라이버 |
| --- | --- | ---|
| **지원 되는 비밀 저장소** | 로컬 환경 변수 (개발용); 로컬 파일 (개발의 경우) Kubernetes 비밀 AWS 암호 관리자 비밀 저장소 Azure Key Vault Kubernetes에서 관리 Id를 사용 하 여 Azure Key Vault; GCP 암호 관리자 HashiCorp 자격 증명 모음 | 비밀 저장소 Azure Key Vault|
| **응용 프로그램 코드에서 비밀 액세스** | Eapr 비밀 API 호출 | 탑재 된 볼륨에 액세스 하거나 탑재 된 콘텐츠를 Kubernetes 암호로 동기화 하 고 환경 변수를 설정 합니다. |
| **비밀 회전** | 새 API 호출은 업데이트 된 암호를 가져옵니다. | 비밀을 폴링하고 구성 가능한 간격으로 탑재를 업데이트 합니다. |
| **로깅 및 메트릭** | 사이드카는 Azure Monitor와 같은 수집기로 구성할 수 있는 로그를 생성 하 고, 프로메테우스를 통해 메트릭을 내보내고, 상태 검사에 대 한 HTTP 끝점을 노출 합니다. | 프로메테우스를 통해 드라이버 및 Azure Key Vault 공급자 메트릭을 내보냅니다. |

Eapr의 암호 관리에 대 한 자세한 내용은 [암호 관리 빌드 블록 개요][dapr-secrets-block]를 참조 하세요.

비밀 저장소 CSI 드라이버 및 Azure Key Vault 공급자에 대 한 자세한 내용은 [암호 저장소 CSI 드라이버 개요][csi-secrets-store]를 참조 하세요.

### <a name="how-does-the-managed-dapr-cluster-extension-compare-to-the-open-source-dapr-offering"></a>관리 되는 Eapr 클러스터 확장은 오픈 소스-Apr 제공과 어떻게 비교 되나요?

관리 되는 Eapr 클러스터 확장은 AKS 클러스터에서 i 4를 프로 비전 하는 가장 쉬운 방법입니다. 확장을 사용 하 여 자동 업그레이드를 옵트인 하 여 Eapr 런타임 버전의 관리를 오프 로드할 수 있습니다. 또한 확장은 스마트 기본값을 사용 하 여 4Apr를 설치 합니다 (예: 고가용성 모드로 4 번째 제어 평면 프로 비전).

투구 또는 4Apr CLI를 통해 Eapr OSS를 설치할 때 런타임 버전 및 구성 옵션은 개발자와 클러스터 유지 관리자의 책임입니다.  

마지막으로, AKS 확장은 확장 프로그램 이므로 현재 미리 보기 상태인 다른 AKS 기능과 동일한 지원 정책을 사용할 수 있습니다.

### <a name="how-can-i-switch-to-using-the-dapr-extension-if-ive-already-installed-dapr-via-a-method-such-as-helm"></a>예를 들어 투구와 같은 메서드를 통해 d 4를 이미 설치한 경우에는 Eapr 확장을 사용 하도록 전환할 수 있습니다.

권장 지침은 AKS 클러스터에서 Eapr를 완전히 제거 하 고 클러스터 확장을 통해 다시 설치 하는 것입니다.  

AKS 확장을 통해 d 4를 설치 하는 경우에는 더 이상 확장을 사용 하 여 eapr CLI 대신 Eapr를 관리 하는 것이 좋습니다. 두 도구를 결합 하면 충돌이 발생 하 여 원치 않는 동작이 발생할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Aapr 및 해결 된 문제 중 일부를 학습 한 후에는 [응용 프로그램을 배포][dapr-quickstart]해 서

<!-- Links Internal -->
[csi-secrets-store]: ./csi-secrets-store-driver.md
[osm-docs]: ./open-service-mesh-about.md
[cluster-extensions]: ./cluster-extensions.md
[dapr-quickstart]: ./quickstart-dapr.md

<!-- Links External -->
[dapr-docs]: https://docs.dapr.io/
[dapr-blocks]: https://docs.dapr.io/concepts/building-blocks-concept/
[dapr-secrets-block]: https://docs.dapr.io/developing-applications/building-blocks/secrets/secrets-overview/