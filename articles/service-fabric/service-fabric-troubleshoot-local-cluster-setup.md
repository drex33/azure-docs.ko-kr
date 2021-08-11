---
title: 로컬 Azure Service Fabric 클러스터 설정 문제 해결
description: 이 문서에서는 로컬 개발 클러스터 문제 해결을 위한 여러 제안 사항을 다룹니다.
ms.topic: conceptual
ms.date: 02/23/2018
ms.openlocfilehash: 20948cd1626c02d73fb6e9ef096b552bbab627fb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96575910"
---
# <a name="troubleshoot-your-local-development-cluster-setup"></a>로컬 개발 클러스터 설정 문제 해결
로컬 Azure 서비스 패브릭 개발 클러스터와 상호 작용하는 동안 문제가 발생할 경우 다음 제안 사항에서 잠재적인 해결 방법이 있는지 검토하세요.

## <a name="cluster-setup-failures"></a>클러스터 설정 오류
### <a name="cannot-clean-up-service-fabric-logs"></a>Service Fabric 로그를 정리할 수 없습니다.
#### <a name="problem"></a>문제
DevClusterSetup 스크립트를 실행하는 동안 다음과 같은 오류가 나타날 수 있습니다.

```output
Cannot clean up C:\SfDevCluster\Log fully as references are likely being held to items in it. Please remove those and run this script again.
At line:1 char:1 + .\DevClusterSetup.ps1
+ ~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo : NotSpecified: (:) [Write-Error], WriteErrorException
+ FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,DevClusterSetup.ps1
```

#### <a name="solution"></a>솔루션
현재 Powershell 창을 닫고 관리자 권한으로 새 Powershell 창을 엽니다. 이제는 스크립트를 성공적으로 실행할 수 있습니다.

## <a name="cluster-connection-failures"></a>클러스터 연결 오류

### <a name="type-initialization-exception"></a>형식 초기화 예외
#### <a name="problem"></a>문제
PowerShell에서 클러스터에 연결할 때 System.Fabric.Common.AppTrace에 대해 TypeInitializationException 오류가 표시됩니다.

#### <a name="solution"></a>솔루션
설치하는 동안 경로 변수가 올바르게 설정되지 않았습니다. Windows에서 로그아웃하고 다시 로그인합니다. 경로를 새로 고칩니다.

### <a name="cluster-connection-fails-with-object-is-closed"></a>“개체 닫힘"으로 인해 클러스터 연결 실패
#### <a name="problem"></a>문제
다음과 같은 오류와 함께 Connect-ServiceFabricCluster 호출에 실패합니다.

```output
Connect-ServiceFabricCluster : The object is closed.
At line:1 char:1
+ Connect-ServiceFabricCluster
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo : InvalidOperation: (:) [Connect-ServiceFabricCluster], FabricObjectClosedException
+ FullyQualifiedErrorId : CreateClusterConnectionErrorId,Microsoft.ServiceFabric.Powershell.ConnectCluster
```

#### <a name="solution"></a>솔루션
현재 Powershell 창을 닫고 관리자 권한으로 새 Powershell 창을 엽니다.

### <a name="fabric-connection-denied-exception"></a>패브릭 연결 거부 예외
#### <a name="problem"></a>문제
Visual Studio에서 디버그 시 FabricConnectionDeniedException 오류가 나타납니다.

#### <a name="solution"></a>솔루션
이 오류는 일반적으로 서비스 호스트 프로세스를 수동으로 시작하려고 할 때 발생합니다.

솔루션에서 시작 프로젝트로 설정된 서비스 프로젝트가 없어야 합니다. Service Fabric 애플리케이션 프로젝트만 시작 프로젝트로 설정되어야 합니다.

> [!TIP]
> 설치를 수행할 때 로컬 클러스터가 비정상적으로 동작을 시작하는 경우 로컬 클러스터 관리자 시스템 트레이 애플리케이션을 사용하여 다시 설정할 수 있습니다. 기존 클러스터를 제거하고 새로 설정합니다. 배포된 모든 애플리케이션 및 연결된 데이터가 제거됩니다.
> 
> 

## <a name="next-steps"></a>다음 단계
* [시스템 상태 보고서와 함께 클러스터 이해 및 문제 해결](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
* [서비스 패브릭 탐색기로 클러스터 시각화](service-fabric-visualizing-your-cluster.md)

