---
title: Kubernetes 워크로드에 대한 워크로드 보호
description: 클라우드용 Microsoft Defender의 Kubernetes 워크로드 보호 보안 권장 사항 집합을 사용하는 방법을 알아봅니다.
services: security-center
author: memildin
manager: rkarlin
ms.service: defender-for-cloud
ms.topic: how-to
ms.date: 11/09/2021
ms.author: memildin
ms.openlocfilehash: 5f8bf1f34ba33a4a6c3d258e8cb813d062f1f98f
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132529338"
---
# <a name="protect-your-kubernetes-workloads"></a>Kubernetes 워크로드 보호

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

이 페이지에서는 Kubernetes 워크로드 보호 전용 클라우드용 Microsoft Defender의 보안 권장 사항 집합을 사용하는 방법을 설명합니다.

[Kubernetes 허용 제어를 사용한 워크로드 보호 모범 사례](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control)에서 해당 기능에 대해 자세히 알아보세요.

Microsoft Defender for Kubernetes를 사용하도록 설정하는 경우 Defender for Cloud는 더 많은 컨테이너 보안 기능을 제공합니다. 구체적으로는 다음과 같습니다.

- 컨테이너 레지스트리에 대한 Microsoft [Defender를](defender-for-container-registries-introduction.md) 사용하여 컨테이너 레지스트리에서 취약성 검사
- K8s 클러스터에 대한 실시간 위협 탐지 경고 받기 [Microsoft Defender for Kubernetes](defender-for-kubernetes-introduction.md)

