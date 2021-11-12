---
title: Azure Red Hat OpenShift 클러스터에서 권한 있는 컨테이너 실행 | Microsoft Docs
description: 권한 있는 컨테이너를 실행하여 보안 및 규정 준수를 모니터링합니다.
author: makdaam
ms.author: b-lejaku
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 12/05/2019
keywords: aro, openshift, aquasec, twistlock, red hat
ms.openlocfilehash: 167a031098feb7764b205b81bcf63f68422be10e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132308208"
---
# <a name="run-privileged-containers-in-an-azure-red-hat-openshift-cluster"></a>Azure Red Hat OpenShift 클러스터에서 권한 있는 컨테이너 실행

> [!IMPORTANT]
> Azure Red Hat OpenShift 3.11은 2022년 6월 30일부터 사용이 중지됩니다. 새 Azure Red Hat OpenShift 3.11 클러스터 만들기 지원은 2020년 11월 30일까지 계속됩니다. 사용 중지 후에는 보안 취약성을 방지하기 위해 남아 있는 Azure Red Hat OpenShift 3.11 클러스터가 종료됩니다.
> 
> 이 가이드를 따라 [Azure Red Hat OpenShift 4 클러스터를 만듭니다](tutorial-create-cluster.md).
> 궁금한 점은 [Microsoft에 문의](mailto:arofeedback@microsoft.com)하세요.

Azure Red Hat OpenShift 클러스터에서는 임의의 권한 있는 컨테이너를 실행할 수 없습니다.
두 보안 모니터링 및 규정 준수 솔루션은 ARO 클러스터에서 실행할 수 있습니다.
이 문서에서는 보안 제품 공급 업체의 일반 OpenShift 배포 설명서와의 차이점을 설명합니다.


공급 업체의 지침을 수행하기 전에 다음 지침을 참조하세요.
아래 제품별 단계의 섹션 제목은 공급 업체의 설명서에 있는 섹션 제목에 직접 참조합니다.

## <a name="before-you-begin"></a>시작하기 전에

대부분의 보안 제품에 대한 설명서에서는 cluster-admin 권한이 있다고 가정합니다.
Azure Red Hat OpenShift에서는 고객 관리자에게 모든 권한이 있는 것이 아닙니다. 클러스터 전체 리소스를 수정하는 데 필요한 권한은 제한됩니다.

먼저 `oc get scc`를 실행하여 사용자가 고객 관리자로 클러스터에 로그인했는지 확인합니다. 고객 관리자 그룹의 구성원인 모든 사용자는 클러스터에 대한 SCC(보안 컨텍스트 제약 조건)를 볼 수 있는 권한이 있습니다.

다음으로 `oc` 이진 버전이 `3.11.154`인지 확인합니다.
```
oc version
oc v3.11.154
kubernetes v1.11.0+d4cacc0
features: Basic-Auth GSSAPI Kerberos SPNEGO

Server https://openshift.aqua-test.osadev.cloud:443
openshift v3.11.154
kubernetes v1.11.0+d4cacc0
```

