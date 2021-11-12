---
title: 컨테이너 인스턴스에 대한 보안 고려 사항
description: Azure Container Instances의 보안 이미지 및 비밀에 대한 권장 사항 및 모든 컨테이너 플랫폼에 대한 일반 보안 고려 사항
ms.topic: article
ms.date: 01/10/2020
ms.custom: ''
ms.openlocfilehash: 9f0a30095605ae9ff52f5780bdf2f94454cf85a8
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132315732"
---
# <a name="security-considerations-for-azure-container-instances"></a>Azure 컨테이너 인스턴스에 대한 보안 고려 사항

이 문서에서는 Azure Container Instances를 사용하여 컨테이너 앱을 실행할 때의 보안 고려 사항을 소개합니다. 다룰 주제는 다음과 같습니다.

> [!div class="checklist"]
> * Azure Container Instances에 대한 이미지 및 비밀 관리에 대한 **보안 권장 사항**
> * 모든 컨테이너 플랫폼의 컨테이너 수명 주기 동안 **컨테이너 에코시스템에 대한 고려 사항**

배포의 보안 상태를 개선하는 데 도움이 되는 포괄적인 권장 사항은 [Container Instances에 대한 Azure 보안 기준](security-baseline.md)을 참조하세요.


## <a name="security-recommendations-for-azure-container-instances"></a>Azure Container Instances에 대한 보안 권장 사항

### <a name="use-a-private-registry"></a>프라이빗 레지스트리 사용

