---
title: Kubernetes 도구를 사용하여 SQL 관리형 인스턴스 만들기
description: Kubernetes 도구를 사용하여 SQL 관리형 인스턴스 만들기
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: dbc2f2efffcab9800deff27e42e4130061c531d7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566982"
---
# <a name="create-azure-sql-managed-instance-using-kubernetes-tools"></a>Kubernetes 도구를 사용하여 Azure SQL 관리형 인스턴스 만들기


## <a name="prerequisites"></a>필수 구성 요소

[Azure Arc 데이터 컨트롤러](./create-data-controller.md)는 이미 만들어 놓은 상태일 것입니다.

Kubernetes 도구를 사용하여 SQL 관리형 인스턴스를 만들려면 Kubernetes 도구가 설치되어 있어야 합니다.  본 문서의 예제에서는 `kubectl`을 사용할 것이지만, Kubernetes 대시보드, `oc` 또는 `helm`과 같은 Kubernetes 도구나 Kubernetes yaml/json에 익숙한 상태라면 비슷한 방식에서 이러한 Kubernetes 도구를 사용할 수도 있습니다.

[kubectl 도구 설치](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

## <a name="overview"></a>개요

SQL 관리형 인스턴스를 만들려면 Kubernetes 비밀을 만들어 시스템 관리자 로그인 및 암호를 안전하게 저장하여야 하고 sqlmanagedinstance 사용자 지정 리소스 정의에 따라 SQL 관리형 인스턴스 사용자 지정 리소스를 만들어야 합니다.

## <a name="create-a-yaml-file"></a>yaml 파일 만들기

[템플릿 yaml](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/sqlmi.yaml) 파일에서 시작하여 사용자 지정 SQL 관리형 인스턴스 yaml 파일을 만들 수 있습니다.  해당 파일을 로컬 컴퓨터에 다운로드하여 텍스트 편집기에서 엽니다.  [VS Code](https://code.visualstudio.com/download)처럼 yaml 파일에 대하여 구문 강조 표시와 린팅을 지원하는 텍스트 편집기를 사용하는 것이 좋습니다.

다음은 예제 yaml 파일입니다.

```yaml
apiVersion: v1
data:
  password: <your base64 encoded password>
  username: <your base64 encoded username>
kind: Secret
metadata:
  name: sql1-login-secret
type: Opaque
---
apiVersion: sql.arcdata.microsoft.com/v1
kind: SqlManagedInstance
metadata:
  name: sql1
  annotations:
    exampleannotation1: exampleannotationvalue1
    exampleannotation2: exampleannotationvalue2
  labels:
    examplelabel1: examplelabelvalue1
    examplelabel2: examplelabelvalue2
spec:
  scheduling:
    default:
      resources:
        limits:
          cpu: "2"
          memory: 4Gi
        requests:
          cpu: "1"
          memory: 2Gi
  services:
    primary:
      type: LoadBalancer
  storage:
    backups:
      volumes:
      - className: default # Use default configured storage class or modify storage class based on your Kubernetes environment
        size: 5Gi
    data:
      volumes:
      - className: default # Use default configured storage class or modify storage class based on your Kubernetes environment
        size: 5Gi
    datalogs:
      volumes:
      - className: default # Use default configured storage class or modify storage class based on your Kubernetes environment
        size: 5Gi
    logs:
      volumes:
      - className: default # Use default configured storage class or modify storage class based on your Kubernetes environment
        size: 5Gi
```

### <a name="customizing-the-login-and-password"></a>로그인 및 암호 사용자 지정

Kubernetes 비밀은 Base64로 인코딩한 문자열로, 사용자 이름을 위하여 하나, 암호를 위하여 하나가 저장됩니다.  시스템 관리자 로그인 및 암호를 Base64로 인코딩하여 `data.password` 및 `data.username`의 자리 표시자에 배치하여야 합니다.  템플릿에 제공된 `<` 및 `>` 기호는 포함하지 마세요.

> [!NOTE]
> 최적의 보안을 위하여 ‘sa’ 값은 로그인에 사용할 수 없습니다.
> [암호 복잡성 정책](/sql/relational-databases/security/password-policy#password-complexity)을 따르세요.

원하는 사용자 이름과 암호를 Base64로 인코딩하기 위하여 온라인 도구를 사용하거나, 플랫폼에 따라 기본 제공되는 CLI 도구를 사용할 수 있습니다.

PowerShell

```console
[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes('<your string to encode here>'))

#Example
#[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes('example'))
```

Linux/macOS

```console
echo -n '<your string to encode here>' | base64

#Example
# echo -n 'example' | base64
```

### <a name="customizing-the-name"></a>이름 사용자 지정

템플릿에는 이름 특성을 위하여 ‘sql1’ 값이 있습니다.  해당 값을 변경할 수 있지만 변경할 값은 반드시 DNS 명명 표준을 따르는 문자여야 합니다.  이에 일치하는 암호의 이름도 변경하여야 합니다.  예를 들어 SQL 관리형 인스턴스 이름을 ‘sql2’와 같이 변경한다면, 그 비밀의 이름도 ‘sql1-login-secret’에서 ‘sql2-login-secret’으로 변경하여야 합니다.

### <a name="customizing-the-resource-requirements"></a>리소스 요구 사항 사용자 지정

필요에 따라 RAM 및 코어 제한과 요청 등의 리소스 요구 사항을 변경할 수 있습니다.  

> [!NOTE]
> [Kubernetes 리소스 거버넌스](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#resource-units-in-kubernetes)에 대하여 자세히 알아볼 수 있습니다.

리소스 제한 및 요청에 대한 요구 사항:
- 청구를 위하여 코어 제한 값은 **필수** 입니다.
- 리소스 요청 및 제한의 나머지 부분은 선택 사항입니다.
- 지정하는 경우 코어 제한과 요청은 양의 정숫값이어야 합니다.
- 지정하는 경우 코어 요청에는 최소 1코어가 필요합니다.
- 메모리 값 형식은 Kubernetes 표기법을 따릅니다.  
- 지정하는 경우 메모리 요청에는 최소 2Gi가 필요합니다.
- 일반적인 가이드라인에 따라, 프로덕션 사용 사례의 경우 각각의 코어마다 RAM에 4GB가 있어야 합니다.

### <a name="customizing-service-type"></a>서비스 유형 사용자 지정

원하는 경우 서비스 유형을 NodePort로 변경할 수 있습니다.  그러면 임의의 포트 번호가 할당됩니다.

### <a name="customizing-storage"></a>스토리지 사용자 지정

사용자 환경에 맞게 스토리지의 스토리지 클래스를 사용자 지정할 수 있습니다.  사용할 수 있는 스토리지 클래스를 잘 모르는 경우 `kubectl get storageclass` 명령을 실행하여 해당 스토리지 클래스를 볼 수 있습니다.  템플릿의 기본값은 ‘default’입니다.  ‘default’_라는_ 스토리지 클래스가 있다는 의미이지 _기본_ 스토리지 클래스가 있다는 의미가 아닙니다.  필요에 따라 스토리지 크기를 변경할 수도 있습니다.  [스토리지 구성](./storage-configuration.md)에 대한 자세한 내용을 참조하세요.

## <a name="creating-the-sql-managed-instance"></a>SQL 관리형 인스턴스 만들기

이제 SQL 관리형 인스턴스 yaml 파일을 사용자 지정하였으므로, 다음 명령을 실행하여 SQL 관리형 인스턴스를 만들 수 있습니다.

```console
kubectl create -n <your target namespace> -f <path to your yaml file>

#Example
#kubectl create -n arc -f C:\arc-data-services\sqlmi.yaml
```

## <a name="monitoring-the-creation-status"></a>생성 상태 모니터링

SQL 관리형 인스턴스 생성을 완료하는 데는 몇 분 정도가 소요됩니다. 다음 명령을 사용하여 다른 터미널 창에서 진행률을 모니터링할 수 있습니다.

> [!NOTE]
>  아래의 예제 명령은 ‘sql1’이라는 이름의 SQL 관리형 인스턴스와 ‘arc’라는 이름의 Kubernetes 네임스페이스를 만들었다고 가정합니다.  다른 네임스페이스/SQL 관리형 인스턴스 이름을 사용하였다면, 그 이름들을 ‘arc’와 ‘sqlmi’ 대신 사용할 수 있습니다.

```console
kubectl get sqlmi/sql1 --namespace arc
```

```console
kubectl get pods --namespace arc
```

아래와 같은 명령을 실행하여 특정 Pod의 생성 상태를 확인할 수도 있습니다.  이 기능은 문제를 해결하는 데 특히 유용합니다.

```console
kubectl describe pod/<pod name> --namespace arc

#Example:
#kubectl describe pod/sql1-0 --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>생성 문제 해결

생성 관련 문제가 발생하는 경우 [문제 해결 가이드](troubleshoot-guide.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

[Azure Arc 지원 SQL Managed Instance에 연결](connect-managed-instance.md)