> [!TIP]
> Kubernetes 클러스터 및 노드에 대해 표시될 수 있는 *모든* 보안 권장 사항의 목록은 권장 사항 참조 표의 [컴퓨팅 섹션](recommendations-reference.md#recs-compute)을 참조하세요.



## <a name="availability"></a>가용성

| 양상                          | 세부 정보                                                                                                                                      |
|---------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------|
| 릴리스 상태:                  | GA(일반 공급)                                                                                                                    |
| 가격 책정:                        | Free                                                                                                                                         |
| 필요한 역할 및 권한: | 할당을 편집하기 위한 **소유자** 또는 **보안 관리자**<br>권장 사항을 보기 위한 **읽기 권한자**                                              |
| 환경 요구 사항:       | Kubernetes v1.14 이상이 필요합니다.<br>클러스터에 PodSecurityPolicy 리소스(이전 PSP 모델)가 없습니다.<br>Windows 노드는 지원되지 않습니다. |
| 클라우드:                         | :::image type="icon" source="./media/icons/yes-icon.png"::: 상용 클라우드<br>:::image type="icon" source="./media/icons/yes-icon.png"::: National(Azure Government, Azure 중국 21Vianet) |
|                                 |                                                                                                                                              |


## <a name="set-up-your-workload-protection"></a>워크로드 보호 설정

Microsoft Defender for Cloud에는 **Kubernetes용 Azure Policy 추가** 기능 을 설치할 때 사용할 수 있는 권장 사항 번들이 포함되어 있습니다.

### <a name="step-1-deploy-the-add-on"></a>1단계: 추가 기능 배포

권장 사항을 구성하려면 **Kubernetes용 Azure Policy 추가 기능** 을 설치합니다. 

- [Log Analytics 에이전트 및 확장의 자동 프로비저닝 사용](enable-data-collection.md#auto-provision-mma)에 설명된 대로 이 추가 기능을 자동 배포할 수 있습니다. 추가 기능에 대한 자동 프로비저닝이 "켜기"로 설정하면 모든 기존 및 향후 클러스터(추가 기능 설치 요구 사항 충족)에서 확장이 기본적으로 사용하도록 설정됩니다.

    :::image type="content" source="media/defender-for-kubernetes-usage/policy-add-on-auto-provision.png" alt-text="Defender for Cloud의 자동 프로비저닝 도구를 사용하여 Kubernetes용 정책 추가 기능 설치":::

- 추가 기능을 수동으로 배포하려면:

    1. 권장 사항 페이지에서 "**클러스터에 Kubernetes용 Azure Policy 추가 기능을 설치하고 사용하도록 설정해야 함**"이라는 권장 사항을 검색합니다. 

        :::image type="content" source="./media/defender-for-kubernetes-usage/recommendation-to-install-policy-add-on-for-kubernetes.png" alt-text="권장 사항 **클러스터에 Kubernetes용 Azure Policy 추가 기능을 설치하고 사용하도록 설정해야 함**":::

        > [!TIP]
        > 이 권장 사항은 다섯 가지 보안 제어에 포함되며, 다음 단계에서 어떤 것을 선택해도 상관없습니다.

    1. 보안 제어에서 권장 사항을 선택하여 추가 기능을 설치할 수 있는 리소스를 확인합니다.
    1. 관련 클러스터를 선택하고 **수정** 합니다.

        :::image type="content" source="./media/defender-for-kubernetes-usage/recommendation-to-install-policy-add-on-for-kubernetes-details.png" alt-text="**클러스터에 Kubernetes용 Azure Policy 추가 기능을 설치하고 사용하도록 설정해야 함**에 대한 권장 사항 세부 정보 페이지":::

### <a name="step-2-view-and-configure-the-bundle-of-recommendations"></a>02단계: 권장 사항 번들 보기 및 구성

1. 추가 기능 설치가 완료된 후 약 30분 후에 Defender for Cloud는 다음과 같은 권장 사항에 대한 클러스터의 상태를 표시합니다. 각 권장 사항은 다음과 같이 관련 보안 제어에 있습니다.

    > [!NOTE]
    > 추가 기능 을 처음으로 설치하는 경우 이러한 권장 사항은 권장 사항 목록에 새로 추가된 항목으로 표시됩니다. 

    > [!TIP]
    > 일부 권장 사항에는 효과적으로 사용하기 위해 Azure Policy를 통해 사용자 지정해야 하는 매개 변수가 있습니다. 예를 들어 **신뢰할 수 있는 레지스트리의 컨테이너 이미지만 배포해야 함** 이라는 권장 사항의 이점을 활용하려면 신뢰할 수 있는 레지스트리를 정의해야 합니다.
    > 
    > 구성이 필요한 권장 사항에 필요한 매개 변수를 입력하지 않으면 워크로드가 비정상으로 표시됩니다.

    | 권장 사항 이름                                                         | 보안 컨트롤                         | 구성이 필요함 |
    |-----------------------------------------------------------------------------|------------------------------------------|------------------------|
    | 컨테이너는 허용되는 포트에서만 수신 대기해야 함                              | 무단 네트워크 액세스 제한     | **예**                |
    | 서비스는 허용되는 포트에서만 수신 대기해야 함                                | 무단 네트워크 액세스 제한     | **예**                |
    | 호스트 네트워킹 및 포트 사용을 제한해야 함                     | 무단 네트워크 액세스 제한     | **예**                |
    | 컨테이너 AppArmor 프로필의 재정의 또는 비활성화를 제한해야 함 | 보안 구성 수정        | **예**                |
    | 컨테이너 이미지는 신뢰할 수 있는 레지스트리에서만 배포해야 함            | 취약성 해결                | **예**                |
    | 최소 권한 Linux 기능을 컨테이너에 적용해야 함       | 액세스 및 사용 권한 관리            | **예**                |
    | Pod HostPath 볼륨 탑재 사용은 알려진 목록으로 제한되어야 함    | 액세스 및 사용 권한 관리            | **예**                |
    | 권한 있는 컨테이너를 피해야 함                                     | 액세스 및 사용 권한 관리            | 예                     |
    | 권한 상승을 포함하는 컨테이너를 사용하지 않아야 함                       | 액세스 및 사용 권한 관리            | 예                     |
    | Kubernetes 클러스터는 자동 탑재 API 자격 증명을 사용하지 않도록 설정해야 함             | 액세스 및 사용 권한 관리            | 예                     |
    | 변경 불가능한(읽기 전용) 루트 파일 시스템을 컨테이너에 적용해야 함     | 액세스 및 사용 권한 관리            | 예                     |
    | 권한 상승을 포함하는 컨테이너를 사용하지 않아야 함                       | 액세스 및 사용 권한 관리            | 예                     |
    | 컨테이너를 루트 사용자로 실행하지 않아야 함                           | 액세스 및 사용 권한 관리            | 예                     |
    | 중요한 호스트 네임스페이스를 공유하는 컨테이너를 사용하지 않아야 함              | 액세스 및 사용 권한 관리            | 예                     |
    | 컨테이너 CPU 및 메모리 한도를 적용해야 함                          | DDoS 공격으로부터 애플리케이션 보호 | 예                     |
    | Kubernetes 클러스터는 HTTPS를 통해서만 액세스할 수 있어야 함                    | 전송 중인 데이터 암호화                  | No                     |
    | Kubernetes 클러스터는 기본 네임스페이스를 사용하지 않아야 함                    | 보안 모범 사례 구현        | No                     |
    ||||


1. 사용자 지정해야 하는 매개 변수가 있는 권장 사항의 경우 다음과 같이 매개 변수를 설정합니다.

    1. 클라우드 용 Defender의 메뉴에서 **보안 정책** 을 선택 합니다.
    1. 관련 구독을 선택합니다.
    1. **클라우드 기본 정책에 대 한 Defender** 섹션에서 **유효 정책 보기** 를 선택 합니다.
    1. 업데이트하는 범위에 대한 기본 정책을 선택합니다.
    1. **매개 변수** 탭을 열고 필요에 따라 값을 수정합니다.

        :::image type="content" source="media/kubernetes-workload-protections/containers-parameter-requires-configuration.png" alt-text="Kubernetes 워크로드 보호 번들의 권장 사항 중 하나에 대한 매개 변수 수정.":::

    1. **검토 후 저장** 을 선택합니다.
    1. **저장** 을 선택합니다.


1. 권장 사항을 적용하려면 다음을 수행합니다. 

    1. 권장 사항 세부 정보 페이지를 열고 **거부** 를 선택합니다.

        :::image type="content" source="./media/defender-for-kubernetes-usage/enforce-workload-protection-example.png" alt-text="Azure Policy 매개 변수에 대한 거부 옵션.":::

        그러면 범위를 설정하는 창이 열립니다. 

    1. 범위를 설정했으면 **거부로 변경** 을 선택합니다.

1. 클러스터에 적용되는 권장 사항을 확인하려면 다음을 수행합니다.

    1. 클라우드의 [자산 재고](asset-inventory.md) 페이지에 대해 Defender를 열고 리소스 유형 필터를 사용 하 여 **서비스를 Kubernetes**.

    1. 조사할 클러스터를 선택하고 클러스터에 사용 가능한 권장 사항을 검토합니다. 

1. 워크로드 보호 집합에서 권장 사항을 볼 때 클러스터와 함께 나열된 영향을 받는 Pod("Kubernetes 구성 요소") 수가 표시됩니다. 특정 Pod 목록을 보려면 클러스터를 선택하고 **작업 수행** 을 선택합니다.

    :::image type="content" source="./media/defender-for-kubernetes-usage/view-affected-pods-for-recommendation.gif" alt-text="K8s 권장 사항에 대해 영향을 받는 Pod 보기."::: 

1. 적용을 테스트하려면 아래의 두 Kubernetes 배포를 사용합니다.

    - 하나는 워크로드 보호 권장 사항의 번들을 준수하는 정상적인 배포에 대한 것입니다.
    - 다른 하나는 권장 사항을 준수하지 *않는* 비정상적인 배포에 대한 것입니다.

    예제 .yaml 파일을 있는 그대로 배포하거나 고유한 워크로드를 수정하기 위한 참조로 사용합니다(8단계).  


## <a name="healthy-deployment-example-yaml-file"></a>정상적인 배포 예제. yaml 파일

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-healthy-deployment
  labels:
    app: redis
spec:
  replicas: 3
  selector:
    matchLabels:
      app: redis
  template:
    metadata:
      labels:
        app: redis
      annotations:
        container.apparmor.security.beta.kubernetes.io/redis: runtime/default
    spec:
      containers:
      - name: redis
        image: <customer-registry>.azurecr.io/redis:latest
        ports:
        - containerPort: 80
        resources:
          limits:
            cpu: 100m
            memory: 250Mi
        securityContext:
          privileged: false
          readOnlyRootFilesystem: true
          allowPrivilegeEscalation: false
          runAsNonRoot: true
          runAsUser: 1000
---
apiVersion: v1
kind: Service
metadata:
  name: redis-healthy-service
spec:
  type: LoadBalancer
  selector:
    app: redis
  ports:
  - port: 80
    targetPort: 80
```

## <a name="unhealthy-deployment-example-yaml-file"></a>비정상적인 배포 예제. yaml 파일

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-unhealthy-deployment
  labels:
    app: redis
spec:
  replicas: 3
  selector:
    matchLabels:
      app: redis
  template:
    metadata:      
      labels:
        app: redis
    spec:
      hostNetwork: true
      hostPID: true 
      hostIPC: true
      containers:
      - name: redis
        image: redis:latest
        ports:
        - containerPort: 9001
          hostPort: 9001
        securityContext:
          privileged: true
          readOnlyRootFilesystem: false
          allowPrivilegeEscalation: true
          runAsUser: 0
          capabilities:
            add:
              - NET_ADMIN
        volumeMounts:
        - mountPath: /test-pd
          name: test-volume
          readOnly: true
      volumes:
      - name: test-volume
        hostPath:
          # directory location on host
          path: /tmp
---
apiVersion: v1
kind: Service
metadata:
  name: redis-unhealthy-service
spec:
  type: LoadBalancer
  selector:
    app: redis
  ports:
  - port: 6001
    targetPort: 9001
```



## <a name="next-steps"></a>다음 단계

이 문서에서는 Kubernetes 워크로드 보호를 구성하는 방법을 살펴보았습니다. 

기타 관련 자료는 다음 페이지를 참조하세요. 

- [계산을 위한 클라우드 권장 사항에 대 한 Defender](recommendations-reference.md#recs-compute)
- [AKS 클러스터 수준에 대한 경고](alerts-reference.md#alerts-k8scluster)
- [컨테이너 호스트 수준에 대한 경고](alerts-reference.md#alerts-containerhost)
