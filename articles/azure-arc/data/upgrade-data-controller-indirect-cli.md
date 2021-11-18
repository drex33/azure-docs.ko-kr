---
title: CLI를 사용하여 간접 모드 Azure Arc 데이터 컨트롤러 업그레이드
description: CLI를 사용하여 간접 모드 Azure Arc 데이터 컨트롤러 업그레이드
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: grrlgeek
ms.author: jeschult
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 5c1afa620fb3b81666aada950c257f38ff5d9a4f
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132707717"
---
# <a name="upgrade-indirect-mode-azure-arc-data-controller-using-the-cli"></a>CLI를 사용하여 간접 모드 Azure Arc 데이터 컨트롤러 업그레이드

이 문서에서는 Azure CLI( )를 사용하여 간접적으로 연결된 Azure Arc 지원 데이터 컨트롤러를 업그레이드하는 방법을 `az` 설명합니다.

> [!IMPORTANT]
> 이 문서는 직접 연결된 Azure Arc 지원 데이터 컨트롤러에는 적용되지 않습니다. 직접 연결된 데이터 컨트롤러를 업그레이드하는 방법에 대한 최신 정보는 릴리스 정보 를 [참조하세요.](./release-notes.md#data-controller-upgrade)

## <a name="prerequisites"></a>필수 구성 요소

imageTag v1.0.0_2021-07-30 이상인 간접 모드 데이터 컨트롤러가 필요합니다.

버전을 확인하려면 다음을 실행합니다.

```console
kubectl get datacontrollers -n -o custom-columns=BUILD:.spec.docker.imageTag
```

## <a name="install-tools"></a>도구 설치

이 문서의 작업을 진행하려면 다음을 설치해야 합니다.

- [Azure CLI(az)](/cli/azure/install-azure-cli)
- [ `arcdata` Azure CLI 대한 확장입니다.](install-arcdata-extension.md)

[!INCLUDE [azure-arc-angle-bracket-example](../../../includes/azure-arc-angle-bracket-example.md)]

## <a name="view-available-images-and-chose-a-version"></a>사용 가능한 이미지 보기 및 버전 선택

다음 명령을 통해 데이터 컨트롤러에 사용할 수 있는 이미지 목록을 가져옵니다.

   ```azurecli
   az arcdata dc list-upgrades --k8s-namespace <namespace> –-use-k8s
   ```

위의 명령은 다음 예제와 같은 출력을 반환합니다.

```output
Found 2 valid versions.  The current datacontroller version is v1.0.0_2021-07-30.
v1.1.0_2021-11-02
v1.0.0_2021-07-30
```

## <a name="upgrade-data-controller"></a>데이터 컨트롤러 업그레이드

이 섹션에서는 간접 모드에서 데이터 컨트롤러를 업그레이드하는 방법을 보여줍니다.

> [!NOTE]
> 일부 데이터 서비스 계층 및 모드는 일반적으로 사용할 수 있으며 일부는 미리 보기로 제공됩니다.
> 동일한 데이터 컨트롤러에 GA 및 미리 보기 서비스를 설치하는 경우 현재 위치로 업그레이드할 수 없습니다.
> 업그레이드하려면 GA가 아닌 모든 데이터베이스 인스턴스를 삭제합니다. [릴리스 정보](./release-notes.md)에서 일반 제공 및 미리 보기 서비스 목록을 찾을 수 있습니다.

### <a name="indirect-mode"></a>간접 모드

Azure Arc 데이터 컨트롤러의 업그레이드를 시작하기 전에 Kubernetes 클러스터에 연결하고 인증하고 기존 Kubernetes 컨텍스트를 선택해야 합니다.

먼저 건성 실행을 수행할 수 있습니다. 건성 실행은 레지스트리, 버전 스키마 및 프라이빗 리포지토리 권한 부여 토큰(사용되는 경우)이 있는지 확인합니다. 건성 실행을 수행하려면 `--dry-run` 명령에서 매개 변수를 `az arcdata dc upgrade` 사용합니다. 예를 들어 다음과 같습니다.

```azurecli
az arcdata dc upgrade --desired-version <version> --k8s-namespace <namespace> --dry-run --use-k8s
```

이전 명령의 출력은 다음과 같습니다.

```output
Preparing to upgrade dc arcdc in namespace arc to version 20211024.1.
Preparing to upgrade dc arcdc in namespace arc to version 20211024.1.
****Dry Run****
Arcdata Control Plane would be upgraded to: 20211024.1
```

데이터 컨트롤러를 업그레이드하려면 `az arcdata dc upgrade` 명령을 실행합니다. 대상 이미지를 지정하지 않으면 데이터 컨트롤러가 최신 버전으로 업그레이드됩니다. 다음 예제에서는 지역 `$version` 변수()를 사용하여 이전에 선택한 버전을 사용합니다(사용[가능한 이미지 보기 및 버전 선택).](#view-available-images-and-chose-a-version)

```azurecli
az arcdata dc upgrade --desired-version $version --k8s-namespace <namespace> --use-k8s
```

이전 명령의 출력에는 단계의 상태가 표시됩니다.

```output
Preparing to upgrade dc arcdc in namespace arc to version 20211024.1.
Preparing to upgrade dc arcdc in namespace arc to version 20211024.1.
Creating service account: arc:cr-upgrade-worker
Creating cluster role: arc:cr-upgrade-worker
Creating cluster role binding: arc:crb-upgrade-worker
Cluster role binding: arc:crb-upgrade-worker created successfully.
Cluster role: arc:cr-upgrade-worker created successfully.
Service account arc:cr-upgrade-worker has been created successfully.
Creating privileged job arc-elevated-bootstrapper-job
```

## <a name="monitor-the-upgrade-status"></a>업그레이드 상태 모니터링

kubectl 또는 CLI를 통해 업그레이드 진행 상황을 모니터링할 수 있습니다.

### <a name="kubectl"></a>kubectl

```console
kubectl get datacontrollers --namespace <namespace>
kubectl get monitors --namespace <namespace>
```

업그레이드는 두 부분으로 구성된 프로세스입니다. 먼저 컨트롤러가 업그레이드된 다음 모니터링 스택이 업그레이드됩니다. 업그레이드하는 동안 를 사용하여 ```kubectl get monitors -n <namespace> -w``` 상태를 확인합니다. 다음과 같이 출력됩니다.

```output
NAME           STATUS     AGE
monitorstack   Updating   36m
monitorstack   Updating   36m
monitorstack   Updating   39m
monitorstack   Updating   39m
monitorstack   Updating   41m
monitorstack   Ready      41m
```

### <a name="cli"></a>CLI

```azurecli
 az arcdata dc status show --k8s-namespace <namespace> --use-k8s
```

업그레이드는 두 부분으로 구성된 프로세스입니다. 먼저 컨트롤러가 업그레이드된 다음 모니터링 스택이 업그레이드됩니다. 업그레이드가 완료되면 출력은 다음과 같습니다.

```output
Ready
```

## <a name="troubleshoot-upgrade-problems"></a>업그레이드 문제 해결

업그레이드하는 데 문제가 발생하는 경우 [문제 해결 가이드](troubleshoot-guide.md)를 참조하세요.