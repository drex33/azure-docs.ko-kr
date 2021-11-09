---
title: Azure IoT Edge 워크로드 배포
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
description: Azure IoT Edge 워크로드 배포
keywords: Kubernetes, Arc, Azure, K8s, 컨테이너
ms.openlocfilehash: 9870e6ce0904510c549033dfd64de9d377b886af
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132056419"
---
# <a name="deploy-azure-iot-edge-workloads"></a>Azure IoT Edge 워크로드 배포

## <a name="overview"></a>개요

Azure Arc 및 Azure IoT Edge는 서로의 기능을 쉽게 보완합니다. 

Azure Arc 클러스터 운영자가 클러스터의 기본 구성 요소를 구성하고 클러스터 정책을 적용 및 적용하는 메커니즘을 제공합니다. 

Azure IoT Edge를 사용하면 애플리케이션 운영자가 편리한 클라우드 사용 및 양방향 통신 기본형을 사용하여 워크로드를 대규모로 원격으로 배포하고 관리할 수 있습니다. 

아래 다이어그램에서는 Edge의 관계를 Azure Arc 및 Azure IoT 보여 줍니다.

![IoT Arc 구성](./media/edge-arc.png)

## <a name="pre-requisites"></a>필수 구성 요소

* [IoT Edge 디바이스 등록](../../iot-edge/quickstart-linux.md#register-an-iot-edge-device) 및 [시뮬레이트된 온도 센서 모듈 배포](../../iot-edge/quickstart-linux.md#deploy-a-module) 아래에 언급된 *values.yaml에* 대한 디바이스의 연결 문자열을 확인합니다.

* [Kubernetes에 대한 IoT Edge 지원](https://aka.ms/edgek8sdoc)을 사용하여 Azure Arc의 Flux 연산자를 통해 배포합니다.

* IoT Edge Helm 차트에 대한 [*values.yaml*](https://github.com/Azure/iotedge/blob/preview/iiot/kubernetes/charts/edge-kubernetes/values.yaml) 파일을 다운로드하고 `deviceConnectionString` 파일 끝에 있는 자리 표시자를 앞에서 적어 두는 연결 문자열로 바꿉니다. 필요에 따라 지원되는 다른 차트 설치 옵션을 설정합니다. IoT Edge 워크로드에 대한 네임스페이스를 만들고 그 안에 비밀을 생성합니다.

  ```
  $ kubectl create ns iotedge

  $ kubectl create secret generic dcs --from-file=fully-qualified-path-to-values.yaml --namespace iotedge
  ```

  [클러스터 구성 예제](./tutorial-use-gitops-connected-cluster.md)를 사용하여 원격으로 설정할 수도 있습니다.

## <a name="connect-a-cluster"></a>클러스터 연결

Azure CLI `az` 확장을 사용하여 `connectedk8s` Kubernetes 클러스터를 Azure Arc 연결합니다.

  ```
  az connectedk8s connect --name AzureArcIotEdge --resource-group AzureArcTest
  ```

## <a name="create-a-configuration-for-iot-edge"></a>IoT Edge의 구성 만들기

[예제 Git 리포지점은](https://github.com/veyalla/edgearc) IoT Edge Helm 차트를 가리키고 필수 조건 섹션에서 만든 비밀을 참조합니다.

Azure CLI `az` 확장을 사용하여 `k8s-configuration` 연결된 클러스터를 Git 리포지션에 연결하는 구성을 만듭니다.

  ```
  az k8s-configuration create --name iotedge --cluster-name AzureArcIotEdge --resource-group AzureArcTest --operator-instance-name iotedge --operator-namespace azure-arc-iot-edge --enable-helm-operator --helm-operator-chart-version 0.6.0 --helm-operator-chart-values "--set helm.versions=v3" --repository-url "git://github.com/veyalla/edgearc.git" --cluster-scoped
  ```

몇 분 후에 클러스터의 네임스페이스에 배포된 IoT Edge 워크로드 모듈이 `iotedge` 표시됩니다. 

해당 `SimulatedTemperatureSensor` 네임스페이스에서 Pod 로그를 확인하여 생성되는 샘플 값을 확인합니다. [Visual Studio Code용 Azure IoT Hub Toolkit 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)을 사용하여 IoT Hub에 메시지가 들어오는 것을 확인할 수도 있습니다.

## <a name="cleanup"></a>정리

다음을 사용하여 구성을 제거합니다.

```
az k8s-configuration delete -g AzureArcTest --cluster-name AzureArcIotEdge --name iotedge
```

## <a name="next-steps"></a>다음 단계

[Azure Policy 사용하여 클러스터 구성을 제어하는](./use-azure-policy.md)방법을 알아봅니다.
