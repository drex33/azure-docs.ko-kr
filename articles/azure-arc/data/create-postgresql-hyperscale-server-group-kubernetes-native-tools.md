---
title: Kubernetes 도구를 사용하여 PostgreSQL 하이퍼스케일 서버 그룹 만들기
description: Kubernetes 도구를 사용하여 PostgreSQL 하이퍼스케일 서버 그룹 만들기
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 20214832a79dcf22bd14f6c6594f37a7036669fe
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131558861"
---
# <a name="create-a-postgresql-hyperscale-server-group-using-kubernetes-tools"></a>Kubernetes 도구를 사용하여 PostgreSQL 하이퍼스케일 서버 그룹 만들기

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>필수 구성 요소

[Azure Arc 데이터 컨트롤러](./create-data-controller.md)를 미리 만들어야 합니다.

Kubernetes 도구를 사용하여 PostgreSQL 하이퍼스케일 서버 그룹을 만들려면 Kubernetes 도구가 설치되어 있어야 합니다.  이 문서의 예제에서는 `kubectl`을 사용하지만 이와 마찬가지로, 관련 도구와 Kubernetes yaml/json에 익숙하다면 Kubernetes 대시보드, `oc` 또는 `helm` 등의 다른 Kubernetes 도구를 사용할 수도 있습니다.

