---
title: Azure Arc의 App Service
description: Azure 운영자를 위한 Azure Arc App Service 통합 소개입니다.
ms.topic: article
ms.date: 11/02/2021
ms.openlocfilehash: 74cf4063d92aa5d563df881f2210e2ca5d08543e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131440358"
---
# <a name="app-service-functions-and-logic-apps-on-azure-arc-preview"></a>Azure Arc의 App Service, Functions 및 Logic Arc(미리 보기)

Azure Arc 지원 Kubernetes 클러스터에서 App Service, Functions 및 Logic Apps 실행할 수 있습니다. Kubernetes 클러스터는 온-프레미스이거나 타사 클라우드에서 호스트될 수 있습니다. 이 방법을 통해 앱 개발자는 App Service 기능을 활용할 수 있습니다. 이와 동시에 IT 관리자는 내부 인프라에서 App Service 앱을 호스트하여 회사 규정 준수를 유지할 수 있습니다. 또한 다른 IT 운영자는 기존 Kubernetes 클러스터에서 App Service를 실행하여 이전에 투자한 다른 클라우드 공급자를 보호할 수 있습니다.

> [!NOTE]
> App Service, Functions 및 Logic Apps에 대해 Kubernetes 클러스터를 설정하는 방법을 알아보려면 [App Service Kubernetes 환경 만들기(미리 보기)](manage-create-arc-environment.md)를 참조하세요.

대부분의 경우 앱 개발자는 배포된 Kubernetes 환경을 나타내는 올바른 Azure 지역에 배포하는 방법만 알면 됩니다. 환경을 제공하고 기본 Kubernetes 인프라를 유지 관리하는 운영자는 다음 Azure 리소스를 알고 있어야 합니다.

