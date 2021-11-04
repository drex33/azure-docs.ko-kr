---
title: 컨테이너 지원
titleSuffix: Azure Cognitive Services
description: Azure CLI에서 Azure 컨테이너 인스턴스 리소스를 만드는 방법을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 11a79d45d6966f9fc4f41ab6149cd6a32d4f75b8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131253469"
---
## <a name="create-an-azure-container-instance-resource-from-the-azure-cli"></a>Azure CLI에서 Azure Container Instance 리소스 만들기

아래 YAML은 Azure Container Instance 리소스를 정의합니다. 콘텐츠를 복사하여 `my-aci.yaml`이라는 새 파일에 붙여넣고 주석 처리된 값을 사용자 고유의 값으로 바꿉니다. 유효한 YAML은 [템플릿 형식][template-format]을 참조하세요. 사용 가능한 이미지 이름 및 해당 리포지토리는 [컨테이너 리포지토리 및 이미지][repositories-and-images]를 참조하세요. 컨테이너 인스턴스의 YAML 참조에 대한 자세한 내용은 [Yaml 참조: Azure Container Instances][aci-yaml-ref]를 참조하세요.

```yaml
apiVersion: 2018-10-01
location: # < Valid location >
name: # < Container Group name >
properties:
  imageRegistryCredentials: # This is only required if you are pulling a non-public image that requires authentication to access. For example Text Analytics for health.
  - server: containerpreview.azurecr.io
    username: # < The username for the preview container registry >
    password: # < The password for the preview container registry >
  containers:
  - name: # < Container name >
    properties:
      image: # < Repository/Image name >
      environmentVariables: # These env vars are required
        - name: eula
          value: accept
        - name: billing
          value: # < Service specific Endpoint URL >
        - name: apikey
          value: # < Service specific API key >
      resources:
        requests:
          cpu: 4 # Always refer to recommended minimal resources
          memoryInGb: 8 # Always refer to recommended minimal resources
      ports:
        - port: 5000
  osType: Linux
  volumes: # This node, is only required for container instances that pull their model in at runtime, such as LUIS.
  - name: aci-file-share
    azureFile:
      shareName: # < File share name >
      storageAccountName: # < Storage account name>
      storageAccountKey: # < Storage account key >
  restartPolicy: OnFailure
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: 5000
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

> [!NOTE]
> 모든 위치에서 CPU 및 메모리 가용성이 동일하지는 않습니다. 위치 및 OS별 컨테이너에 사용 가능한 리소스 목록은 [위치 및 리소스][location-to-resource] 표를 참조하세요.

[`az container create`][azure-container-create] 명령에 대해 만든 YAML 파일을 사용합니다. Azure CLI에서 `<resource-group>`을 사용자 고유의 것으로 바꾸는 `az container create` 명령을 실행합니다. 또한 YAML 배포 내에서 값을 보호하려면 [보안 값][secure-values]을 참조합니다.

```azurecli
az container create -g <resource-group> -f my-aci.yaml
```

명령의 출력이 유효한 경우 `Running...`이며 나중에 출력을 새로 만든 ACI 리소스를 나타내는 JSON 문자열로 변경합니다. 컨테이너 이미지는 잠시 동안 사용하지 못할 수도 있지만 지금 리소스가 배포됩니다.

> [!TIP]
> YAML은 위치와 일치하도록 적절히 조정해야 하므로 공개 미리 보기 Azure Cognitive Service 제품의 위치에 주의하세요.

[azure-container-create]: /cli/azure/container#az_container_create
[template-format]: /azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups#template-format
[aci-yaml-ref]: ../../../container-instances/container-instances-reference-yaml.md
[repositories-and-images]: ../container-image-tags.md
[location-to-resource]: ../../../container-instances/container-instances-region-availability.md
[secure-values]: ../../../container-instances/container-instances-environment-variables.md#secure-values
