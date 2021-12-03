---
title: 컨테이너 인스턴스에서 준비 상태 프로브 설정
description: Azure Container Instances의 컨테이너가 준비된 경우에만 요청을 수신하도록 프로브를 구성하는 방법을 알아봅니다.
ms.topic: article
ms.date: 07/02/2020
ms.openlocfilehash: 7cbf120364b85595cad9a72faa77f8ef55164e2e
ms.sourcegitcommit: 5b25f76d0fd0ffb6784a2afab808fa55b3eac07b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2021
ms.locfileid: "133517094"
---
# <a name="configure-readiness-probes"></a>준비 상태 프로브 구성

트래픽 서비스를 제공하는 컨테이너화된 애플리케이션의 경우 컨테이너가 수신 요청을 처리할 준비가 되었는지 확인하고 싶을 수 있습니다. Azure Container Instances에서는 준비 상태 프로브에 특정 조건에서는 컨테이너에 액세스할 수 없도록 하는 구성을 포함하도록 지원합니다. 이 준비 상태 프로브는 [Kubernetes 준비 상태 프로브](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)와 유사하게 작동합니다. 예를 들어 컨테이너 응용 프로그램이 시작 하는 동안 대량 데이터 집합을 로드 해야 할 수 있으며이 시간 동안 요청을 수신 하지 않으려고 합니다.

이 문서에서는 프로브가 성공하는 경우에만 컨테이너가 트래픽을 수신하도록 하는 준비 상태 프로브가 포함된 컨테이너 그룹을 배포하는 방법을 설명합니다.

Azure Container Instances에서는 비정상 컨테이너가 자동으로 다시 시작하도록 구성할 수 있는 [활동성 프로브](container-instances-liveness-probe.md)도 지원합니다.

> [!NOTE]
> 현재 가상 네트워크에 배포된 컨테이너 그룹에서는 준비 상태 프로브를 사용할 수 없습니다.

## <a name="yaml-configuration"></a>YAML 구성

예를 들어 준비 상태 프로브가 포함된 다음 코드 조각을 사용하여 `readiness-probe.yaml` 파일을 만듭니다. 이 파일은 작은 웹앱을 실행하는 컨테이너로 구성된 컨테이너 그룹을 정의합니다. 이 앱은 퍼블릭 `mcr.microsoft.com/azuredocs/aci-helloworld` 이미지에서 배포됩니다. 이 컨테이너화된 앱은 [Azure CLI를 사용하여 Azure에서 컨테이너 인스턴스 배포](container-instances-quickstart.md) 및 다른 빠른 시작에도 설명되어 있습니다.

