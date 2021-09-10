---
title: AKS(Azure Kubernetes Service) 클러스터의 계획된 유지 관리 사용(미리 보기)
titleSuffix: Azure Kubernetes Service
description: AKS(Azure Kubernetes Service)에서 계획된 유지 관리 사용 방법을 알아봅니다.
services: container-service
ms.topic: article
ms.date: 03/03/2021
ms.author: qpetraroia
author: qpetraroia
ms.openlocfilehash: 1c0a7768c0f5e30d9d5ab3b0fa32b0eb6f4946c3
ms.sourcegitcommit: d137460f55a38a0e8f8b9e6594e480d5e5f662ed
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2021
ms.locfileid: "112428420"
---
# <a name="use-planned-maintenance-to-schedule-maintenance-windows-for-your-azure-kubernetes-service-aks-cluster-preview"></a>계획된 유지 관리를 사용하여 AKS(Azure Kubernetes Service) 클러스터의 유지 관리 기간 예약(미리 보기)

AKS 클러스터에서는 정기적인 유지 관리가 자동으로 수행됩니다. 기본적으로 이 작업은 언제든지 수행할 수 있습니다. 계획된 유지 관리를 사용하면 VMSS 인스턴스의 kube 시스템 Pod와 컨트롤 플레인을 업데이트하고 워크로드 영향을 최소화하는 주간 유지 관리 기간을 예약할 수 있습니다. 예약되면 선택한 기간에 모든 유지 관리가 수행됩니다. 특정 날짜에 날짜 또는 시간 범위를 지정하여 클러스터에서 하나 이상의 주별 기간을 예약할 수 있습니다. 유지 관리 기간은 Azure CLI를 사용하여 구성됩니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 기존 AKS 클러스터가 있다고 가정합니다. AKS 클러스터가 필요한 경우 AKS 빠른 시작 [Azure CLI 사용][aks-quickstart-cli] 또는 [Azure Portal 사용][aks-quickstart-portal]을 참조하세요.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="limitations"></a>제한 사항

계획된 유지 관리를 사용하는 경우 다음 제한 사항이 적용됩니다.

- AKS는 긴급하거나 중요한 계획되지 않은/반응형 유지 관리 작업의 해당 기간을 중단할 권리를 보유합니다.
- 현재 유지 관리 작업을 수행하는 것은 *최적의 작업* 으로 간주하며 지정된 기간에 발생하지 않을 수도 있습니다.
- 7일 넘게 업데이트를 차단할 수 없습니다.

### <a name="install-aks-preview-cli-extension"></a>aks-preview CLI 확장 설치

*aks-preview* Azure CLI 확장 버전 0.5.4 이상도 필요합니다. [Az extension add][az-extension-add] 명령을 사용하여 *aks-preview* Azure CLI 확장을 설치 합니다. 또는 [az extension update][az-extension-update] 명령을 사용하여 사용 가능한 업데이트를 설치 합니다.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="allow-maintenance-on-every-monday-at-100am-to-200am"></a>매주 월요일 오전 1시부터 오전 2시까지 유지 관리 허용

유지 관리 기간을 추가하려면 `az aks maintenanceconfiguration add` 명령을 사용할 수 있습니다.

> [!IMPORTANT]
> 지금은 `default`을 `--name`에 대한 값으로 설정해야 합니다. 다른 이름을 사용 하면 유지 관리 기간이 실행되지 않습니다.
>
> 계획된 유지 관리 기간은 협정 세계시(UTC)로 지정됩니다.

```azurecli-interactive
az aks maintenanceconfiguration add -g MyResourceGroup --cluster-name myAKSCluster --name default --weekday Monday  --start-hour 1
```

다음 예제 출력은 매주 월요일 오전 1시부터 오전 2시까지의 유지 관리 기간을 보여 줍니다.

```json
{- Finished ..
  "id": "/subscriptions/<subscriptionID>/resourcegroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/default",
  "name": "default",
  "notAllowedTime": null,
  "resourceGroup": "MyResourceGroup",
  "systemData": null,
  "timeInWeek": [
    {
      "day": "Monday",
      "hourSlots": [
        1
      ]
    }
  ],
  "type": null
}
```

하루 중 언제든지 유지 관리를 허용하려면 *start-hour* 매개 변수를 생략합니다. 예를 들어 다음 명령은 월요일마다 전체 요일의 유지 관리 기간을 설정합니다.

```azurecli-interactive
az aks maintenanceconfiguration add -g MyResourceGroup --cluster-name myAKSCluster --name default --weekday Monday
```

## <a name="add-a-maintenance-configuration-with-a-json-file"></a>JSON 파일을 사용하여 유지 관리 구성 추가

매개 변수를 사용하는 대신 JSON 파일을 사용하여 유지 관리 기간을 만들 수도 있습니다. 다음 콘텐츠가 포함된 `test.json` 파일을 만듭니다.

