---
title: Bicep 개발 및 배포 환경 설정
description: Bicep 개발 및 배포 환경을 구성하는 방법
ms.topic: conceptual
ms.date: 10/20/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 7f590c0d0954ca4e3ccc3f4d894f55892b01a6ae
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131443512"
---
# <a name="install-bicep-tools"></a>Bicep 도구 설치

Bicep 파일을 개발하고 배포하기 위한 환경이 설정되었는지 확인해 보겠습니다.

## <a name="vs-code-and-bicep-extension"></a>VS Code 및 Bicep 확장

Bicep 파일을 만들려면 적절한 Bicep 편집기가 필요합니다. 다음이 권장됩니다.

- **Visual Studio Code** - 아직 Visual Studio Code가 없는 경우 [설치](https://code.visualstudio.com/)합니다.
- **Visual Studio Code용 Bicep 확장**  Bicep 확장이 포함된 Visual Studio Code는 언어 지원 및 리소스 자동 완성 기능을 제공합니다. 확장을 사용하여 Bicep 파일을 만들고 유효성을 검사할 수 있습니다.

  확장을 설치하려면 **확장** 탭 또는 [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep)에서 *bicep* 을 검색합니다.

  **설치** 를 선택합니다.

  :::image type="content" source="./media/install/install-extension.png" alt-text="Bicep 확장 설치":::

확장을 설치했는지 확인하려면 `.bicep` 파일 확장명을 사용하여 파일을 엽니다. 오른쪽 아래 모퉁이의 언어 모드가 **Bicep** 으로 변경된 것을 볼 수 있습니다.

:::image type="content" source="./media/install/language-mode.png" alt-text="Bicep 언어 모드":::

### <a name="troubleshoot"></a>문제 해결

Visual Studio Code Bicep 확장을 설치할 때 다음과 같은 오류 메시지가 나타날 수 있습니다.

```error
Failed to install .NET runtime v5.0
```

```error
Failed to download .NET 5.0.x ....... Error!
```

이 문제를 해결하려면 [.NET 웹 사이트에서](https://aka.ms/dotnet-core-download).NET을 수동으로 설치한 다음 기존 .NET 설치를 다시 사용하도록 Visual Studio Code 구성할 수 있습니다. 다음 설정을 사용합니다.

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

```json
"dotnetAcquisitionExtension.existingDotnetPath": [
  {
    "extensionId": "ms-azuretools.vscode-bicep",
    "path": "/usr/local/share/dotnet/dotnet"
  }
]
```

Visual Studio Code 설정 구성은 [사용자 및 작업 영역](https://code.visualstudio.com/docs/getstarted/settings) 설정 참조하세요.

## <a name="deployment-environment"></a>배포 환경

개발 환경을 설정하면 배포 환경용 도구를 설치해야 합니다. 로컬 배포 환경을 설정하려면 [Azure CLI](#azure-cli) 사용하여 Bicep CLI를 설치하거나 [를 Azure PowerShell.](#azure-powershell) 이러한 단계는 다음 섹션에 표시됩니다.

Azure Pipeline에서 Bicep 파일을 배포하려면 [Bicep과 Azure Pipelines 통합을](add-template-to-azure-pipelines.md)참조하세요. GitHub Actions를 통해 Bicep 파일을 배포하려면 GitHub [작업을 사용하여 Bicep 파일 배포를 참조하세요.](deploy-github-actions.md)

## <a name="azure-cli"></a>Azure CLI

Azure CLI 버전 **2.20.0이** 설치되어 있어야 합니다. Azure CLI를 설치하거나 업데이트하려면 다음을 참조하세요.

- [Windows에 Azure CLI 설치](/cli/azure/install-azure-cli-windows)
- [Linux에 Azure CLI 설치](/cli/azure/install-azure-cli-linux)
- [macOS에 Azure CLI 설치](/cli/azure/install-azure-cli-macos)

현재 버전을 확인하려면 다음을 실행합니다.

```azurecli
az --version
```

이제 Bicep 파일을 [배포](deploy-cli.md)하고 [디컴파일](decompile.md)하는 데 필요한 모든 것이 있습니다. 필요한 명령이 실행될 때 Azure CLI 자동으로 Bicep CLI를 설치하기 때문에 모든 것이 있습니다.

Bicep CLI 설치를 수동으로 시작하려면 다음을 사용합니다.

```azurecli
az bicep install
```

최신 버전으로 업그레이드하려면 다음을 수행합니다.

```azurecli
az bicep upgrade
```

설치를 확인하려면 다음을 사용합니다.

```azurecli
az bicep version
```

자세한 명령은 [Bicep CLI](bicep-cli.md)를 참조하세요.

> [!IMPORTANT]
> Azure CLI는 Bicep CLI의 자체 포함 인스턴스를 설치합니다. 이 인스턴스는 수동으로 설치한 버전과 충돌하지 않습니다. Azure CLI는 Bicep CLI를 PATH에 추가하지 않습니다.

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell 버전 **5.6.0이** 설치되어 있어야 합니다. 업데이트하거나 설치하려면 [Azure PowerShell 설치](/powershell/azure/install-az-ps)를 참조하세요.

Azure PowerShell은 Bicep CLI를 자동으로 설치하지 않습니다. 대신 [Bicep CLI를 수동으로 설치](#install-manually)해야 합니다.

> [!IMPORTANT]
> Azure CLI에 의해 설치된 Bicep CLI의 자체 포함 인스턴스는 PowerShell 명령에 사용할 수 없습니다. Bicep CLI를 수동으로 설치하지 않은 경우 Azure PowerShell 배포가 실패합니다.

Bicep CLI를 수동으로 설치할 때 Azure CLI용 `az bicep` 구문 대신 `bicep` 구문을 사용하여 Bicep 명령을 실행합니다.

CLI 버전을 확인하려면 다음을 실행합니다.

```cmd
bicep --version
```

## <a name="install-manually"></a>수동 설치

다음 메서드는 Bicep CLI를 설치하고 경로에 추가합니다. Azure CLI 이외의 모든 용도에 대해서는 수동으로 설치해야 합니다.

- [Linux](#linux)
- [macOS](#macos)
- [Windows](#windows)

### <a name="linux"></a>Linux

```sh
# Fetch the latest Bicep CLI binary
curl -Lo bicep https://github.com/Azure/bicep/releases/latest/download/bicep-linux-x64
# Mark it as executable
chmod +x ./bicep
# Add bicep to your PATH (requires admin)
sudo mv ./bicep /usr/local/bin/bicep
# Verify you can now access the 'bicep' command
bicep --help
# Done!
```

> [!NOTE]
> [Alpine](https://alpinelinux.org/)과 같은 경량 Linux 배포판의 경우 앞의 스크립트에서 **bicep-linux-x64** 대신 **bicep-linux-musl-x64** 를 사용합니다.

### <a name="macos"></a>macOS

#### <a name="via-homebrew"></a>homebrew를 통해

```sh
# Add the tap for bicep
brew tap azure/bicep

# Install the tool
brew install bicep
```

#### <a name="via-bash"></a>BASH를 통해

```sh
# Fetch the latest Bicep CLI binary
curl -Lo bicep https://github.com/Azure/bicep/releases/latest/download/bicep-osx-x64
# Mark it as executable
chmod +x ./bicep
# Add Gatekeeper exception (requires admin)
sudo spctl --add ./bicep
# Add bicep to your PATH (requires admin)
sudo mv ./bicep /usr/local/bin/bicep
# Verify you can now access the 'bicep' command
bicep --help
# Done!

```

### <a name="windows"></a>Windows

#### <a name="windows-installer"></a>Windows Installer

[최신 Windows 설치 관리자](https://github.com/Azure/bicep/releases/latest/download/bicep-setup-win-x64.exe)를 다운로드하여 실행합니다. 설치 프로그램에는 관리자 권한이 필요하지 않습니다. 설치 후에 Bicep CLI가 사용자 PATH에 추가됩니다. PATH 변경 내용을 적용하려면 열려 있는 모든 명령 셸 창을 닫았다가 다시 엽니다.

#### <a name="chocolatey"></a>Chocolatey

```powershell
choco install bicep
```

#### <a name="winget"></a>Winget

```powershell
winget install -e --id Microsoft.Bicep
```

#### <a name="manual-with-powershell"></a>PowerShell을 사용하여 수동으로

```powershell
# Create the install folder
$installPath = "$env:USERPROFILE\.bicep"
$installDir = New-Item -ItemType Directory -Path $installPath -Force
$installDir.Attributes += 'Hidden'
# Fetch the latest Bicep CLI binary
(New-Object Net.WebClient).DownloadFile("https://github.com/Azure/bicep/releases/latest/download/bicep-win-x64.exe", "$installPath\bicep.exe")
# Add bicep to your PATH
$currentPath = (Get-Item -path "HKCU:\Environment" ).GetValue('Path', '', 'DoNotExpandEnvironmentNames')
if (-not $currentPath.Contains("%USERPROFILE%\.bicep")) { setx PATH ($currentPath + ";%USERPROFILE%\.bicep") }
if (-not $env:path.Contains($installPath)) { $env:path += ";$installPath" }
# Verify you can now access the 'bicep' command.
bicep --help
# Done!
```

## <a name="install-on-air-gapped-cloud"></a>에어 맵이 있는 클라우드에 설치

Bicep CLI를 에어 갭 환경에 설치하려면 Bicep CLI 실행 파일을 수동으로 다운로드하여 특정 위치에 저장해야 합니다.

- **Linux**

    1. 비 에어 갭 환경의 [Bicep 릴리스 페이지](https://github.com/Azure/bicep/releases/latest/)에서 **bicep-linux-x64** 를 다운로드합니다.
    1. 실행 파일을 에어 갭 컴퓨터의 **$HOME/.azure/bin** 디렉터리에 복사합니다.

- **macOS**

    1. 비 에어 갭 환경의 [Bicep 릴리스 페이지](https://github.com/Azure/bicep/releases/latest/)에서 **bicep-osx-x64** 를 다운로드합니다.
    1. 실행 파일을 에어 갭 컴퓨터의 **$HOME/.azure/bin** 디렉터리에 복사합니다.

- **Windows**

    1. 비 에어 갭 환경의 [Bicep 릴리스 페이지](https://github.com/Azure/bicep/releases/latest/)에서 **bicep-win-x64.exe** 를 다운로드합니다.
    1. 실행 파일을 에어 갭 컴퓨터의 **%UserProfile%/.azure/bin** 디렉터리에 복사합니다.

`bicep install`공 `bicep upgrade` 간격이 있는 환경에서는 및 명령이 작동하지 않습니다.

## <a name="install-the-nightly-builds"></a>야간 빌드 설치

릴리스 전에 Bicep의 시험판 비트를 사용해 보려는 경우 [야간 빌드 설치](https://github.com/Azure/bicep/blob/main/docs/installing-nightly.md)를 참조하세요.

> [!WARNING]
> 이러한 시험판 빌드에는 알려진 버그 또는 알려지지 않은 버그가 있을 가능성이 훨씬 높습니다.

## <a name="next-steps"></a>다음 단계

Visual Studio Code 및 Bicep 확장 사용에 대한 자세한 내용은 [빠른 시작: Visual Studio Code를 사용하여 Bicep 파일 만들기](./quickstart-create-bicep-use-visual-studio-code.md)를 참조하세요.
