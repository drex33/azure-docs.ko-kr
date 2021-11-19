---
title: Bicep 설치 문제 해결
description: Bicep 설치와 관련하여 오류 및 문제를 해결하는 방법입니다.
ms.topic: conceptual
ms.date: 11/18/2021
ms.openlocfilehash: 261bc7c9a6f2fe8d0c68c7df45c6fab1d009a0d3
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132817882"
---
# <a name="troubleshoot-bicep-installation"></a>Bicep 설치 문제 해결

이 문서에서는 Bicep 설치에서 잠재적인 오류를 해결하는 방법을 설명합니다.

## <a name="net-runtime-error"></a>.NET 런타임 오류

Visual Studio Code Bicep 확장을 설치할 때 다음과 같은 오류 메시지가 나타날 수 있습니다.

```error
Failed to install .NET runtime v5.0
```

```error
Failed to download .NET 5.0.x ....... Error!
```

이 문제를 해결하려면 [.NET 웹 사이트에서 .NET을](https://aka.ms/dotnet-core-download)수동으로 설치한 다음, 다음 설정을 사용하여 기존 .NET 설치를 다시 사용하도록 Visual Studio Code 구성할 수 있습니다.

**Windows**

```json
"dotnetAcquisitionExtension.existingDotnetPath": [
  {
    "extensionId": "ms-azuretools.vscode-bicep",
    "path": "C:\\Program Files\\dotnet\\dotnet.exe"
  }
]

```

**macOS**

**x64** 설치가 필요한 경우 다음을 사용합니다.

```json
"dotnetAcquisitionExtension.existingDotnetPath": [
  {
    "extensionId": "ms-azuretools.vscode-bicep",
    "path": "/usr/local/share/dotnet/x64/dotnet"
  }
]
```

다른 **macOS** 설치의 경우 다음을 사용합니다.

```json
"dotnetAcquisitionExtension.existingDotnetPath": [
  {
    "extensionId": "ms-azuretools.vscode-bicep",
    "path": "/usr/local/share/dotnet/dotnet"
  }
]
```

Visual Studio Code 설정을 구성하기 위한 [사용자 및 작업 영역](https://code.visualstudio.com/docs/getstarted/settings) 설정 참조하세요.

## <a name="two-versions-of-bicep-cli-installed"></a>설치된 Bicep CLI의 두 가지 버전

Bicep CLI를 두 개 이상의 위치에 수동으로 설치하는 경우 [업그레이드 명령을](bicep-cli.md#upgrade)실행할 때 Bicep CLI가 업데이트되지 않는 등의 예기치 않은 동작이 나타날 수 있습니다. 또는 를 실행하면 `az bicep version` 하나의 버전이 반환되지만 다른 버전이 반환되는 것을 알 수 `bicep --version` 있습니다.

이 문제를 해결하려면 두 위치를 모두 업데이트하거나 한 위치를 삭제하고 다른 위치를 경로에 추가할 수 있습니다.

**두 설치 위치를 모두 유지하려면** 를 사용하여 한 버전을 `az bicep upgrade` 업데이트합니다. 다른 버전의 경우 이전에 사용한 것과 동일한 방법을 사용하여 [Bicep CLI 를 수동으로 설치합니다.](install.md#install-manually)

**설치 위치를 하나만 유지하려면** 다음 단계를 사용합니다.

1. PowerShell이 아닌 명령 프롬프트를 열고 를 `where bicep` 실행합니다. 이 명령은 Bicep 설치의 위치를 반환합니다.
1. 이전 단계에서 반환된 설치를 삭제합니다.
1. **PATH** 환경 변수에서 위치를 제거합니다.
1. PATH **변수에** 다른 설치 위치를 추가합니다. Windows 경우 Azure CLI 유지 관리되는 위치는 `%USERPROFILE%\.Azure\bin` 입니다.

## <a name="next-steps"></a>다음 단계

Visual Studio Code 및 Bicep 확장 사용에 대한 자세한 내용은 [빠른 시작: Visual Studio Code를 사용하여 Bicep 파일 만들기](./quickstart-create-bicep-use-visual-studio-code.md)를 참조하세요.