```json
  {
        "timeInWeek": [
          {
            "day": "Tuesday",
            "hour_slots": [
              1,
              2
            ]
          },
          {
            "day": "Wednesday",
            "hour_slots": [
              1,
              6
            ]
          }
        ],
        "notAllowedTime": [
          {
            "start": "2021-05-26T03:00:00Z",
            "end": "2021-05-30T12:00:00Z"
          }
        ]
}
```

위의 JSON 파일은 매주 화요일 오전 1:00 - 오전 3:00 및 매주 수요일 오전 1:00 - 오전 2:00 및 오전 6:00 - 오전 7:00에 유지 관리 기간을 지정합니다. *2021-05-26T03:00:00Z* 부터 *2021-05-30T12:00:00Z* 까지는 예외입니다. 이 기간은 유지 관리 기간과 겹치는 경우에도 유지 관리가 허용되지 않습니다. 다음 명령은 `test.json`에서 유지 관리 기간을 추가합니다.

```azurecli-interactive
az aks maintenanceconfiguration add -g MyResourceGroup --cluster-name myAKSCluster --name default --config-file ./test.json
```

## <a name="update-an-existing-maintenance-window"></a>기존 유지 관리 기간 업데이트

기존 유지 관리 구성을 업데이트하려면 `az aks maintenanceconfiguration update` 명령을 사용합니다.

```azurecli-interactive
az aks maintenanceconfiguration update -g MyResourceGroup --cluster-name myAKSCluster --name default --weekday Monday  --start-hour 1
```

## <a name="list-all-maintenance-windows-in-an-existing-cluster"></a>기존 클러스터의 모든 유지 관리 기간 나열

AKS 클러스터에서 현재 유지 관리 구성 창을 모두 보려면 `az aks maintenanceconfiguration list` 명령을 사용합니다.

```azurecli-interactive
az aks maintenanceconfiguration list -g MyResourceGroup --cluster-name myAKSCluster
```

아래 출력에서는 myAKSCluster에 구성된 유지 관리 기간이 두 개 있음을 볼 수 있습니다. 한 기간은 월요일 오전 1시이고 다른 기간은 금요일 오전 4시입니다.

```json
[
  {
    "id": "/subscriptions/<subscriptionID>/resourcegroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/default",
    "name": "default",
    "notAllowedTime": null,
    "resourceGroup": "MyResourceGroup",
    "systemData": null,
    "timeInWeek": [
      {
        "day": "Monday",
        "hourSlots": [
          1
        ]
      }
    ],
    "type": null
  },
  {
    "id": "/subscriptions/<subscriptionID>/resourcegroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/testConfiguration",
    "name": "testConfiguration",
    "notAllowedTime": null,
    "resourceGroup": "MyResourceGroup",
    "systemData": null,
    "timeInWeek": [
      {
        "day": "Friday",
        "hourSlots": [
          4
        ]
      }
    ],
    "type": null
  }
]
```

## <a name="show-a-specific-maintenance-configuration-window-in-an-aks-cluster"></a>AKS 클러스터의 특정 유지 관리 구성 기간 표시

AKS 클러스터에서 현재 유지 관리 구성 기간을 모두 보려면 `az aks maintenanceconfiguration show` 명령을 사용합니다.

```azurecli-interactive
az aks maintenanceconfiguration show -g MyResourceGroup --cluster-name myAKSCluster --name default
```

다음 예제 출력에서는 *기본값* 의 유지 관리 기간을 보여 줍니다.

```json
{
  "id": "/subscriptions/<subscriptionID>/resourcegroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/default",
  "name": "default",
  "notAllowedTime": null,
  "resourceGroup": "MyResourceGroup",
  "systemData": null,
  "timeInWeek": [
    {
      "day": "Monday",
      "hourSlots": [
        1
      ]
    }
  ],
  "type": null
}
```

## <a name="delete-a-certain-maintenance-configuration-window-in-an-existing-aks-cluster"></a>기존 AKS 클러스터에서 특정 유지 관리 구성 기간 삭제

AKS 클러스터에서 특정 유지 관리 구성 기간을 모두 보려면 `az aks maintenanceconfiguration delete` 명령을 사용합니다.

```azurecli-interactive
az aks maintenanceconfiguration delete -g MyResourceGroup --cluster-name myAKSCluster --name default
```

## <a name="using-planned-maintenance-with-cluster-auto-upgrade"></a>클러스터 자동 업그레이드와 함께 계획된 유지 관리 사용

계획된 유지 관리는 클러스터 자동 업그레이드를 사용하고 자동으로 유지 관리 기간 동안 업그레이드를 예약하는지 검색합니다. 클러스터 자동 업그레이드에 대한 자세한 내용은 [AKS(Azure Kubernetes Service) 클러스터 업그레이드][aks-upgrade]를 참조하세요.

## <a name="next-steps"></a>다음 단계

- AKS 클러스터 업그레이드를 시작하려면 [AKS 클러스터 업그레이드][aks-upgrade] 참조


<!-- LINKS - Internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-provider-register]: /cli/azure/provider#az_provider_register
[aks-upgrade]: upgrade-cluster.md
