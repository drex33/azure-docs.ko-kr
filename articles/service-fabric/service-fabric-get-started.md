---
title: Windows 개발 환경 설정
description: 런타임, SDK 및 도구를 설치하고 로컬 개발 클러스터를 만듭니다. 이 설정을 완료하면 Windows에서 애플리케이션을 빌드할 수 있습니다.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/16/2020
ms.custom: sfrev, devx-track-azurepowershell
ms.openlocfilehash: 8f520d74f8b3ccac8c31fce6fbf4af2016cb51ac
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130217718"
---
# <a name="prepare-your-development-environment-on-windows"></a>Windows에서 개발 환경 준비

> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md) 
> * [Linux](service-fabric-get-started-linux.md)
> * [Mac OS X](service-fabric-get-started-mac.md)
>
>

Windows 개발 컴퓨터에서 [Azure Service Fabric 애플리케이션][1]을 빌드 및 실행하려면 Service Fabric 런타임, SDK, 도구를 설치합니다. 또한 SDK에 포함된 [Windows PowerShell 스크립트의 실행을 사용하도록 설정](#enable-powershell-script-execution)해야 합니다.

## <a name="prerequisites"></a>필수 구성 요소

지원되는 [Windows 버전](service-fabric-versions.md#supported-windows-versions-and-support-end-date)을 사용하고 있는지 확인합니다.

## <a name="install-the-sdk-and-tools"></a>SDK 및 도구 설치

SDK와 도구를 설치하려면 WebPI(웹 플랫폼 설치 관리자)를 사용하는 것이 좋습니다. WebPI 사용 시에 런타임 오류가 발생하는 경우 특정 Service Fabric 릴리스의 릴리스 정보에서 설치 관리자의 직접 링크를 확인할 수도 있습니다. [Service Fabric 팀 블로그](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)의 여러 릴리스 공지에서 릴리스 정보를 확인할 수 있습니다.

> [!NOTE]
> 로컬 Service Fabric 개발 클러스터 업그레이드는 지원되지 않습니다.

### <a name="to-use-visual-studio-2017-or-2019"></a>Visual Studio 2017 또는 2019를 사용하려면

Service Fabric 도구는 Visual Studio 2019 및 2017의 Azure 개발 워크로드의 일부입니다. 이 워크로드를 Visual Studio 설치의 일부로 사용하도록 설정해야 합니다.
또한 웹 플랫폼 설치 관리자를 사용하여 Microsoft Azure Service Fabric SDK 및 런타임을 설치해야 합니다.

* [Microsoft Azure Service Fabric SDK 설치][core-sdk]

### <a name="sdk-installation-only"></a>SDK 설치만

SDK만 필요한 경우 다음 패키지를 설치할 수 있습니다.

* [Microsoft Azure Service Fabric SDK 설치][core-sdk]

현재 버전은 다음과 같습니다.

* Service Fabric SDK 및 도구 5.2.1235
* Service Fabric 런타임 8.2.1235

지원되는 버전 목록은 [Service Fabric 버전](service-fabric-versions.md)을 참조하세요.

> [!NOTE]
> 단일 컴퓨터 클러스터(OneBox)는 애플리케이션 또는 클러스터 업그레이드용으로 지원되지 않습니다. 클러스터 업그레이드를 수행해야 하거나 애플리케이션 업그레이드 수행 시에 문제가 발생하는 경우에는 OneBox 클러스터를 삭제한 후에 다시 만드세요. 

## <a name="enable-powershell-script-execution"></a>PowerShell 스크립트 실행 활성화

서비스 패브릭은 로컬 개발 클러스터를 만들고 Visual Studio에서 애플리케이션을 배포하기 위해 Windows PowerShell 스크립트를 사용합니다. 기본적으로 Windows에서는 이러한 스크립트의 실행을 차단합니다. 따라서 이러한 스크립트를 사용하려면 PowerShell 실행 정책을 수정해야 합니다. 관리자로 PowerShell을 열고 다음 명령을 입력합니다.

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## <a name="install-docker-optional"></a>Docker 설치(선택 사항)

Service Fabric은 머신 클러스터에 마이크로 서비스를 배포하기 위한 [컨테이너 오케스트레이터](service-fabric-containers-overview.md)입니다. 로컬 개발 클러스터에서 Windows 컨테이너 애플리케이션을 실행하려면 먼저 Windows용 Docker를 설치해야 합니다. [Windows용 Docker CE(안정화)](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description)를 가져옵니다. Docker를 설치하고 시작한 후에 트레이 아이콘을 마우스 오른쪽 단추로 클릭하고 **Windows 컨테이너로 전환** 을 선택합니다. 이 단계는 Windows 기반의 Docker 이미지를 실행하는 데 필요합니다.

## <a name="next-steps"></a>다음 단계

개발 환경의 설정을 마쳤으므로 앱을 빌드하고 실행하기 시작할 수 있습니다.

* [애플리케이션 만들기, 배포 및 관리하는 방법 알아보기](service-fabric-tutorial-create-dotnet-app.md)
* [프로그래밍 모델에 대해 알아보기: Reliable Services 및 Reliable Actors](service-fabric-choose-framework.md)
* [GitHub의 서비스 패브릭 코드 샘플 확인](/samples/browse/?products=azure)
* [서비스 패브릭 탐색기를 사용하여 클러스터 시각화](service-fabric-visualizing-your-cluster.md)
* [Windows에서 Linux 개발 환경 준비](service-fabric-local-linux-cluster-windows.md)
* [Service Fabric 지원 옵션](service-fabric-support.md) 알아보기

[1]: https://azure.microsoft.com/campaigns/service-fabric/ "Service Fabric 캠페인 페이지"
[2]: https://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[full-bundle-vs2015]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "VS 2015 WebPI 링크"
[full-bundle-dev15]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Dev15 WebPI 링크"
[core-sdk]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK "Core SDK WebPI 링크"
[powershell5-download]:https://www.microsoft.com/download/details.aspx?id=54616
