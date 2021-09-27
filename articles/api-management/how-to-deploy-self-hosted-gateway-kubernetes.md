---
title: Kubernetes에 자체 호스팅 게이트웨이 배포
description: Azure API Management의 자체 호스팅 게이트웨이 구성 요소를 Kubernetes에 배포하는 방법 알아보기
author: dlepow
manager: gwallace
ms.service: api-management
ms.workload: mobile
ms.topic: article
ms.author: danlep
ms.date: 05/25/2021
ms.openlocfilehash: dda6fcbf71914fefcd1afd2056864ad66aa11d4d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128609824"
---
# <a name="deploy-a-self-hosted-gateway-to-kubernetes"></a>Kubernetes에 자체 호스팅 게이트웨이 배포

이 문서에서는 Azure API Management의 자체 호스팅 게이트웨이 구성 요소를 Kubernetes 클러스터에 배포하는 단계를 설명합니다.

> [!NOTE]
> 자체 호스팅 게이트웨이를 [Azure Arc 지원 Kubernetes 클러스터](how-to-deploy-self-hosted-gateway-azure-arc.md)에 [클러스터 확장](../azure-arc/kubernetes/extensions.md)으로 배포할 수도 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

- 다음 빠른 시작을 완료합니다. [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)
- Kubernetes 클러스터를 만듭니다.
   > [!TIP]
   > [단일 노드 클러스터](https://kubernetes.io/docs/setup/#learning-environment)는 개발 및 평가 목적에 적합합니다. 프로덕션 워크로드에는 온-프레미스 또는 클라우드에서 [Kubernetes 인증](https://kubernetes.io/partners/#conformance) 다중 노드 클러스터를 사용하세요.
- [API Management 인스턴스에 자체 호스팅 게이트웨이 리소스를 프로비전](api-management-howto-provision-self-hosted-gateway.md)합니다.

## <a name="deploy-to-kubernetes"></a>Kubernetes에 배포

1. **배포 및 인프라** 아래에서 **게이트웨이** 를 선택합니다.
2. 배포하려는 자체 호스팅 게이트웨이 리소스를 선택합니다.
3. **배포** 를 선택합니다.
4. **토큰** 텍스트 상자의 액세스 토큰은 기본 **만료** 및 **비밀 키** 값을 기반으로 자동 생성되었습니다. 필요한 경우 컨트롤 중 하나 또는 둘 모두에서 원하는 값을 선택하여 새 토큰을 생성합니다.
5. **배포 스크립트** 아래에서 **Kubernetes** 탭을 선택합니다.
6. **\<gateway-name\>.yml** 파일 링크를 선택하고 YAML 파일을 다운로드합니다.
7. **배포** 텍스트 상자의 오른쪽 아래 모서리에 있는 **복사** 아이콘을 선택하여 `kubectl` 명령을 클립보드에 저장합니다.
8. 명령을 터미널(또는 명령) 창에 붙여넣습니다. 첫 번째 명령은 4단계에서 생성한 액세스 토큰을 포함하는 Kubernetes 비밀을 만듭니다. 두 번째 명령은 6단계에서 다운로드한 구성 파일을 Kubernetes 클러스터에 적용하고 파일이 현재 디렉터리에 있는 것으로 예상합니다.
9. 명령을 실행하여 [기본 네임스페이스](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)에 필요한 Kubernetes 개체를 만들고 Microsoft Container Registry에서 다운로드한 [컨테이너 이미지](https://aka.ms/apim/sputnik/dhub)에서 자체 호스팅 게이트웨이 Pod를 시작합니다.
10. 다음 명령을 실행하여 배포가 성공했는지 확인합니다. 모든 개체를 만들고 Pod가 초기화될 때까지 시간이 약간 걸릴 수 있습니다.

    ```console
    kubectl get deployments
    NAME             READY   UP-TO-DATE   AVAILABLE   AGE
    <gateway-name>   1/1     1            1           18s
    ```
11. 다음 명령을 실행하여 서비스가 성공적으로 만들어졌는지 확인합니다. 서비스 IP와 포트가 다를 것입니다.

    ```console
    kubectl get services
    NAME             TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
    <gateway-name>   LoadBalancer   10.99.236.168   <pending>     80:31620/TCP,443:30456/TCP   9m1s
    ```
1. Azure Portal로 돌아가서 **개요** 를 선택합니다.
1. **상태** 에 녹색 확인 표시가 있고 그 뒤에 YAML 파일에 지정된 복제본 수와 일치하는 노드 수가 표시되는지 확인합니다. 이 상태는 배포된 자체 호스팅 게이트웨이 Pod가 API Management 서비스와 성공적으로 통신하고 있으며 "하트비트"가 규칙적이라는 것을 의미합니다.

    ![게이트웨이 상태](media/how-to-deploy-self-hosted-gateway-kubernetes/status.png)

> [!TIP]
> 게이트웨이가 여러 개 있는 경우 `kubectl logs deployment/<gateway-name>` 명령을 실행하여 임의로 선택한 Pod의 로그를 확인합니다.
> 명령 옵션 전체 세트에 대해 `kubectl logs -h`를 실행합니다(예: 특정 Pod 또는 컨테이너에 대한 로그를 보는 방법).

## <a name="production-deployment-considerations"></a>프로덕션 배포 고려 사항

### <a name="access-token"></a>액세스 토큰
유효한 액세스 토큰이 없으면 자체 호스팅 게이트웨이가 연결된 API Management 서비스의 엔드포인트에 액세스하여 구성 데이터를 다운로드할 수 없습니다. 액세스 토큰은 최대 30일 동안 유효합니다. 만료되기 전에 수동으로 또는 자동화를 통해 액세스 토큰을 다시 생성하고 클러스터를 새 토큰으로 구성해야 합니다.

토큰 새로 고침을 자동화하는 경우 [이 관리 API 작업](/rest/api/apimanagement/2020-12-01/gateway/generate-token)을 사용하여 새 토큰을 생성합니다. Kubernetes 비밀 관리에 대한 자세한 내용은 [Kubernetes 웹 사이트](https://kubernetes.io/docs/concepts/configuration/secret)를 참조하세요.

### <a name="namespace"></a>네임스페이스
Kubernetes [네임스페이스](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)는 단일 클러스터를 여러 팀, 프로젝트 또는 애플리케이션 간에 분할하는 데 도움이 됩니다. 네임스페이스는 리소스 및 이름의 범위를 제공합니다. 리소스 할당량 및 액세스 제어 정책과 연결될 수 있습니다.

Azure Portal은 **기본** 네임스페이스에 자체 호스팅 게이트웨이 리소스를 만드는 명령을 제공합니다. 이 네임스페이스는 자동으로 만들어지고, 모든 클러스터에 있으며, 삭제할 수 없습니다.
프로덕션에서 자체 호스팅 게이트웨이를 [만들고 별도의 네임스페이스에 배포](https://www.kubernetesbyexample.com/)하는 것이 좋습니다.

### <a name="number-of-replicas"></a>복제본 수
프로덕션에 적합한 최소 복제본 수는 2개입니다.

기본적으로 자체 호스팅 게이트웨이는 **RollingUpdate** 배포 [전략](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#strategy)을 사용하여 배포됩니다. 기본값을 검토하고, 특히 복제본 수가 많은 경우 [maxUnavailable](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#max-unavailable) 및 [maxSurge](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#max-surge) 필드를 명시적으로 설정하는 것이 좋습니다.

### <a name="container-resources"></a>컨테이너 리소스
기본적으로 Azure Portal에서 제공하는 YAML 파일은 컨테이너 리소스 요청을 지정하지 않습니다.

컨테이너당 CPU 및 메모리 리소스의 양과 특정 워크로드를 지원하는 데 필요한 복제본 수를 안정적으로 예측하고 권장하는 것이 불가능합니다. 다음과 같은 많은 요소가 있습니다.

- 클러스터가 실행 중인 특정 하드웨어
- 가상화의 현재 상태 및 유형
- 동시 클라이언트 연결 수 및 속도
- 요청 속도.
- 구성된 정책의 종류 및 수
- 페이로드 크기 및 페이로드가 버퍼링되는지 아니면 스트리밍되는지 여부
- 백 엔드 서비스 대기 시간

처음에는 리소스 요청을 2개 코어와 2GiB로 설정하는 것이 좋습니다. 부하 테스트를 수행하고 그 결과에 따라 스케일 업/아웃하거나 스케일 다운하면 됩니다.

### <a name="container-image-tag"></a>컨테이너 이미지 태그
Azure Portal에서 제공하는 YAML 파일은 **최신** 태그를 사용합니다. 이 태그는 항상 자체 호스팅 게이트웨이 컨테이너 이미지의 최신 버전을 참조합니다.

의도치 않게 최신 버전으로 업그레이드되는 일이 없도록 프로덕션에서 특정 버전 태그를 사용하는 것이 좋습니다.

[사용 가능한 태그의 전체 목록을 다운로드](https://mcr.microsoft.com/v2/azure-api-management/gateway/tags/list)할 수 있습니다.

### <a name="dns-policy"></a>DNS 정책
DNS 이름 확인은 Azure의 종속성에 연결하고 API의 백엔드 서비스 호출을 디스패치하는 자체 호스팅 게이트웨이의 기능에 있어서 매우 중요한 역할을 합니다.

Azure Portal에 제공되는 YAML 파일은 기본 [ClusterFirst](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-policy) 정책을 적용합니다. 이 정책은 클러스터 DNS에서 확인하지 못한 이름 확인 요청을 노드에서 상속된 업스트림 DNS 서버로 전달하게 합니다.

Kubernetes의 이름 확인에 대한 자세한 내용은 [Kubernetes 웹 사이트](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service)를 참조하세요. 설치에 적합하도록 [DNS 정책](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-policy) 또는 [DNS 구성](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-config)을 사용자 지정하는 방안을 고려해 보세요.

### <a name="external-traffic-policy"></a>외부 트래픽 정책
Azure Portal에서 제공하는 YAML 파일은 [Service](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.19/#service-v1-core) 개체의 `externalTrafficPolicy` 필드를 `Local`로 설정합니다. 따라서 호출자 IP 주소([요청 컨텍스트](api-management-policy-expressions.md#ContextVariables)에서 액세스 가능)가 유지되고 노드 간 부하 분산이 사용되지 않으므로 네트워크 홉이 발생하지 않습니다. 이 설정을 사용하면 노드당 게이트웨이 Pod 수가 균등하지 않은 배포에서 트래픽이 비대칭적으로 배포될 수 있습니다.

### <a name="custom-domain-names-and-ssl-certificates"></a>사용자 지정 도메인 이름 및 SSL 인증서

API Management 엔드포인트에 사용자 지정 도메인 이름을 사용하는 경우, 특히 Management 엔드포인트에 사용자 지정 도메인 이름을 사용하는 경우 **\<gateway-name\>.yaml** 파일의 `config.service.endpoint` 값을 업데이트하여 기본 도메인 이름을 사용자 지정 도메인 이름으로 바꿔야 할 수 있습니다. Kubernetes 클러스터에 있는 자체 호스팅 게이트웨이의 Pod에서 Management 엔드포인트에 액세스할 수 있어야 합니다.

이 시나리오에서 Management 엔드포인트에서 사용하는 SSL 인증서가 잘 알려진 CA 인증서로 서명되지 않은 경우 자체 호스팅 게이트웨이의 Pod에서 CA 인증서를 신뢰하는지 확인해야 합니다.

### <a name="configuration-backup"></a>구성 백업
일시적으로 Azure 연결이 중단되었을 때 자체 호스팅 게이트웨이가 어떻게 동작하는지 알아보려면 [자체 호스팅 게이트웨이 개요](self-hosted-gateway-overview.md#connectivity-to-azure)를 참조하세요.

자체 호스팅 게이트웨이 컨테이너에 대한 로컬 스토리지 볼륨을 구성합니다. 그러면 다운로드한 최신 구성의 백업 복사본을 유지할 수 있습니다. 연결이 중단되면 스토리지 볼륨은 다시 시작할 때 백업 복사본을 사용할 수 있습니다. 볼륨 탑재 경로는 <code>/apim/config</code>여야 합니다. [GitHub](https://github.com/Azure/api-management-self-hosted-gateway/blob/master/examples/self-hosted-gateway-with-configuration-backup.yaml)의 예제를 참조하세요.
Kubernetes의 스토리지에 대한 자세한 내용은 [Kubernetes 웹 사이트](https://kubernetes.io/docs/concepts/storage/volumes/)를 참조하세요.

### <a name="local-logs-and-metrics"></a>로컬 로그 및 메트릭
자체 호스팅 게이트웨이는 연결된 API Management 서비스의 구성 설정에 따라 [Azure Monitor](api-management-howto-use-azure-monitor.md) 및 [Azure Application Insights](api-management-howto-app-insights.md)에 원격 분석 데이터를 보냅니다.
[Azure에 대한 연결](self-hosted-gateway-overview.md#connectivity-to-azure)이 일시적으로 끊어지면 Azure로 전달되는 원격 분석 흐름이 중단되고 가동 중단 기간 동안 데이터가 손실됩니다.
Azure 연결 중단 시 API 트래픽을 관찰하고 원격 분석 데이터 손실을 방지할 수 있도록 [로컬 모니터링을 설정](how-to-configure-local-metrics-logs.md)하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

* 자체 호스팅 게이트웨이에 대한 자세한 내용은 [자체 호스팅 게이트웨이 개요](self-hosted-gateway-overview.md)를 참조하세요.
* [API Management 자체 호스팅 게이트웨이를 Azure Arc 지원 Kubernetes 클러스터에 배포하는 방법](how-to-deploy-self-hosted-gateway-azure-arc.md)에 대해 알아봅니다.
