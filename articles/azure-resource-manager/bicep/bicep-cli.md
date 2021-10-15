---
title: Bicep CLI 명령 및 개요
description: Bicep CLI에서 사용할 수 있는 명령에 대해 설명합니다. 이러한 명령에는 Bicep에서 Azure Resource Manager 템플릿을 빌드하는 명령이 포함됩니다.
ms.topic: conceptual
ms.date: 10/14/2021
ms.openlocfilehash: 7158ad4ce79986661d5f6ec514115d28305d3590
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130067291"
---
# <a name="bicep-cli-commands"></a>Bicep CLI 명령

이 문서에서는 Bicep CLI에서 사용할 수 있는 명령에 대해 설명합니다. 이러한 명령을 실행하려면 [Bicep CLI가 설치](./install.md)되어 있어야 합니다.

이 문서에서는 Azure CLI에서 명령을 실행하는 방법을 보여 줍니다. Azure CLI 사용하지 않는 경우 명령을 시작할 때 `az`를 사용하지 않고 명령을 실행합니다. 예를 들어 `az bicep version`는 ``bicep version``가 됩니다.

## <a name="build"></a>빌드

`build` 명령은 Bicep 파일을 ARM 템플릿(Azure Resource Manager 템플릿)으로 변환합니다. 일반적으로 이 명령은 Bicep 파일을 배포할 때 자동으로 실행되므로 실행할 필요가 없습니다. Bicep 파일에서 만든 ARM 템플릿 JSON을 보려면 수동으로 실행합니다.

다음 예제에서는 _main.bicep_ 이라는 Bicep 파일을 _main.json_ 이라는 ARM 템플릿으로 변환합니다. 새 파일은 Bicep 파일이 있는 동일한 디렉터리에 만들어집니다.

```azurecli
az bicep build --file main.bicep
```

다음 예제는 _main.json_ 을 다른 디렉터리에 저장합니다.

```azurecli
az bicep build --file main.bicep --outdir c:\jsontemplates
```

다음 예제는 만들 파일의 이름과 위치를 지정합니다.

```azurecli
az bicep build --file main.bicep --outfile c:\jsontemplates\azuredeploy.json
```

`stdout`에 파일을 인쇄하려면 다음을 사용합니다.

```azurecli
az bicep build --file main.bicep --stdout
```