[kubectl 도구 설치](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

## <a name="overview"></a>개요

PostgreSQL 하이퍼스케일 서버 그룹을 만들려면 Postgres 관리자 로그인 및 암호를 안전하게 저장하는 Kubernetes 비밀을 만들고 _postgresqls_ 사용자 지정 리소스 정의를 기반으로 하는 PostgreSQL 하이퍼스케일 서버 그룹 사용자 지정 리소스를 만들어야 합니다.

## <a name="create-a-yaml-file"></a>Yaml 파일 만들기

[템플릿 yaml](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/postgresql.yaml) 파일을 시작 지점으로 사용하여 고유한 사용자 지정 PostgreSQL 하이퍼스케일 서버 그룹 yaml 파일을 만들 수 있습니다.  이 파일을 로컬 컴퓨터에 다운로드하여 텍스트 편집기에서 엽니다.  [VS Code](https://code.visualstudio.com/download)처럼 yaml 파일에 대하여 구문 강조 표시와 린팅을 지원하는 텍스트 편집기를 사용하는 것이 좋습니다.

**yaml 파일 예:**

```yaml
apiVersion: v1
data:
  password: <your base64 encoded password>
kind: Secret
metadata:
  name: pg1-login-secret
type: Opaque
---
apiVersion: arcdata.microsoft.com/v1beta1
kind: postgresql
metadata:
  name: pg1
spec:
  engine:
    version: 12
    extensions:
    - name: citus
  scale:
    workers: 3
  scheduling:
    default:
      resources:
        limits:
          cpu: "4"
          memory: 4Gi
        requests:
          cpu: "1"
          memory: 2Gi
  services:
    primary:
      type: LoadBalancer # Modify service type based on your Kubernetes environment
  storage:
    backups:
      volumes:
      - className: default # Use default configured storage class or modify storage class based on your Kubernetes environment
        size: 5Gi
    data:
      volumes:
      - className: default # Use default configured storage class or modify storage class based on your Kubernetes environment
        size: 5Gi
    logs:
      volumes:
      - className: default # Use default configured storage class or modify storage class based on your Kubernetes environment
        size: 5Gi
```

### <a name="customizing-the-login-and-password"></a>로그인 및 암호를 사용자 지정합니다.
Kubernetes 암호는 base64로 인코딩된 문자열로 저장되며, 하나는 사용자 이름에, 하나는 암호에 사용됩니다.  관리자 로그인 및 암호를 base64로 인코딩하고 및 의 자리 표시자 위치에 배치해야 `data.password` `data.username` 합니다.  템플릿에서 제공하는 `<` 및 `>` 기호는 포함하지 마세요.

플랫폼에 따라 온라인 도구를 사용하여 원하는 사용자 이름과 암호를 base64로 인코딩하거나 기본 제공 CLI 도구를 사용할 수 있습니다.

PowerShell

```console
[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('<your string to encode here>'))

#Example
#[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('example'))

```

Linux/macOS

```console
echo -n '<your string to encode here>' | base64

#Example
# echo -n 'example' | base64
```

### <a name="customizing-the-name"></a>이름 사용자 지정

템플릿에는 `pg1` name 특성에 대한 값이 있습니다.  이 값을 변경할 수 있지만 DNS 명명 표준을 따르는 문자여야 합니다. 이름을 변경하는 경우 일치하도록 비밀의 이름을 변경합니다.  예를 들어 PostgreSQL 하이퍼스케일 서버 그룹의 이름을 로 변경하는 경우 비밀 이름을 에서 로 `pg2` 변경해야 합니다. `pg1-login-secret``pg2-login-secret`

### <a name="customizing-the-engine-version"></a>엔진 버전 사용자 지정

`kind` 특성을 편집하여 엔진 버전을 postgresql-11 또는 postgresql-12로 변경할 수 있습니다.

### <a name="customizing-the-resource-requirements"></a>리소스 요구 사항 사용자 지정

필요에 따라 RAM 및 코어 제한과 요청 등의 리소스 요구 사항을 변경할 수 있습니다.  

> [!NOTE]
> [Kubernetes 리소스 거버넌스](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#resource-units-in-kubernetes)에 대하여 자세히 알아볼 수 있습니다.

리소스 제한 및 요청에 대한 요구 사항:
- 청구를 위하여 코어 제한 값은 **필수** 입니다.
- 리소스 요청 및 제한의 나머지 부분은 선택 사항입니다.
- 지정하는 경우 코어 제한과 요청은 양의 정숫값이어야 합니다.
- 지정된 경우 코어 요청에 최소 1개의 코어가 필요합니다.
- 메모리 값 형식은 Kubernetes 표기법을 따릅니다.  

### <a name="customizing-service-type"></a>서비스 유형 사용자 지정

원하는 경우 서비스 유형을 NodePort로 변경할 수 있습니다.  그러면 임의의 포트 번호가 할당됩니다.

### <a name="customizing-storage"></a>스토리지 사용자 지정

사용자 환경에 맞게 스토리지의 스토리지 클래스를 사용자 지정할 수 있습니다.  사용할 수 있는 스토리지 클래스를 잘 모르는 경우 명령을 `kubectl get storageclass` 실행하여 해당 클래스를 봅니다.  템플릿의 기본값은 `default` 입니다.  이 값은 _기본값인_ 스토리지 클래스가 없다는 것을 의미하지 않는 라는 `default` 스토리지 클래스가 있음을 의미합니다.   필요에 따라 스토리지 크기를 변경할 수도 있습니다.  [스토리지 구성](./storage-configuration.md)에 대한 자세한 내용을 참조하세요.

## <a name="creating-the-postgresql-hyperscale-server-group"></a>PostgreSQL 하이퍼스케일 서버 그룹 만들기

이제 PostgreSQL 하이퍼스케일 서버 그룹 yaml 파일을 사용자 지정했으므로 다음 명령을 실행하여 PostgreSQL 하이퍼스케일 서버 그룹을 만들 수 있습니다.

```console
kubectl create -n <your target namespace> -f <path to your yaml file>

#Example
#kubectl create -n arc -f C:\arc-data-services\postgres.yaml
```


## <a name="monitoring-the-creation-status"></a>생성 상태 모니터링

PostgreSQL 하이퍼스케일 서버 그룹을 만드는 작업은 완료하는 데 몇 분 정도 걸립니다. 다음 명령을 사용하여 다른 터미널 창에서 진행률을 모니터링할 수 있습니다.

> [!NOTE]
>  아래 예제 명령은 이름이 인 및 `pg1` Kubernetes 네임스페이스라는 PostgreSQL 하이퍼스케일 서버 그룹을 만들었다고 `arc` 가정합니다.  다른 네임스페이스/PostgreSQL 하이퍼스케일 서버 그룹 이름을 사용한 경우 및 를 사용자 이름으로 바꿀 수 `arc` `pg1` 있습니다.

```console
kubectl get postgresqls/pg1 --namespace arc
```

```console
kubectl get pods --namespace arc
```

명령을 실행하여 특정 Pod의 생성 상태를 확인할 수도 `kubectl describe` 있습니다.  `describe`이 명령은 문제를 해결하는 데 특히 유용합니다. 예를 들면 다음과 같습니다.

```console
kubectl describe pod/<pod name> --namespace arc

#Example:
#kubectl describe pod/pg1-0 --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>만들기 문제 해결

만들기 관련 문제가 발생하는 경우 [문제 해결 가이드](troubleshoot-guide.md)를 참조하세요.
