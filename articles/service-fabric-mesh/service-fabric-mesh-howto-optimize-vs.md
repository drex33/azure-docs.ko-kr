---
title: Azure Service Fabric Mesh에 대해 Visual Studio 최적화
description: 이 문서에서는 첫 번째 디버깅 실행(F5)이 더 신속하게 수행되도록 Service Fabric Mesh 프로젝트에서 Visual Studio 성능을 최적화하는 방법을 보여줍니다.
author: georgewallace
ms.author: gwallace
ms.date: 11/29/2018
ms.topic: conceptual
ms.openlocfilehash: e676286dca852c94f94e8bd8ff2ab73ee92b1412
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99625755"
---
# <a name="optimize-visual-studio-performance-for-service-fabric-mesh-projects"></a>Service Fabric Mesh 프로젝트에서 Visual Studio 성능 최적화

> [!IMPORTANT]
> Azure Service Fabric Mesh의 미리 보기가 사용 중지되었습니다. 새 배포는 더이상 Service Fabric Mesh API를 통해 허용되지 않습니다. 기존 배포에 대한 지원은 2021년 4월 28일까지 계속됩니다.
> 
> 자세한 내용은 [Azure Service Fabric Mesh 미리 보기 사용 중지](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)를 참조하세요.

이 문서에서는 첫 번째 디버깅 실행(F5)이 더 신속하게 수행되도록 Service Fabric Mesh 프로젝트에서 Visual Studio 성능을 최적화하는 방법을 보여줍니다.  

## <a name="change-visual-studio-settings"></a>Visual Studio 설정 변경
 
Visual Studio의 **도구** > **옵션**  > **Service Fabric Mesh Tools** > **일반** 에서 다음 설정을 조정할 수 있습니다.

- **열린 프로젝트에서 필수 Docker 이미지 풀링** 에서는 프로젝트가 로드되는 동안 이미지 다운로드 프로세스를 시작하여 첫 번째 디버깅 실행(F5)을 더 신속하게 수행할 수 있습니다.  
- **열린 프로젝트에서 애플리케이션 배포** 에서는 프로젝트가 열린 후에 배포 프로세스를 시작하여 첫 번째 디버깅 실행(F5)을 더 신속하게 수행할 수 있습니다.  
- **닫힌 프로젝트에서 애플리케이션 제거** 에서는 프로젝트가 닫힐 때 메시 앱을 제거하여 앱에 할당된 리소스(CPU, RAM)를 회수합니다.  

Visual Studio에서 '이미지를 끌어오거나', '준비하거나', '애플리케이션을 제거하는' Service Fabric 도구 출력 창에 메시지가 표시되면 위의 설정에 대한 참조에 것입니다. 이러한 설정을 해제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[메시 앱 디버그 자습서](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md) 읽기