```yaml
apiVersion: 2019-12-01
location: eastus
name: readinesstest
properties:
  containers:
  - name: mycontainer
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      command:
        - "/bin/sh"
        - "-c"
        - "node /usr/src/app/index.js & (sleep 240; touch /tmp/ready); wait"
      ports:
      - port: 80
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      readinessProbe:
        exec:
          command:
          - "cat"
          - "/tmp/ready"
        periodSeconds: 5
  osType: Linux
  restartPolicy: Always
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: '80'
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

### <a name="start-command"></a>시작 명령

이 배포에는 컨테이너가 처음 실행을 시작할 때 실행되는 시작 명령이 정의된 `command` 속성이 포함되어 있습니다. 이 속성에는 문자열 배열이 사용됩니다. 이 명령은 웹앱이 실행되지만 컨테이너가 준비되지 않은 시간을 시뮬레이션합니다. 

먼저 셸 세션을 시작하고 `node` 명령을 실행하여 웹앱을 시작합니다. 240초 동안 일시 중지하는 명령도 시작합니다. 그 후에는 `/tmp` 디렉터리에 `ready` 파일을 만듭니다.

```console
node /usr/src/app/index.js & (sleep 240; touch /tmp/ready); wait
```

### <a name="readiness-command"></a>준비 상태 명령

이 YAML 파일에서는 준비 상태 확인을 담당하는 `exec` 준비 상태 명령을 지원하도록 `readinessProbe`를 정의합니다. 이 예제 준비 상태 명령은 `/tmp` 디렉터리에 `ready` 파일이 있는지 테스트합니다.

`ready` 파일이 없으면 준비 상태 명령이 0이 아닌 값으로 종료됩니다. 컨테이너가 계속 실행되지만 컨테이너에 액세스할 수 없습니다. 종료 코드 0으로 이 명령이 종료되면 컨테이너에 액세스할 준비가 된 것입니다. 

`periodSeconds` 속성은 준비 상태 명령이 5초마다 실행되도록 지정합니다. 준비 상태 프로브는 컨테이너 그룹의 수명 기간 동안 실행됩니다.

## <a name="example-deployment"></a>예제 배포

다음 명령을 실행하여 앞에 있는 YAML 구성으로 컨테이너 그룹을 배포합니다.

```azurecli-interactive
az container create --resource-group myResourceGroup --file readiness-probe.yaml
```

## <a name="view-readiness-checks"></a>준비 상태 확인 보기

이 예제에서는 처음 240초 동안 준비 상태 명령이 `ready` 파일이 있는지 확인하는 데 실패합니다. 반환된 상태 코드는 컨테이너가 준비되지 않았음을 나타냅니다.

이러한 이벤트는 Azure Portal 또는 Azure CLI에서 볼 수 있습니다. 예를 들어, 포털에서는 준비 상태 명령이 실패하면 `Unhealthy` 유형의 이벤트가 트리거되는 것을 보여 줍니다. 

![포털 비정상 이벤트][portal-unhealthy]

## <a name="verify-container-readiness"></a>컨테이너 준비 상태 확인

컨테이너를 시작한 후 처음에는 컨테이너에 액세스할 수 없다는 것을 확인할 수 있습니다. 프로비저닝한 후 컨테이너 그룹의 IP 주소를 가져옵니다.

```azurecli
az container show --resource-group myResourceGroup --name readinesstest --query "ipAddress.ip" --out tsv
```

준비 상태 프로브가 실패할 때 사이트에 액세스해 봅니다.

```bash
wget <ipAddress>
```

처음에는 출력에서 사이트에 액세스할 수 없음을 보여 줍니다.
```
$ wget 192.0.2.1
--2019-10-15 16:46:02--  http://192.0.2.1/
Connecting to 192.0.2.1... connected.
HTTP request sent, awaiting response... 
```

240초 후 준비 상태 명령이 성공하면 컨테이너가 준비되었음을 나타내는 것입니다. 이제 `wget` 명령을 실행하면 명령이 성공합니다.

```
$ wget 192.0.2.1
--2019-10-15 16:46:02--  http://192.0.2.1/
Connecting to 192.0.2.1... connected.
HTTP request sent, awaiting response...200 OK
Length: 1663 (1.6K) [text/html]
Saving to: ‘index.html.1’

index.html.1                       100%[===============================================================>]   1.62K  --.-KB/s    in 0s      

2019-10-15 16:49:38 (113 MB/s) - ‘index.html.1’ saved [1663/1663] 
```

컨테이너가 준비된 상태인 경우에는 웹 브라우저로 IP 주소를 찾아 웹앱에 액세스할 수도 있습니다.

> [!NOTE]
> 준비 상태 프로브는 컨테이너 그룹의 수명 기간 동안 계속 실행됩니다. 나중에 준비 상태 명령이 실패하면 다시 컨테이너에 액세스할 수 없는 상태가 됩니다. 
> 

## <a name="next-steps"></a>다음 단계

준비 상태 프로브는 종속 컨테이너로 구성된 다중 컨테이너 그룹 관련 시나리오에 유용할 수 있습니다. 다중 컨테이너 시나리오에 대한 자세한 내용은 [Azure Container Instances의 컨테이너 그룹](container-instances-container-groups.md)을 참조하세요.

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-readiness-probe/readiness-probe-failed.png