- 연결된 클러스터, 이는 Kubernetes 인프라의 Azure 프로젝션입니다. 자세한 내용은 [Azure Arc 지원 Kubernetes란?을 참조하세요.](../azure-arc/kubernetes/overview.md)
- 클러스터 확장, 이는 연결된 클러스터 리소스의 하위 리소스입니다. App Service 확장은 [연결된 클러스터에 필수 Pod를 설치합니다](#pods-created-by-the-app-service-extension). 클러스터 확장에 대한 자세한 내용은 [Azure Arc 지원 Kubernetes의 클러스터 확장을 참조하세요.](../azure-arc/kubernetes/conceptual-extensions.md)
- 사용자 지정 위치, 이는 확장 그룹을 번들로 묶어 만든 리소스의 네임스페이스에 매핑합니다. 자세한 내용은 [Azure Arc 지원 Kubernetes 위에 있는 사용자 지정 위치를 참조하세요.](../azure-arc/kubernetes/conceptual-custom-locations.md)
- App Service Kubernetes 환경, 이는 앱 간에 공통적인 구성을 사용할 수 있지만 클러스터 작업과는 관련이 없습니다. 개념적으로 사용자 지정 위치 리소스에 배포되고 앱 개발자는 이 환경에 앱을 만듭니다. 이에 대해서는 [App Service Kubernetes 환경](#app-service-kubernetes-environment)에 자세히 설명되어 있습니다.

## <a name="public-preview-limitations"></a>공용 미리 보기 제한 사항

App Service Kubernetes 환경에는 다음과 같은 공개 미리 보기 제한 사항이 적용됩니다. 변경 내용이 사용 가능해지면 업데이트됩니다.

| 제한 사항                                              | 세부 정보                                                                               |
|---------------------------------------------------------|---------------------------------------------------------------------------------------|
| 지원되는 Azure 지역                                 | 미국 동부, 서부 유럽                                                                  |
| 클러스터 네트워킹 요구 사항                          | `LoadBalancer` 서비스 유형을 지원하고 공개적으로 주소를 지정할 수 있는 고정 IP를 제공해야 합니다. |
| 클러스터 스토리지 요구 사항                             | 해당하는 경우 코드 기반 앱의 배포 및 빌드를 지원하려면 확장에서 사용할 수 있는 클러스터 연결 스토리지 클래스가 있어야 합니다.                      |
| 기능: 네트워킹                                     | [사용할 수 없음(클러스터 네트워킹에 따라 다름)](#are-networking-features-supported)      |
| 기능: 관리되는 ID                             | [사용할 수 없음](#are-managed-identities-supported)                                    |
| 기능: Key Vault 참조                           | 사용할 수 없음(관리되는 ID에 따라 다름)                                         |
| 기능: 관리되는 ID를 사용하여 ACR에서 이미지 끌어오기     | 사용할 수 없음(관리되는 ID에 따라 다름)                                         |
| 기능: Functions 및 Logic Apps에 대한 포털 내 편집 | 사용할 수 없음                                                                         |
| 기능: FTP 게시                                 | 사용할 수 없음                                                                         |
| 로그                                                    | Log Analytics는 클러스터 확장으로 구성해야 합니다. 사이트별로 구성하지 않습니다.                 |

## <a name="pods-created-by-the-app-service-extension"></a>App Service 확장에 의해 만들어진 Pod

App Service 확장이 Azure Arc 지원 Kubernetes 클러스터에 설치되면 지정된 릴리스 네임스페이스에 생성된 여러 Pod가 표시됩니다. 이러한 Pod를 사용하면 Kubernetes 클러스터가 Azure에서 `Microsoft.Web` 리소스 공급자의 확장이 되고 앱의 관리 및 운영을 지원할 수 있습니다. 필요에 따라 이벤트 기반 크기 조정을 위해 확장이 [KEDA](https://keda.sh/)를 설치하도록 선택할 수 있습니다.
 <!-- You can only have one installation of KEDA on the cluster. If you have one already, you must disable this behavior during installation of the cluster extension `TODO`. -->

다음 표에서는 기본적으로 만들어지는 각 Pod의 역할에 대해 설명합니다.

| Pod                                   | 설명                                                                                                                       |
|---------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------|
| `<extensionName>-k8se-app-controller` | 클러스터에 리소스를 만들고 구성 요소의 상태를 유지 관리하는 핵심 운영자 Pod입니다.                                |
| `<extensionName>-k8se-envoy`          | 모든 데이터 평면 요청에 대한 프런트 엔드 프록시 계층입니다. 인바운드 트래픽을 올바른 앱으로 라우팅합니다.                           |
| `<extensionName>-k8se-activator`      | 시스템에서 사용 가능한 첫 번째 인스턴스를 가져오는 동안 0으로 크기가 조정된 앱에 도움이 되는 대체 라우팅 대상입니다. |
| `<extensionName>-k8se-build-service`  | 배포 작업을 지원하고 [고급 도구 기능](resources-kudu.md)을 제공합니다.                                        |
| `<extensionName>-k8se-http-scaler`    | [KEDA](https://keda.sh)에 크기 조정 정보를 제공하기 위해 인바운드 요청 볼륨을 모니터링합니다.                               |
| `<extensionName>-k8se-img-cacher`     | 자리 표시자 및 앱 이미지를 노드의 로컬 캐시로 끌어옵니다.                                                                  |
| `<extensionName>-k8se-log-processor`  | 앱 및 기타 구성 요소에서 로그를 수집하고 Log Analytics로 보냅니다.                                                      |
| `placeholder-azure-functions-*`       | Azure Functions에 대한 콜드 시작 속도를 높이는 데 사용됩니다.                                                                                 |

## <a name="app-service-kubernetes-environment"></a>App Service Kubernetes 환경

앱을 만들려면 App Service Kubernetes 환경 리소스가 필요합니다. 이를 통해 기본 DNS 접미사와 같이 사용자 지정 위치의 앱에 공통적으로 구성할 수 있습니다.

사용자 지정 위치에 하나의 Kubernetes 환경 리소스만 만들 수 있습니다. 대부분의 경우 앱을 만들고 배포하는 개발자는 리소스를 직접 인식할 필요가 없습니다. 제공된 사용자 지정 위치 ID에서 직접 유추할 수 있습니다. 그러나 Azure Resource Manager 템플릿을 정의할 때 계획 리소스는 환경의 리소스 ID를 직접 참조해야 합니다. 계획 및 지정된 환경의 사용자 지정 위치 값이 일치해야 합니다.

## <a name="faq-for-app-service-functions-and-logic-apps-on-azure-arc-preview"></a>Azure Arc의 App Service, Functions 및 Logic Apps(미리 보기)에 대한 FAQ

- [가격은 얼마나 되나요?](#how-much-does-it-cost)
- [Windows 및 Linux 앱을 모두 지원하나요?](#are-both-windows-and-linux-apps-supported)
- [어떤 기본 제공 애플리케이션 스택을 지원하나요?](#which-built-in-application-stacks-are-supported)
- [모든 앱 배포 유형을 지원하나요?](#are-all-app-deployment-types-supported)
- [어떤 App Service 기능이 지원되나요?](#which-app-service-features-are-supported)
- [네트워킹 기능을 지원하나요?](#are-networking-features-supported)
- [관리 ID가 지원되나요?](#are-managed-identities-supported)
- [스케일링 한도가 있나요?](#are-there-any-scaling-limits)
- [어떤 로그가 수집되나요?](#what-logs-are-collected)
- [공급자 등록 오류가 표시되는 경우 어떻게 해야 하나요?](#what-do-i-do-if-i-see-a-provider-registration-error)
- [ARM64 기반 클러스터에 애플리케이션 서비스 확장을 배포할 수 있나요?](#can-i-deploy-the-application-services-extension-on-an-arm64-based-cluster)

### <a name="how-much-does-it-cost"></a>가격은 얼마인가요?

공개 미리 보기 기간에는 Azure Arc의 App Service가 무료로 제공됩니다.

### <a name="are-both-windows-and-linux-apps-supported"></a>Windows 및 Linux 앱을 모두 지원하나요?

코드 및 사용자 지정 컨테이너에는 Linux 기반 앱만 지원됩니다. Windows 앱은 지원되지 않습니다.

### <a name="which-built-in-application-stacks-are-supported"></a>어떤 기본 제공 애플리케이션 스택을 지원하나요?

모든 기본 제공 Linux 스택이 지원됩니다.

### <a name="are-all-app-deployment-types-supported"></a>모든 앱 배포 유형을 지원하나요?

FTP 배포는 지원되지 않습니다. 현재 `az webapp up`도 지원되지 않습니다. Git, ZIP, CI/CD, Visual Studio, Visual Studio Code 등의 기타 배포 방법이 지원됩니다.

### <a name="which-app-service-features-are-supported"></a>어떤 App Service 기능을 지원하나요?

미리 보기 기간 동안 특정 App Service 기능의 유효성을 검사하고 있습니다. 지원되는 경우 Azure Portal의 왼쪽 탐색 옵션이 활성화됩니다. 아직 지원되지 않는 기능은 회색으로 표시됩니다.

### <a name="are-networking-features-supported"></a>네트워킹 기능을 지원하나요?

아니요. 하이브리드 연결, Virtual Network 통합 또는 IP 제한과 같은 네트워킹 기능은 지원되지 않습니다. 네트워킹은 Kubernetes 클러스터 자체의 네트워킹 규칙에서 직접 처리해야 합니다.

### <a name="are-managed-identities-supported"></a>관리 ID를 지원하나요?

아니요. Azure Arc에서 실행할 때 앱에 관리 ID를 할당할 수 없습니다. 앱에 다른 Azure 리소스 작업을 위한 ID가 필요한 경우 대신 [애플리케이션 서비스 주체](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)를 사용하는 것이 좋습니다.

### <a name="are-there-any-scaling-limits"></a>스케일링 한도가 있나요?

Azure Arc를 사용하는 Kubernetes의 Azure App Service와 함께 배포된 모든 애플리케이션은 기본 Kubernetes 클러스터의 한도 내에서 스케일링할 수 있습니다.  기본 Kubernetes 클러스터에서 사용 가능한 컴퓨팅 리소스(주로 CPU 및 메모리)가 부족하면 애플리케이션은 Kubernetes가 사용 가능한 리소스로 예약할 수 있는 애플리케이션의 인스턴스 수로만 스케일링할 수 있습니다.

### <a name="what-logs-are-collected"></a>어떤 로그가 수집되나요?

시스템 구성 요소와 애플리케이션 모두에 대한 로그는 표준 출력에 기록됩니다. 두 로그 유형 모두를 표준 Kubernetes 도구를 사용하여 분석을 위해 수집할 수 있습니다. 또한 [Log Analytics 작업 영역](../azure-monitor/logs/log-analytics-overview.md)을 사용하여 App Service 클러스터 확장을 구성할 수도 있으며 모든 로그를 해당 작업 영역으로 보냅니다.

기본적으로 시스템 구성 요소의 로그는 Azure 팀으로 전송됩니다. 애플리케이션 로그는 전송되지 않습니다. `logProcessor.enabled=false`를 확장 구성 설정으로 설정하여 이러한 로그가 전송되지 않도록 방지할 수 있습니다. 이렇게 하면 Log Analytics 작업 영역에도 애플리케이션이 전달되지 않습니다. 로그 프로세서를 사용하지 않도록 하면 지원 사례에 필요한 시간에 영향을 줄 수 있으며 표준 출력에서 다른 방법을 통해 로그를 수집하라는 메시지가 표시됩니다.

### <a name="what-do-i-do-if-i-see-a-provider-registration-error"></a>공급자 등록 오류가 표시되는 경우 어떻게 해야 하나요?

Kubernetes 환경 리소스를 만들 때 일부 구독에 “등록된 리소스 공급자를 찾을 수 없음” 오류가 표시될 수 있습니다. 오류 세부 정보에는 유효한 것으로 간주되는 위치 및 API 버전 세트가 포함될 수 있습니다. 이 경우 Microsoft.Web 공급자에 대해 구독을 다시 등록해야 할 수 있습니다. 이 작업은 기존 애플리케이션 또는 API에 영향을 주지 않습니다. 다시 등록하려면 Azure CLI를 사용하여 `az provider register --namespace Microsoft.Web --wait`를 실행합니다. 그런 다음, Kubernetes 환경 명령을 다시 시도합니다.

### <a name="can-i-deploy-the-application-services-extension-on-an-arm64-based-cluster"></a>ARM64 기반 클러스터에 애플리케이션 서비스 확장을 배포할 수 있나요?

ARM64 기반 클러스터는 현재 지원되지 않습니다.  

## <a name="extension-release-notes"></a>확장 릴리스 정보

### <a name="application-services-extension-v-090-may-2021"></a>응용 프로그램 서비스 확장 v 0.9.0 (2021 년 5 월)

- 응용 프로그램 서비스 확장의 초기 공개 미리 보기 릴리스입니다.
- 웹, 함수 및 논리 응용 프로그램의 코드 및 컨테이너 기반 배포를 지원 합니다.
- 웹 응용 프로그램 런타임 지원-.NET 3.1 및 5.0; 노드 JS 12 및 14 Python 3.6, 3.7 및 3.8; PHP 7.3 및 7.4; Ruby 2.5, 2.5.5, 2.6 및 2.6.2 critical; Java SE 8u232, 8u232, 8u232, 11.05, 11.06 및 11.07; Tomcat 8.5, 8.5.41, 8.5.53, 8.5.57, 9.0, 9.0.20, 9.0.33 및 9.0.37.

### <a name="application-services-extension-v-0100-november-2021"></a>응용 프로그램 서비스 확장 v 0.10.0 (11 월 2021)

확장이 안정적인 버전에 있고 자동 업그레이드-부 버전이 사용으로 설정 된 경우 확장은 자동으로 업그레이드 됩니다.  확장을 최신 버전의 확장으로 수동으로 업그레이드 하려면 아래 명령을 실행할 수 있습니다.

- 엔보이 끝점에 할당 하는 데 필요한 미리 할당 된 고정 IP 주소에 대 한 요구 사항이 제거 되었습니다.
- Keda를 v 2.4.0로 업그레이드
- V 1.19.0로 엔보이 업그레이드
- Azure Function runtime을 v 3.3.1로 업그레이드
- App Controller 및 엔보이 컨트롤러의 기본 복제본 수를 2로 설정 하 여 더 많은 안정성 추가

확장이 안정적인 버전에 있고 자동 업그레이드-부 버전이 true로 설정 된 경우 확장은 자동으로 업그레이드 됩니다.  확장을 최신 버전으로 수동으로 업그레이드 하려면 아래 명령을 실행할 수 있습니다.

```azurecli-interactive
    az k8s-extension update --cluster-type connectedClusters -c <clustername> -g <resource group> -n <extension name> --release-train stable --version 0.10.0
```


## <a name="next-steps"></a>다음 단계

[App Service Kubernetes 환경 만들기(미리 보기)](manage-create-arc-environment.md)
