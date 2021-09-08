---
title: Azure Digital Twins CLI 명령 집합
titleSuffix: Azure Digital Twins
description: Azure Digital Twins CLI 명령 집합을 이해합니다.
author: baanders
ms.author: baanders
ms.date: 8/25/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 087a7bc7fda1c54e0a9e2c7f896b06dda78fa11e
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123221160"
---
# <a name="azure-digital-twins-cli-command-set"></a>Azure Digital Twins CLI 명령 집합

Azure Portal에서 Azure Digital Twins 인스턴스를 관리하는 것 외에도, Azure Digital Twins에는 다음을 포함한 서비스를 통해 대부분의 주요 작업을 수행하는 데 사용할 수 있는 [Azure CLI](/cli/azure/what-is-azure-cli)에 대한 명령 집합도 있습니다.
* Azure Digital Twins 인스턴스 관리
* 모델 관리
* 디지털 트윈 관리
* 트윈 관계 관리
* 엔드포인트 구성
* [경로](concepts-route-events.md) 관리
* Azure RBAC(Azure 역할 기반 액세스 제어)를 통해 [보안](concepts-security.md) 구성

명령 집합은 **az dt** 라고 하며, [Azure CLI용 Azure IoT 확장](https://github.com/Azure/azure-iot-cli-extension)의 일부입니다. `az iot` 명령 집합: [az dt 명령 참조](/cli/azure/dt?view=azure-cli-latest&preserve-view=true)에 대한 참조 설명서의 일부로 명령 및 사용법의 전체 목록을 볼 수 있습니다.

## <a name="uses-deploy-and-validate"></a>사용(배포 및 유효성 검사)

CLI는 일반적으로 인스턴스를 관리하는 것 외에도 배포 및 유효성 검사에 유용한 도구입니다.
* 제어 평면 명령을 사용하여 새 인스턴스의 배포를 반복하거나 자동화할 수 있습니다.
* 데이터 평면 명령을 사용하여 인스턴스의 값을 신속하게 확인하고 작업이 예상대로 완료되었는지 확인할 수 있습니다.

## <a name="get-the-command-set"></a>명령 집합 가져오기

Azure Digital Twins 명령은 [Azure CLI용 Azure IoT 확장(azure-iot)](https://github.com/Azure/azure-iot-cli-extension)의 일부이므로 다음 단계에 따라 **az dt** 명령을 사용하여 최신 `azure-iot` 확장이 있는지 확인합니다.

### <a name="cli-version-requirements"></a>CLI 버전 요구 사항

PowerShell과 함께 Azure CLI를 사용하는 경우 확장 패키지의 요구 사항으로 Azure CLI 버전이 **2.3.1** 이상이어야 합니다.

다음 CLI 명령을 사용하여 Azure CLI의 버전을 확인할 수 있습니다.
```azurecli
az --version
```

Azure CLI를 최신 버전으로 설치하거나 업데이트하는 방법에 대한 지침은 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

### <a name="get-the-extension"></a>확장 가져오기

Azure CLI는 필요한 명령을 처음 사용할 때 확장을 설치하라는 메시지를 자동으로 표시합니다.

아니면 다음 명령을 사용하여 언제든지 확장을 직접 설치하거나 이전 버전이 이미 있는 경우에는 해당 확장을 업데이트할 수 있습니다. 이 명령은 [Azure Cloud Shell](../cloud-shell/overview.md) 또는 [로컬 Azure CLI](/cli/azure/install-azure-cli)에서 실행할 수 있습니다.

```azurecli-interactive
az extension add --upgrade --name azure-iot
```

## <a name="next-steps"></a>다음 단계

참조 문서를 통해 CLI 및 전체 명령 세트를 탐색합니다.
* [az dt 명령 참조](/cli/azure/dt?view=azure-cli-latest&preserve-view=true)