Bicep 파일에 외부 레지스트리를 참조 하는 모듈이 포함 되어 있으면 빌드 명령에서 [restore](#restore)를 자동으로 호출 합니다. Restore 명령은 레지스트리에서 파일을 가져와 로컬 캐시에 저장 합니다.

복원을 자동으로 호출 하지 않으려면 스위치를 사용 합니다 `--no-restore` .

```azurecli
az bicep build --no-restore <bicep-file>
```

`--no-restore`외부 모듈 중 하나가 아직 캐시 되지 않은 경우 스위치를 사용 하는 빌드 프로세스가 실패 합니다.

```error
The module with reference "br/exampleregistry.azurecr.io/bicep/modules/storage:v1" has not been restored.
```

이 오류가 발생 하면 `build` 스위치 없이 명령을 실행 `--no-restore` 하거나 먼저를 실행 `bicep restore` 합니다.

스위치를 사용 하려면 `--no-restore` BICEP CLI 버전 **0.4.1008 이상이** 있어야 합니다.

## <a name="decompile"></a>디컴파일

`decompile` 명령은 ARM 템플릿 JSON을 Bicep 파일로 변환합니다.

```azurecli
az bicep decompile --file main.json
```

이 명령 사용에 대한 자세한 내용은 [ARM 템플릿 JSON을 Bicep으로 디컴파일](decompile.md)을 참조하세요.

## <a name="install"></a>설치

`install` 명령은 로컬 환경에 Bicep CLI를 추가합니다. 자세한 내용은 [Bicep 도구 설치](install.md)를 참조하세요.

최신 버전을 설치하려면 다음을 사용하세요.

```azurecli
az bicep install
```

특정 버전을 설치하려면

```azurecli
az bicep install --version v0.3.255
```

## <a name="list-versions"></a>list-versions

`list-versions` 명령은 사용 가능한 모든 버전의 Bicep CLI를 반환합니다. 이 명령을 사용하여 새 버전을 [업그레이드](#upgrade)하거나 [설치](#install)할 것인지 확인합니다.

```azurecli
az bicep list-versions
```

이 명령은 사용 가능한 버전 배열을 반환합니다.

```azurecli
[
  "v0.4.1",
  "v0.3.539",
  "v0.3.255",
  "v0.3.126",
  "v0.3.1",
  "v0.2.328",
  "v0.2.317",
  "v0.2.212",
  "v0.2.59",
  "v0.2.14",
  "v0.2.3",
  "v0.1.226-alpha",
  "v0.1.223-alpha",
  "v0.1.37-alpha",
  "v0.1.1-alpha"
]
```

## <a name="publish"></a>게시

`publish`명령은 레지스트리에 모듈을 추가 합니다. Azure container registry가 있어야 하 고 레지스트리에 게시 하는 계정에 올바른 권한이 있어야 합니다. 모듈 레지스트리를 설정 하는 방법에 대 한 자세한 내용은 [Bicep 모듈에 개인 레지스트리 사용](private-module-registry.md)을 참조 하세요.

파일을 레지스트리에 게시 한 후에 [는 모듈에서 참조할](modules.md#file-in-registry)수 있습니다.

Publish 명령을 사용 하려면 Bicep CLI version **0.4.1008 이상이** 있어야 합니다.

레지스트리에 모듈을 게시 하려면 다음을 사용 합니다.

```azurecli
az bicep publish <bicep-file> --target br/<registry-name>.azurecr.io/<module-path>:<tag>
```

예를 들면 다음과 같습니다.

```azurecli
az bicep publish storage.bicep --target br/exampleregistry.azurecr.io/bicep/modules/storage:v1
```

`publish`명령은 [bicepconfig](bicep-config.md) 파일에서 정의한 별칭을 인식 하지 못합니다. 전체 모듈 경로를 제공 합니다.

> [!WARNING]
> 동일한 대상에 게시 하면 이전 모듈을 덮어씁니다. 업데이트 시 버전을 증가 시키는 것이 좋습니다.

## <a name="restore"></a>복원

Bicep 파일이 레지스트리에 게시 된 모듈을 사용 하는 경우이 `restore` 명령은 레지스트리에서 필요한 모든 모듈의 복사본을 가져옵니다. 이러한 복사본은 로컬 캐시에 저장 됩니다. 로컬 캐시에서 외부 파일을 사용할 수 있는 경우에만 Bicep 파일을 빌드할 수 있습니다. 일반적으로 `restore` 는에서 자동으로 호출 되기 때문에를 실행할 필요가 없습니다 `build` .

Restore 명령을 사용 하려면 Bicep CLI version **0.4.1008 이상이** 있어야 합니다.

파일의 외부 모듈을 수동으로 복원 하려면 다음을 사용 합니다.

```azurecli
az bicep restore <bicep-file>
```

사용자가 제공 하는 Bicep 파일은 배포 하려는 파일입니다. 레지스트리에 연결 되는 모듈을 포함 해야 합니다. 예를 들어 다음 파일을 복원할 수 있습니다.

```bicep
module stgModule 'br/exampleregistry.azurecr.io/bicep/modules/storage:v1' = {
  name: 'storageDeploy'
  params: {
    storagePrefix: 'examplestg1'
  }
}
```

로컬 캐시는 다음 위치에 있습니다.

```path
%USERPROFILE%\.bicep\br\<registry-name>.azurecr.io\<module-path\<tag>
```

## <a name="upgrade"></a>업그레이드

`upgrade` 명령은 설치된 버전을 최신 버전으로 업데이트합니다.

```azurecli
az bicep upgrade
```

## <a name="version"></a>버전

`version` 명령은 설치된 버전을 반환합니다.

```azurecli
az bicep version
```

이 명령은 버전 번호를 표시합니다.

```azurecli
Bicep CLI version 0.4.1 (e2387595c9)
```

Bicep CLI를 설치하지 않은 경우 Bicep CLI를 찾을 수 없다는 오류가 표시됩니다.

## <a name="next-steps"></a>다음 단계

Bicep 파일 배포에 대한 자세한 내용은 다음을 참조하세요.

* [Azure CLI](deploy-cli.md)
* [Cloud Shell](deploy-cloud-shell.md)
* [PowerShell](deploy-powershell.md)
