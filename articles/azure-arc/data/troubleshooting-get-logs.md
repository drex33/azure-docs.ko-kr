---
title: Azure Arc 지원 데이터 서비스 문제를 해결하기 위한 로그 가져오기
description: Azure Arc 지원 데이터 서비스 문제를 해결하기 위해 데이터 컨트롤러에서 로그 파일을 가져오는 방법에 대해 알아봅니다.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 82152756b5caf5bfbe0301a14185d8ffed1d1afe
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535723"
---
# <a name="get-logs-to-troubleshoot-azure-arc-enabled-data-services"></a>Azure Arc 지원 데이터 서비스 문제를 해결하기 위한 로그 가져오기


## <a name="prerequisites"></a>사전 요구 사항

계속 진행하려면 다음 항목이 필요합니다.

* `arcdata` 확장을 사용하는 Azure CLI(`az`) 자세한 내용은 [Azure Arc 데이터 서비스 배포 및 관리를 위한 클라이언트 도구 설치](./install-client-tools.md)를 참조하세요.
* Azure Arc 지원 데이터 컨트롤러에 로그인하는 데 사용되는 관리자 계정입니다.

## <a name="get-log-files"></a>로그 파일 가져오기

문제 해결을 위해 모든 Pod 또는 특정 Pod에서 서비스 로그를 가져올 수 있습니다. 한 가지 방법은 `kubectl logs` 명령과 같은 표준 Kubernetes 도구를 사용하는 것입니다. 이 문서에서는 Azure(`az`) CLI `arcdata` 확장을 사용하며 이 도구는 모든 로그를 한 번에 쉽게 가져올 수 있습니다.

다음 명령을 실행하여 로그를 덤프합니다.

   ```azurecli
   az arcdata dc debug copy-logs --exclude-dumps --skip-compress
   ```

   예를 들면 다음과 같습니다.

   ```azurecli
   #az arcdata dc debug copy-logs --exclude-dumps --skip-compress
   ```

데이터 컨트롤러는 `logs`라는 하위 디렉터리에서 현재 작업 디렉터리에 로그 파일을 만듭니다. 

## <a name="options"></a>옵션

`az arcdata dc debug copy-logs` 명령은 출력을 관리하기 위해 다음 옵션을 제공합니다.

* `--target-folder` 매개 변수를 사용하여 로그 파일을 다른 디렉터리에 출력합니다.
* `--skip-compress` 매개 변수를 생략하여 파일을 압축합니다.
* `--exclude-dumps`를 생략하여 메모리 덤프를 트리거 및 포함합니다. Microsoft 지원에서 메모리 덤프를 요청하는 경우를 제외하면 이 메서드를 권장하지 않습니다. 메모리 덤프를 가져오려면 데이터 컨트롤러를 만들 때 데이터 컨트롤러 설정 `allowDumps`가 `true`로 설정되어야 합니다
* 특정 Pod(`--pod`) 또는 컨테이너(`--container`)에 대한 로그를 이름으로 수집하도록 필터링합니다.
* `--resource-kind` 및 `--resource-name` 매개 변수를 전달하여 특정 사용자 지정 리소스에 대한 로그를 수집하도록 필터링합니다. `resource-kind` 매개 변수 값은 사용자 지정 리소스 정의 이름 중 하나여야 합니다. `kubectl get customresourcedefinition` 명령을 사용하여 이러한 이름을 검색할 수 있습니다.

이러한 매개 변수를 사용하여 다음 예제에서 `<parameters>`를 바꿀 수 있습니다. 

```azurecli
az arcdata dc debug copy-logs --target-folder <desired folder> --exclude-dumps --skip-compress -resource-kind <custom resource definition name> --resource-name <resource name>
```

예를 들면 다음과 같습니다.

```console
#az arcdata dc debug copy-logs --target-folder C:\temp\logs --exclude-dumps --skip-compress --resource-kind postgresql-12 --resource-name pg1 
```

다음 폴더 계층 구조를 예로 들 수 있습니다. Pod 이름, 컨테이너, 컨테이너 내의 디렉터리 계층 구조를 기준으로 구성됩니다.

```output
<export directory>
├───debuglogs-arc-20200827-180403
│   ├───bootstrapper-vl8j2
│   │   └───bootstrapper
│   │       ├───apt
│   │       └───fsck
│   ├───control-j2dm5
│   │   ├───controller
│   │   │   └───controller
│   │   │       ├───2020-08-27
│   │   │       └───2020-08-28
│   │   └───fluentbit
│   │       ├───agent
│   │       ├───fluentbit
│   │       └───supervisor
│   │           └───log
│   ├───controldb-0
│   │   ├───fluentbit
│   │   │   ├───agent
│   │   │   ├───fluentbit
│   │   │   └───supervisor
│   │   │       └───log
│   │   └───mssql-server
│   │       ├───agent
│   │       ├───mssql
│   │       ├───mssql-server
│   │       └───supervisor
│   │           └───log
│   ├───controlwd-ln6j8
│   │   └───controlwatchdog
│   │       └───controlwatchdog
│   ├───logsdb-0
│   │   └───elasticsearch
│   │       ├───agent
│   │       ├───elasticsearch
│   │       ├───provisioner
│   │       └───supervisor
│   │           └───log
│   ├───logsui-7gg2d
│   │   └───kibana
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───kibana
│   │       └───supervisor
│   │           └───log
│   ├───metricsdb-0
│   │   └───influxdb
│   │       ├───agent
│   │       ├───influxdb
│   │       └───supervisor
│   │           └───log
│   ├───metricsdc-2f62t
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsdc-jznd2
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsdc-n5vnx
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsui-h748h
│   │   └───grafana
│   │       ├───agent
│   │       ├───grafana
│   │       └───supervisor
│   │           └───log
│   └───mgmtproxy-r5zxs
│       ├───fluentbit
│       │   ├───agent
│       │   ├───fluentbit
│       │   └───supervisor
│       │       └───log
│       └───service-proxy
│           ├───agent
│           ├───nginx
│           └───supervisor
│               └───log
└───debuglogs-kube-system-20200827-180431
    ├───coredns-8bbb65c89-kklt7
    │   └───coredns
    ├───coredns-8bbb65c89-z2vvr
    │   └───coredns
    ├───coredns-autoscaler-5585bf8c9f-g52nt
    │   └───autoscaler
    ├───kube-proxy-5c9s2
    │   └───kube-proxy
    ├───kube-proxy-h6x56
    │   └───kube-proxy
    ├───kube-proxy-nd2b7
    │   └───kube-proxy
    ├───metrics-server-5f54b8994-vpm5r
    │   └───metrics-server
    └───tunnelfront-db87f4cd8-5xwxv
        ├───tunnel-front
        │   ├───apt
        │   └───journal
        └───tunnel-probe
            ├───apt
            ├───journal
            └───openvpn
```