## <a name="product-specific-steps-for-aqua-security"></a>Aqua Security용 제품별 단계
수정하려는 기본 지침은 [Aqua Security 배포 설명서](https://docs.aquasec.com/docs/openshift-red-hat)에서 찾을 수 있습니다. 여기에 나와 있는 단계는 Aqua 배포 설명서와 연계하여 실행됩니다.

첫 번째 단계는 업데이트할 필수 SCC에 주석을 추가하는 것입니다. 이러한 주석은 클러스터의 동기화 Pod가 변경 내용을 해당 SCC로 되돌리지 못하게 합니다.

```
oc annotate scc hostaccess openshift.io/reconcile-protect=true
oc annotate scc privileged openshift.io/reconcile-protect=true
```

### <a name="step-1-prepare-prerequisites"></a>1단계: 필수 구성 요소 준비
cluster-admin 역할 대신 ARO 고객 관리자로 클러스터에 로그인해야 합니다.

프로젝트 및 서비스 계정을 만듭니다.
```
oc new-project aqua-security
oc create serviceaccount aqua-account -n aqua-security
```

cluster-reader 역할을 할당하는 대신 다음 명령을 사용하여 customer-admin-cluster 역할을 aqua-account에 할당합니다.
```
oc adm policy add-cluster-role-to-user customer-admin-cluster system:serviceaccount:aqua-security:aqua-account
oc adm policy add-scc-to-user privileged system:serviceaccount:aqua-security:aqua-account
oc adm policy add-scc-to-user hostaccess system:serviceaccount:aqua-security:aqua-account
```

1단계의 나머지 지침에 따라 계속합니다.  이러한 지침은 Aqua 레지스트리의 비밀을 설정하는 방법을 설명합니다.

### <a name="step-2-deploy-the-aqua-server-database-and-gateway"></a>2단계: Aqua 서버, 데이터베이스 및 게이트웨이 배포
Aqua 문서에 설명된 단계에 따라 aqua-console.yaml을 설치합니다.

제공된 `aqua-console.yaml`을 수정합니다.  맨 위의 `kind: ClusterRole` 및 `kind: ClusterRoleBinding`이라는 두 개체를 제거합니다.  지금은 고객 관리자에게 `ClusterRole` 및 `ClusterRoleBinding` 개체를 수정할 수 있는 권한이 없기 때문에 이러한 리소스가 생성되지 않습니다.

두 번째 수정은 `aqua-console.yaml`의 `kind: Route` 부분에 대한 것입니다. `aqua-console.yaml` 파일의 `kind: Route` 개체를 다음 yaml으로 바꿉니다.
```
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  labels:
    app: aqua-web
  name: aqua-web
  namespace: aqua-security
spec:
  port:
    targetPort: aqua-web
  tls:
    insecureEdgeTerminationPolicy: Redirect
    termination: edge
  to:
    kind: Service
    name: aqua-web
    weight: 100
  wildcardPolicy: None
```

나머지 지침을 따릅니다.

### <a name="step-3-login-to-the-aqua-server"></a>3단계: Aqua 서버에 로그인
이 섹션은 어떤 방식으로든 수정되지 않습니다.  Aqua 문서를 따르세요.

다음 명령을 사용하여 Aqua Console 주소를 가져옵니다.
```
oc get route aqua-web -n aqua-security
```

### <a name="step-4-deploy-aqua-enforcers"></a>4단계: Aqua Enforcer 배포
Enforcer를 배포할 때 다음 필드를 설정합니다.

| 필드          | 값         |
| -------------- | ------------- |
| 오케스트레이터   | OpenShift     |
| ServiceAccount | aqua-account  |
| 프로젝트        | aqua-security |

## <a name="product-specific-steps-for-prisma-cloud--twistlock"></a>Prisma Cloud/Twistlock용 제품별 단계

수정하려는 기본 지침은 [Prisma Cloud 배포 설명서](https://docs.paloaltonetworks.com/prisma/prisma-cloud/19-11/prisma-cloud-compute-edition-admin/install/install_openshift.html)에서 확인할 수 있습니다.

'Prisma Cloud 설치' 및 'Prisma Cloud 소프트웨어 다운로드' 섹션에 설명된 대로 `twistcli` 도구를 설치하여 시작합니다.

새 OpenShift 프로젝트 만들기
```
oc new-project twistlock
```

선택 사항인 'Prisma Cloud 이미지를 프라이빗 레지스트리로 푸시' 섹션을 건너뜁니다. Azure Red Hat OpenShift에서는 작동하지 않습니다. 대신 온라인 레지스트리를 사용합니다.

아래에 설명된 수정 사항을 적용할 때 공식 설명서를 따를 수 있습니다.
'콘솔 설치' 섹션부터 시작합니다.

### <a name="install-console"></a>콘솔 설치

2단계의 `oc create -f twistlock_console.yaml`에서 네임스페이스를 만들 때 오류가 발생합니다.
이 오류는 무시할 수 있습니다. 네임스페이스는 앞서 `oc new-project` 명령을 사용하여 만들었습니다.

스토리지 유형으로 `azure-disk`를 사용합니다.

### <a name="create-an-external-route-to-console"></a>콘솔에 대한 외부 경로 만들기

설명서를 따르거나 oc 명령을 선호하는 경우 아래 지침을 따를 수 있습니다.
다음 경로 정의를 컴퓨터의 twistlock_route.yam이라는 파일에 복사합니다.
```
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  labels:
    name: console
  name: twistlock-console
  namespace: twistlock
spec:
  port:
    targetPort: mgmt-http
  tls:
    insecureEdgeTerminationPolicy: Redirect
    termination: edge
  to:
    kind: Service
    name: twistlock-console
    weight: 100
  wildcardPolicy: None
```
그리고 다음을 실행합니다.
```
oc create -f twistlock_route.yaml
```

`oc get route twistlock-console -n twistlock` 명령을 사용하여 Twistlock 콘솔에 할당된 URL을 가져올 수 있습니다.

### <a name="configure-console"></a>콘솔 구성

Twistlock 설명서를 따릅니다.

### <a name="install-defender-for-cloud"></a>Defender for Cloud 설치

2단계의 `oc create -f defender.yaml`에서 클러스터 역할 및 클러스터 역할 바인딩을 만들 때 오류가 발생합니다.
이런 예외는 무시할 수 있습니다.

Defender는 컴퓨팅 노드에만 배포됩니다. 노드 선택기를 사용하여 제한할 필요가 없습니다.