컨테이너는 하나 이상의 리포지토리에 저장된 이미지에서 작성됩니다. 이러한 리포지토리는 공용 레지스트리(예: [Docker Hub](https://hub.docker.com)) 또는 프라이빗 레지스트리에 속할 수 있습니다. 프라이빗 레지스트리의 예는 [Docker Trusted Registry](https://docs.docker.com/datacenter/dtr/)이며 온-프레미스 또는 가상 프라이빗 클라우드에 설치할 수 있습니다. [Azure Container Registry](../container-registry/container-registry-intro.md)를 포함한 클라우드 기반 프라이빗 컨테이너 레지스트리 서비스를 사용할 수도 있습니다. 

공개적으로 사용 가능한 컨테이너 이미지는 보안이 보장되지 않습니다. 컨테이너 이미지는 여러 소프트웨어 계층으로 구성되며 각 소프트웨어 계층은 취약점이 있을 수 있습니다. 공격의 위협을 줄이려면 Azure Container Registry 또는 Docker Trusted Registry와 같은 프라이빗 레지스트리에서 이미지를 저장하고 검색해야 합니다. 관리되는 프라이빗 레지스트리를 제공하는 것 외에도, Azure Container Registry는 기본 인증 흐름에 대해 Azure Active Directory를 통한 [서비스 주체 기반 인증](../container-registry/container-registry-authentication.md)을 지원합니다. 이 인증에는 읽기 전용(풀), 쓰기(푸시) 및 기타 권한에 대한 역할 기반 액세스가 포함됩니다.

### <a name="monitor-and-scan-container-images"></a>컨테이너 이미지 모니터링 및 스캔

솔루션을 활용하여 프라이빗 레지스트리에서 컨테이너 이미지를 스캔하고 잠재적인 취약성을 식별합니다. 다양한 솔루션이 제공하는 위협 탐지의 깊이를 이해하는 것이 중요합니다.

예를 들어 Azure Container Registry 필요에 따라 [Microsoft Defender for Cloud와 통합되어 레지스트리에](../security-center/defender-for-container-registries-introduction.md) 푸시된 모든 Linux 이미지를 자동으로 검색합니다. Microsoft Defender for Cloud의 통합 Qualys 스캐너는 이미지 취약성을 감지하고 분류하며 수정 지침을 제공합니다.

[Twistlock](https://azuremarketplace.microsoft.com/marketplace/apps/twistlock.twistlock?tab=Overview) 및 [Aqua Security](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview)와 같은 보안 모니터링 및 이미지 스캔 솔루션도 Azure Marketplace를 통해 사용할 수 있습니다.  

### <a name="protect-credentials"></a>자격 증명 보호

컨테이너는 여러 클러스터 및 Azure 지역에 걸쳐 분산될 수 있습니다. 따라서 암호나 토큰 등의 로그인 또는 API 액세스에 필요한 자격 증명을 보호해야 합니다. 권한 있는 사용자만 전송 중 및 휴지 상태의 컨테이너에 액세스할 수 있도록 합니다. 모든 자격 증명 비밀의 인벤토리를 수집한 다음, 개발자가 컨테이너 플랫폼용으로 설계된 새로운 비밀 관리 도구를 사용해야 합니다.  솔루션에 암호화된 데이터베이스, 전송 중인 비밀 데이터에 대한 TLS 암호화 및 최소 권한 [Azure RBAC(Azure Role-based Access Control)](../role-based-access-control/overview.md)가 포함되어 있는지 확인합니다. [Azure Key Vault](../key-vault/general/security-features.md)는 컨테이너화된 애플리케이션에 대한 암호화 키와 비밀(예: 인증서, 연결 문자열 및 암호)을 보호하는 클라우드 서비스입니다. 이 데이터는 민감하고 업무상 중요하기 때문에 권한이 부여된 애플리케이션과 사용자만 액세스할 수 있도록 하여 키 자격 증명 모음에 대한 액세스를 보호합니다.

## <a name="considerations-for-the-container-ecosystem"></a>컨테이너 에코시스템에 대한 고려 사항

잘 구현되고 효과적으로 관리되는 다음 보안 조치는 컨테이너 에코시스템을 보호하는 데 도움이 될 수 있습니다. 이러한 조치는 개발부터 프로덕션 배포에 이르기까지 컨테이너 수명 주기 전반에 걸쳐, 그리고 다양한 컨테이너 오케스트레이터, 호스트 및 플랫폼에 적용됩니다. 

### <a name="use-vulnerability-management-as-part-of-your-container-development-lifecycle"></a>컨테이너 개발 수명 주기의 일부로 취약성 관리 사용 

컨테이너 개발 수명 주기 전반에 걸쳐 효과적인 취약성 관리를 사용하면 보안 문제가 더 심각해지기 전에 식별하고 해결할 확률을 높일 수 있습니다. 

### <a name="scan-for-vulnerabilities"></a>취약성 검사 

새로운 취약성은 항상 발견되므로 취약성을 검사하고 식별하는 것은 지속적인 프로세스입니다. 컨테이너 수명 주기 전체에서 취약성 검사를 통합합니다.

* 개발 파이프라인의 최종 체크 인으로 이미지를 공개 또는 비공개 레지스트리로 푸시하기 전에 컨테이너에서 취약성 스캔을 수행해야 합니다. 
* 레지스트리에서 컨테이너 이미지를 계속 스캔하여 개발 중에 누락된 결함을 식별하고 컨테이너 이미지에 사용된 코드에 존재할 수 있는 새로 발견된 취약점을 해결합니다.  

### <a name="map-image-vulnerabilities-to-running-containers"></a>실행 중인 컨테이너에 이미지 취약점 매핑 

보안 문제를 완화하거나 해결할 수 있도록 컨테이너 이미지에서 식별된 취약성을 실행 중인 컨테이너에 매핑하는 수단이 있어야 합니다.  

### <a name="ensure-that-only-approved-images-are-used-in-your-environment"></a>승인된 이미지만 환경에서 사용되는지 확인합니다. 

컨테이너 에코시스템에는 알 수 없는 컨테이너도 허용하지 않으면서 충분한 변화와 변동성이 있습니다. 승인된 컨테이너 이미지만 허용하세요. 승인되지 않은 컨테이너 이미지의 사용을 모니터링하고 방지하기 위한 도구와 프로세스를 갖추세요. 

공격 노출면을 줄이고 개발자가 심각한 보안 실수를 범하지 않도록 하는 효과적인 방법은 개발 환경으로 들어오는 컨테이너 이미지의 흐름을 제어하는 것입니다. 예를 들어 잠재적인 공격에 대한 노출면을 최소화하기 위해 단일 Linux 배포를 가급적 용량이 적은 베이스 이미지(Ubuntu가 아닌 Alpine 또는 CoreOS)로 승인할 수 있습니다. 

이미지 서명 또는 지문은 컨테이너의 무결성을 확인할 수 있는 보호 체인을 제공할 수 있습니다. 예를 들어 Azure Container Registry는 Docker의 [콘텐츠 신뢰](https://docs.docker.com/engine/security/trust/content_trust) 모델을 지원하므로 이미지 게시자는 레지스트리에 푸시된 이미지에 서명하고 이미지 소비자는 서명된 이미지만 가져올 수 있습니다.

### <a name="permit-only-approved-registries"></a>승인된 레지스트리만 허용 

환경에서 승인된 이미지만 사용하도록 보장하는 확장은 승인된 컨테이너 레지스트리의 사용만 허용하는 것입니다. 승인된 컨테이너 레지스트리를 사용하도록 요구하면 알려지지 않은 취약성 또는 보안 문제의 유입 가능성을 제한하여 위험에 대한 노출을 줄입니다. 

### <a name="ensure-the-integrity-of-images-throughout-the-lifecycle"></a>수명 주기 전체에서 이미지의 무결성 보장 

컨테이너 수명 주기 전체에 걸쳐 보안을 관리하는 작업의 일부는 레지스트리에서 변경되거나 프로덕션에 배포될 때 컨테이너 이미지의 무결성을 보장하는 것입니다. 

* 취약성이 있는 이미지는 그 취약성이 사소하더라도 프로덕션 환경에서 실행되지 않도록 해야 합니다. 프로덕션에 배포된 모든 이미지는 이상적으로 소수의 사용자만 액세스할 수 있는 프라이빗 레지스트리에 저장하는 것이 좋습니다. 효과적으로 관리할 수 있도록 프로덕션 이미지 수를 적게 유지합니다.

* 공개적으로 사용 가능한 컨테이너 이미지에서 소프트웨어의 출처를 찾아내는 것은 어렵기 때문에 원본에서 이미지를 빌드하여 레이어의 출처를 알 수 있도록 합니다. 자체 구축된 컨테이너 이미지에 취약성이 있을 경우 고객은 보다 신속하게 해결 방법을 찾을 수 있습니다. 공용 이미지가 있는 고객은 공용 이미지의 루트를 찾아 수정하거나 게시자로부터 다른 보안 이미지를 얻어야 합니다. 

* 프로덕션에 배포된 철저하게 검색된 이미지는 애플리케이션의 수명 동안 최신 상태가 보장되지 않습니다. 보안 취약성은 이전에 알려지지 않았거나 프로덕션 배포 후에 도입 된 이미지 계층에 대해 보고될 수 있습니다. 

  최신 상태가 아니거나 잠시 동안 업데이트되지 않은 이미지를 식별하기 위해 프로덕션 환경에 배포된 이미지를 주기적으로 감사합니다. 청록색 배포 방법론과 롤링 업그레이드 메커니즘을 사용하여 가동 중지 시간 없이 컨테이너 이미지를 업데이트할 수 있습니다. 이전 섹션에서 설명한 도구를 사용하여 이미지를 스캔할 수 있습니다. 

* 보안 검색 스캔이 통합된 CI(연속 통합) 파이프라인을 사용하여 보안 이미지를 빌드한 후 프라이빗 레지스트리로 푸시합니다. CI 솔루션에 기본 제공된 취약성 검색은 모든 테스트를 통과한 이미지가 프로덕션 워크로드가 배포된 프라이빗 레지스트리에 푸시되는지 확인합니다. 

  CI 파이프라인 오류는 취약한 이미지가 프로덕션 워크로드 배포에 사용되는 프라이빗 레지스트리에 푸시되지 않도록 합니다. 또한 이미지 수가 많은 경우에는 이미지 보안 검색을 자동화합니다. 그렇지 않고 이미지에서 보안 취약성을 수동으로 감사하는 것은 매우 번거롭고 오류가 발생하기 쉽습니다. 

### <a name="enforce-least-privileges-in-runtime"></a>런타임에 최소 권한 적용 

최소 권한의 개념은 컨테이너에도 적용되는 기본 보안 모범 사례입니다. 취약점이 악용되면 일반적으로 공격자에게 손상된 애플리케이션 또는 프로세스와 동일한 액세스 및 권한을 부여합니다. 가장 낮은 권한과 액세스로 작업을 완료하도록 컨테이너를 작동하게 하면 위험에 대한 노출을 줄일 수 있습니다. 

### <a name="reduce-the-container-attack-surface-by-removing-unneeded-privileges"></a>불필요한 권한을 제거하여 컨테이너 공격 노출면 감소 

컨테이너 런타임에서 사용되지 않거나 불필요한 프로세스 또는 권한을 제거하여 잠재적인 공격 노출면을 최소화할 수도 있습니다. 권한 있는 컨테이너는 루트로 실행됩니다. 악의적인 사용자 또는 워크로드가 권한 있는 컨테이너에서 벗어나면 컨테이너는 해당 시스템에서 루트로 실행됩니다.

### <a name="preapprove-files-and-executables-that-the-container-is-allowed-to-access-or-run"></a>컨테이너가 액세스하거나 실행할 수 있는 파일 및 실행 파일 사전 승인 

변수 또는 알 수 없음의 수를 줄이면 안정적이고 안정적인 환경을 유지하는 데 도움이 됩니다. 사전 승인되거나 수신 허용 목록에 있는 파일 및 실행 파일만 액세스하거나 실행할 수 있도록 컨테이너를 제한하는 것은 위험에 대한 노출을 차단하는 검증된 방법입니다.  

수신 허용 목록을 처음부터 구현하면 훨씬 쉽게 관리할 수 있습니다. 수신 허용 목록은 애플리케이션이 올바르게 작동하는 데 필요한 파일과 실행 파일을 파악할 때 어느 정도의 제어 및 관리 용이성을 제공합니다. 

수신 허용 목록은 공격 노출면을 줄일 뿐만 아니라 이상 징후에 대한 기준을 제공하고 "노이즈가 많은 주변 환경" 및 컨테이너 중단 시나리오와 같은 사용 사례를 방지할 수 있습니다. 

### <a name="enforce-network-segmentation-on-running-containers"></a>실행 중인 컨테이너에서 네트워크 세분화 적용  

한 서브넷의 컨테이너를 다른 서브넷의 보안 위험으로부터 보호하려면 네트워크 세분화(또는 나노 세분화) 또는 실행 중인 컨테이너 간의 분리를 유지합니다. 규정 준수 의무를 충족해야 하는 업계에서 컨테이너를 사용하려면 네트워크 세분화를 유지해야 할 수도 있습니다.  

예를 들어, 파트너 도구인 [Aqua](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview)는 나노 세분화를 위한 자동화된 접근 방식을 제공합니다. Aqua는 런타임에서 컨테이너 네트워크 활동을 모니터링합니다. 다른 컨테이너, 서비스, IP 주소 및 공용 인터넷에서 모든 인바운드 및 아웃바운드 네트워크 연결을 식별합니다. 나노 세분화는 모니터링된 트래픽에 따라 자동으로 생성됩니다. 

### <a name="monitor-container-activity-and-user-access"></a>컨테이너 작업 및 사용자 액세스 모니터링 

모든 IT 환경과 마찬가지로 의심스럽거나 악의적인 활동을 신속하게 식별하려면 컨테이너 에코시스템에 대한 활동 및 사용자 액세스를 지속적으로 모니터링해야 합니다. Azure는 다음을 비롯한 컨테이너 모니터링 솔루션을 제공합니다.

* [컨테이너용 Azure Monitor](../azure-monitor/containers/container-insights-overview.md)는 AKS(Azure Kubernetes Service)에서 호스팅되는 Kubernetes 환경에 배포된 워크로드의 성능을 모니터링합니다. 컨테이너용 Azure Monitor는 Metrics API를 통해 Kubernetes에서 사용할 수 있는 컨트롤러, 노드 및 컨테이너의 메모리 및 프로세서 메트릭을 수집하여 성능을 시각적으로 표시합니다. 

* [Azure Container Monitoring 솔루션](../azure-monitor/containers/containers.md)을 사용하여 단일 위치에서 Docker 및 Windows 컨테이너 호스트를 보고 관리할 수 있습니다. 예를 들면 다음과 같습니다.

  * 컨테이너와 함께 사용되는 명령을 보여주는 자세한 감사 정보를 봅니다. 
  * 중앙화된 로그를 보고 검색하면 원격으로 Docker 또는 Windows 호스트를 보지 않고도 컨테이너의 문제를 해결합니다.  
  * 호스트에서 노이즈가 많고 과도한 리소스를 사용할 수 있는 컨테이너를 찾습니다.
  * 중앙 집중식 CPU, 메모리, 스토리지, 네트워크 사용량 및 컨테이너 성능 정보를 봅니다.  

  이 솔루션은 Docker Swarm, DC/OS, 관리되지 않는 Kubernetes, Service Fabric 및 Red Hat OpenShift를 포함한 컨테이너 오케스트레이터를 지원합니다. 

### <a name="monitor-container-resource-activity"></a>컨테이너 리소스 작업 모니터링 

컨테이너가 액세스하는 파일, 네트워크 및 기타 리소스와 같은 리소스 활동을 모니터링합니다. 리소스 작업 및 사용 모니터링은 성능 모니터링과 보안 조치 모두에 유용합니다. 

[Azure Monitor](../azure-monitor/overview.md)를 사용하면 메트릭, 활동 로그 및 진단 로그를 수집하도록 허용하여 Azure 서비스에 대한 핵심 모니터링을 수행할 수 있습니다. 예를 들어, 활동 로그는 새 리소스가 만들어지거나 수정되는 시기를 알려줍니다. 

  다양한 리소스와 가상 머신 내부의 운영 체제에 대한 성능 통계를 제공하는 메트릭을 사용할 수 있습니다. Azure Portal에서 탐색기 중 하나를 사용하여 이 데이터를 보고 이러한 메트릭을 기반으로 경고를 만들 수 있습니다. Azure Monitor는 가장 빠른 메트릭 파이프라인을 제공하므로(5분에서 1분으로) 시간이 중요한 경고 및 알림에 사용해야 합니다. 

### <a name="log-all-container-administrative-user-access-for-auditing"></a>감사를 위해 모든 컨테이너 관리 사용자 액세스 로그 

Kubernetes 클러스터, 컨테이너 레지스트리 및 컨테이너 이미지를 포함하여 컨테이너 에코시스템에 대한 관리 액세스에 대한 정확한 감사 추적을 유지 관리합니다. 이러한 로그는 감사 목적으로 필요할 수 있으며 보안 인시던트 후 포렌식 증거로 유용합니다. Azure 솔루션에는 다음이 포함됩니다.

* [Microsoft Defender for Cloud와 Azure Kubernetes Service 통합하여](../security-center/defender-for-kubernetes-introduction.md) 클러스터 환경의 보안 구성을 모니터링하고 보안 권장 사항을 생성합니다.
* [Azure Container Monitoring 솔루션](../azure-monitor/containers/containers.md)
* [Azure Container Instances](container-instances-log-analytics.md) 및 [Azure Container Registry](../container-registry/monitor-service.md)에 대한 리소스 로그

## <a name="next-steps"></a>다음 단계

* 배포의 보안 상태를 개선하는 데 도움이 되는 포괄적인 권장 사항은 [Container Instances에 대한 Azure 보안 기준](security-baseline.md)을 참조하세요.

* 컨테이너화된 환경에서 실시간 위협 탐지를 [위해 Microsoft Defender for Cloud를](../security-center/container-security.md) 사용하는 방법에 대해 자세히 알아보세요.

* [Twistlock](https://www.twistlock.com/solutions/microsoft-azure-container-security/) 및 [Aqua Security](https://www.aquasec.com/solutions/azure-container-security/)의 솔루션으로 컨테이너 취약성을 관리하는 방법에 대해 자세히 알아보세요.
