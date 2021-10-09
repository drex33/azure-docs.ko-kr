---
title: Azure Kubernetes Service에 자체 호스팅 게이트웨이 배포
description: Azure Kubernetes Service에 Azure API Management의 자체 호스팅 게이트웨이 구성 요소를 배포하는 방법에 대해 알아봅니다.
author: dlepow
manager: gwallace
ms.service: api-management
ms.topic: article
ms.date: 06/11/2021
ms.author: danlep
ms.openlocfilehash: 624229e947a872f4ea50c0a4e61e210fee9d946f
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129708437"
---
# <a name="deploy-to-azure-kubernetes-service"></a>Azure Kubernetes Service에 배포

이 문서에서는 Azure API Management의 자체 호스팅 게이트웨이 구성 요소를 [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/)에 배포하는 단계를 제공합니다. Kubernetes 클러스터에 자체 호스팅 게이트웨이를 배포하려면 [방법 문서](how-to-deploy-self-hosted-gateway-kubernetes.md)를 참조하세요.

> [!NOTE]
> [Azure Arc 지원 Kubernetes 클러스터에](how-to-deploy-self-hosted-gateway-azure-arc.md) 자체 호스팅 게이트웨이를 [클러스터 확장으로](../azure-arc/kubernetes/extensions.md)배포할 수도 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

- [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)
- [Azure Kubernetes 클러스터 만들기](../aks/kubernetes-walkthrough-portal.md)
- [API Management 인스턴스에서 게이트웨이 리소스 프로비저닝](api-management-howto-provision-self-hosted-gateway.md)

## <a name="deploy-the-self-hosted-gateway-to-aks"></a>AKS에 자체 호스팅 게이트웨이 배포

1. **배포 및 인프라** 에서 **게이트웨이** 를 선택합니다.
2. 배포하려는 자체 호스팅 게이트웨이 리소스를 선택합니다.
3. **배포** 를 선택합니다.
4. **토큰** 텍스트 상자의 새 토큰은 기본 **만료** 및 **비밀 키** 값을 사용하여 자동으로 생성되었습니다. 원하는 경우 둘 중 하나 또는 둘 다를 조정하고 **생성** 을 선택하여 새 토큰을 만듭니다.
5. **배포 스크립트** 아래에서 **Kubernetes** 가 선택되어 있는지 확인합니다.
6. **배포** 옆에 있는 **\<gateway-name\> .yml** 파일 링크를 선택하여 파일을 다운로드합니다.
7. 필요에 따라 yml 파일의 `config.service.endpoint`, 포트 매핑 및 컨테이너 이름을 조정합니다.
8. 시나리오에 따라 [서비스 유형](../aks/concepts-network.md#services)을 변경해야 할 수도 있습니다. 
    * 기본값은 외부 부하 분산 장치인 `LoadBalancer`입니다. 
    * [내부 부하 분산 장치](../aks/internal-lb.md)를 사용하여 자체 호스팅 게이트웨이에 대한 액세스를 내부 사용자로만 제한할 수 있습니다. 
    * 아래 샘플에서는 `NodePort`를 사용합니다.
1. **배포** 텍스트 상자의 오른쪽 끝에 있는 **복사** 아이콘을 선택하여 `kubectl` 명령을 클립보드에 저장합니다.
1. 명령을 터미널(또는 명령) 창에 붙여넣습니다. 이 명령은 다운로드한 환경 파일이 현재 디렉터리에 있는 것으로 예상합니다.

   ```console
   kubectl apply -f <gateway-name>.yaml
   ```
   
1. 명령을 실행합니다. 이 명령은 AKS 클러스터에 다음을 지시합니다.
    * Microsoft Container Registry에서 다운로드한 자체 호스팅 게이트웨이의 이미지를 사용하여 컨테이너를 실행합니다. 
    * HTTP(8080) 및 HTTPS(443) 포트를 노출하도록 컨테이너를 구성합니다.
1. 아래 명령을 실행하여 게이트웨이 Pod가 실행하고 있는지 확인합니다. Pod 이름이 달라집니다.

   ```console
   kubectl get pods
   NAME                                   READY     STATUS    RESTARTS   AGE
   contoso-apim-gateway-59f5fb94c-s9stz   1/1       Running   0          1m
   ```

1. 아래 명령을 실행하여 게이트웨이 서비스가 실행하고 있는지 확인합니다. 서비스 이름과 IP 주소가 달라집니다.
    ```console
    kubectl get services
    NAME             TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
    contosogateway   NodePort    10.110.230.87   <none>        80:32504/TCP,443:30043/TCP   1m
    ```
1. Azure Portal로 돌아가서 배포한 게이트웨이 노드가 정상 상태를 보고하는지 확인합니다.

> [!TIP]
> `kubectl logs <gateway-pod-name>` 명령을 사용하여 자체 호스팅 게이트웨이 로그의 스냅샷을 봅니다.

## <a name="next-steps"></a>다음 단계

* 자체 호스팅 게이트웨이에 대한 자세한 내용은 [Azure API Management 자체 호스팅 게이트웨이 개요](self-hosted-gateway-overview.md)를 참조하세요.
* [Azure Arc 지원 Kubernetes 클러스터에 API Management 자체 호스팅 게이트웨이를 배포하는 방법을 알아봅니다.](how-to-deploy-self-hosted-gateway-azure-arc.md)
* [Azure Kubernetes Service](../aks/intro-kubernetes.md)에 대해 자세히 알아봅니다.
* [클라우드에서 로그를 구성하고 유지하는 방법](how-to-configure-cloud-metrics-logs.md)을 알아봅니다.
* [로그를 로컬로 구성하고 유지하는 방법](how-to-configure-local-metrics-logs.md)을 알아봅니다.
