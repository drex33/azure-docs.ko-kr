---
title: Bicep 설치 관련 문제 해결
description: Bicep 설치와 관련 된 오류 및 문제를 해결 하는 방법입니다.
ms.topic: conceptual
ms.date: 11/19/2021
ms.openlocfilehash: 55d3161f39d34cbceb8e59ed6e14c13729e7b807
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2021
ms.locfileid: "132863464"
---
# <a name="troubleshoot-bicep-installation"></a>Bicep 설치 문제 해결

이 문서에서는 Bicep 설치 시 발생할 수 있는 오류를 해결 하는 방법을 설명 합니다.

## <a name="net-runtime-error"></a>.NET 런타임 오류

Visual Studio Code 용 Bicep 확장을 설치 하는 경우 다음 오류 메시지가 나타날 수 있습니다.

```error
Failed to install .NET runtime v5.0
```

```error
Failed to download .NET 5.0.x ....... Error!
```

이 문제를 해결 하려면 .net [웹 사이트](https://aka.ms/dotnet-core-download)에서 .net을 수동으로 설치한 다음 기존 .net 설치를 다음 설정으로 다시 사용 하도록 Visual Studio Code를 구성 하면 됩니다.

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

**X64** 설치를 사용 해야 하는 경우 다음을 사용 합니다.

```json
"dotnetAcquisitionExtension.existingDotnetPath": [
  {
    "extensionId": "ms-azuretools.vscode-bicep",
    "path": "/usr/local/share/dotnet/x64/dotnet"
  }
]
```

다른 **Macos** 설치의 경우 다음을 사용 합니다.

```json
"dotnetAcquisitionExtension.existingDotnetPath": [
  {
    "extensionId": "ms-azuretools.vscode-bicep",
    "path": "/usr/local/share/dotnet/dotnet"
  }
]
```

Visual Studio Code 설정을 구성 하려면 [사용자 및 작업 영역 설정](https://code.visualstudio.com/docs/getstarted/settings) 를 참조 하세요.

## <a name="multiple-versions-of-bicep-cli-installed"></a>여러 버전의 Bicep CLI가 설치 됨

Bicep CLI를 여러 위치에 수동으로 설치 하는 경우 [업그레이드 명령을](bicep-cli.md#upgrade)실행할 때 Bicep cli를 업데이트 하지 않는 것과 같은 예기치 않은 동작이 발생할 수 있습니다. 또는이 실행 되는 경우 `az bicep version` 한 버전을 반환 하지만 다른 버전을 반환 하는 것을 알 수 있습니다 `bicep --version` .

이 문제를 해결 하려면 모든 위치를 업데이트 하거나 다른 위치를 유지 하기 위해 위치 하나를 선택 하거나 다른 위치를 삭제할 수 있습니다.

먼저 명령 프롬프트 (PowerShell 아님)를 열고를 실행 `where bicep` 합니다. 이 명령은 Bicep 설치의 위치를 반환 합니다. Azure CLI에서 관리 하는 Bicep CLI의 인스턴스를 사용 하는 경우 경로에 추가 되지 않으므로이 설치는 표시 되지 않습니다. 에서 한 위치만 반환 하는 경우에는 충돌 하는 `where bicep` 버전이 수동 설치와 Azure CLI 설치 사이에 있을 수 있습니다.

**모든 설치 위치를 유지** 하려면 이전에 사용한 것과 동일한 방법을 사용 하 여 유지 하려는 모든 위치에 대해 [Bicep CLI를 수동으로 설치](install.md#install-manually) 합니다. Azure CLI를 사용 하는 경우를 실행 `az bicep upgrade` 하 여 해당 버전을 업데이트 합니다.

**설치 위치** 를 하나만 유지 하려면 다음 단계를 사용 합니다.

1. 유지 하지 않으려는 설치에 대 한 파일을 삭제 합니다.
1. **PATH** 환경 변수에서 이러한 위치를 제거 합니다.

**수동 설치와 Azure CLI에서 관리** 하는 인스턴스를 모두 사용 하는 경우 사용을 하나의 인스턴스로 결합할 수 있습니다.

1. 수동 설치 위치를 삭제 합니다.
1. Azure CLI 하 여 설치 된 Bicep CLI의 위치를 **PATH** 변수에 추가 합니다. Windows의 경우 Azure CLI에서 유지 관리 되는 위치는 `%USERPROFILE%\.Azure\bin` 입니다.

경로에 Azure CLI 인스턴스를 추가한 후에는 해당 버전을 또는와 함께 사용할 수 있습니다 `az bicep` `bicep` .

## <a name="next-steps"></a>다음 단계

Visual Studio Code 및 Bicep 확장 사용에 대한 자세한 내용은 [빠른 시작: Visual Studio Code를 사용하여 Bicep 파일 만들기](./quickstart-create-bicep-use-visual-studio-code.md)를 참조하세요.
