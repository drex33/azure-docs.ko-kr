---
title: Service Fabric Mesh에서 기존 .NET 앱 컨테이너화
description: 전체 .NET 프레임워크를 사용하는 ASP.NET 및 콘솔 프로젝트에 Service Fabric Mesh 컨테이너 오케스트레이션 지원을 추가합니다.
author: georgewallace
ms.author: gwallace
ms.date: 11/08/2018
ms.topic: conceptual
ms.openlocfilehash: b9c5053a2a49c942cc89bd50c65e13f3a2f8d9d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99625885"
---
# <a name="containerize-an-existing-net-app-for-service-fabric-mesh"></a>Service Fabric Mesh에서 기존 .NET 앱 컨테이너화

> [!IMPORTANT]
> Azure Service Fabric Mesh의 미리 보기가 사용 중지되었습니다. 새 배포는 더이상 Service Fabric Mesh API를 통해 허용되지 않습니다. 기존 배포에 대한 지원은 2021년 4월 28일까지 계속됩니다.
> 
> 자세한 내용은 [Azure Service Fabric Mesh 미리 보기 사용 중지](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)를 참조하세요.

이 문서에서는 기존 .NET 앱에 Service Fabric Mesh 컨테이너 오케스트레이션 지원을 추가하는 방법을 보여줍니다.

Visual Studio 2017에서 전체 .NET Framework를 사용하는 ASP.NET 및 콘솔 프로젝트에 컨테이너화 지원을 추가할 수 있습니다.

> [!NOTE]
> 현재 .NET **Core** 프로젝트가 지원되지 않습니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독이 아직 없으면 시작하기 전에 [무료 계정을 만들](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 수 있습니다.

* [개발 환경이 설정](service-fabric-mesh-howto-setup-developer-environment-sdk.md)되었는지 확인합니다. Service Fabric 런타임, SDK, Docker, Visual Studio 2017을 설치하고 로컬 클러스터를 만드는 방법이 여기에 포함됩니다.

## <a name="open-an-existing-net-app"></a>기존 .NET 앱 열기

컨테이너 오케스트레이션 지원을 추가하려는 앱을 엽니다.

예제를 사용하려는 경우 [eShop](https://github.com/MikkelHegn/ContainersSFLab) 코드 샘플을 사용할 수 있습니다. 고유한 프로젝트에 다음 단계를 적용할 수 있지만 이 문서의 나머지 부분에서는 해당 프로젝트를 사용한다고 가정합니다.

**eShop** 프로젝트의 복사본을 가져옵니다.

```git
git clone https://github.com/MikkelHegn/ContainersSFLab.git
```

일단 다운로드하면 Visual Studio 2017에서 **ContainersSFLab\eShopLegacyWebFormsSolution\eShopLegacyWebForms.sln** 을 엽니다.

## <a name="add-container-support"></a>컨테이너 지원 추가
 
Service Fabric Mesh 도구를 사용하여 기존 ASP.NET 또는 콘솔 프로젝트에 컨테이너 오케스트레이션 지원을 추가합니다.

Visual Studio 솔루션 탐색기에서 프로젝트 이름(예: **eShopLegacyWebForms**)을 마우스 오른쪽 단추로 클릭한 다음, **추가** > **컨테이너 오케스트레이터 지원** 을 선택합니다.
**컨테이너 오케스트레이터 지원 추가** 대화 상자가 표시됩니다.

![Visual Studio 추가 컨테이너 오케스트레이터 대화 상자](./media/service-fabric-mesh-howto-containerize-vs/add-container-orchestration-support.png)

드롭다운 목록에서 **Service Fabric Mesh** 를 선택하고 **확인** 을 클릭합니다.


>[!NOTE]
> 2020년 11월 2일부터 Docker Free 계획 계정에서 Docker Hub에 대한 익명 및 인증된 요청에 [다운로드 속도 제한이 적용](https://docs.docker.com/docker-hub/download-rate-limit/)되며 IP 주소에 의해 적용됩니다. 자세한 내용은 [Docker Hub를 사용하여 인증](../container-registry/buffer-gate-public-content.md#authenticate-with-docker-hub)을 참조하세요.
>
> 요금 제한을 방지하려면 Dockerfile의 기본 `FROM microsoft/aspnet:4.7.2-windowsservercore-1803 AS base` 가 다음으로 바뀌었는지 확인합니다. `FROM mcr.microsoft.com/dotnet/framework/aspnet:4.7.2-windowsservercore-1803 AS base`

이 도구는 Docker가 설치되고, 프로젝트에 Dockerfile을 추가하고, 프로젝트의 Docker 이미지를 끌어왔는지 확인합니다.  
Service Fabric Mesh 애플리케이션 프로젝트를 솔루션에 추가합니다. Mesh 게시 프로필 및 구성 파일이 포함됩니다. 프로젝트의 이름은 사용자 프로젝트 이름의 끝에 'Application'을 연결한 것과 같습니다(예: **eShopLegacyWebFormsApplication**). 

새 Mesh 프로젝트에서 고려해야 하는 두 개의 폴더가 표시됩니다.
- **앱 리소스** 에는 네트워크 등의 추가 Mesh 리소스를 설명하는 YAML 파일이 포함되어 있습니다.
- **서비스 리소스** 에는 앱을 배포할 때 실행해야 하는 방법을 설명하는 service.yaml 파일이 포함되어 있습니다.

컨테이너 오케스트레이션 지원을 앱에 추가하면 **F5** 키를 눌러 로컬 Service Fabric Mesh 클러스터에서 .NET 앱을 디버그할 수 있습니다. Service Fabric Mesh 클러스터에서 실행 중인 eShop ASP.NET 앱은 다음과 같습니다. 

![eShop 앱](./media/service-fabric-mesh-howto-containerize-vs/eshop-running.png)

이제 Azure Service Fabric Mesh에 앱을 게시할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Service Fabric Mesh에 앱을 게시하는 방법은 [자습서 - Service Fabric Mesh 애플리케이션 배포](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)를 참조하세요.