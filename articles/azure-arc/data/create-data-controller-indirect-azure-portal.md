---
title: Azure Portal에서 간접 모드로 Azure Arc 데이터 컨트롤러 만들기
description: Azure Portal에서 간접 모드로 Azure Arc 데이터 컨트롤러 만들기
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: ce593d8f4e29ecbe0873381b2a1cf028f03e212b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529144"
---
# <a name="create-azure-arc-data-controller-from-azure-portal---indirect-connectivity-mode"></a>Azure Portal에서 Azure Arc 데이터 컨트롤러 만들기 - 간접 연결 모드


## <a name="introduction"></a>소개

Azure Portal을 사용하여 간접 연결 모드에서 Azure Arc 데이터 컨트롤러를 만들 수 있습니다.

Azure Arc를 만드는 환경 대부분은 만들거나 관리할 리소스가 Azure 인프라 외부에 있는 경우에도 Azure Portal에서 시작합니다. 이러한 경우, 특히 Azure와 사용자 환경 간에 직접 연결되지 않은 경우 사용자 환경 패턴은 Azure Portal을 사용하여 스크립트를 생성한 다음, 사용자 환경에서 다운로드하고 실행하여 Azure에 대한 보안 연결을 다시 설정하는 것입니다. 예를 들어 Azure Arc 지원 서버는 이 패턴에 따라 [Arc 지원 서버를 만듭니다](../servers/onboard-portal.md).

Azure Arc 지원 데이터 서비스의 간접 연결 모드를 사용하는 경우 Azure Portal을 사용하여 Notebook을 생성한 다음, Azure Data Studio에서 다운로드하여 Kubernetes 클러스터에 대해 실행할 수 있습니다. 

   [!INCLUDE [use-insider-azure-data-studio](includes/use-insider-azure-data-studio.md)]

직접 연결 모드를 사용하는 경우 Azure Portal에서 직접 데이터 컨트롤러를 프로비저닝할 수 있습니다. [연결 모드](connectivity.md)에 대해 자세히 알아볼 수 있습니다.

## <a name="use-the-azure-portal-to-create-an-azure-arc-data-controller"></a>Azure Portal을 사용하여 Azure Arc 데이터 컨트롤러 만들기

다음 단계에 따라 Azure Portal 및 Azure Data Studio를 사용하여 Azure Arc 데이터 컨트롤러를 만듭니다.

1. 먼저, [Azure Portal Marketplace](https://ms.portal.azure.com/#blade/Microsoft_Azure_Marketplace/MarketplaceOffersBlade/selectedMenuItemId/home/searchQuery/azure%20arc%20data%20controller)에 로그인합니다.  Marketplace 검색 결과가 필터링되어 ‘Azure Arc 데이터 컨트롤러’를 표시합니다.
1. 첫 번째 단계에서 검색 조건을 입력하지 않은 경우 검색 결과에 입력하고 ‘Azure Arc 데이터 컨트롤러’를 클릭합니다.
1. Marketplace에서 Azure 데이터 컨트롤러 타일을 선택합니다.
1. **만들기** 단추를 클릭합니다.
1. 간접 연결 모드를 선택합니다. [연결 모드 및 요구 사항](./connectivity.md)에 대해 자세히 알아봅니다. 
1. 요구 사항을 검토하여 Azure Arc 데이터 컨트롤러를 만들고 Azure Data Studio 및 kubectl 같이 누락된 필수 조건 소프트웨어를 설치합니다.
1. **다음: 데이터 컨트롤러 세부 정보** 단추를 클릭합니다.
1. Azure Portal에서 만든 다른 모든 리소스와 마찬가지로 구독, 리소스 그룹 및 Azure 위치를 선택합니다. 이 경우 선택하는 Azure 위치는 리소스에 대한 메타데이터가 저장될 위치입니다.  리소스 자체는 선택하는 모든 인프라에 생성됩니다. Azure 인프라에 있을 필요가 없습니다.
1. 데이터 컨트롤러의 이름을 입력합니다.

1. **Azure Studio에서 열기** 단추를 클릭합니다.
1. 다음 화면에는 선택 항목에 대한 요약과 생성되는 Notebook이 표시됩니다.  **Azure Data Studio의 열기 링크** 단추를 클릭하여 생성된 Notebook을 Azure Data Studio에서 열 수 있습니다.
1. Azure Data Studio에서 Notebook을 열고 위쪽에 있는 **모두 실행** 단추를 클릭합니다.
1. Notebook의 프롬프트 및 지침에 따라 데이터 컨트롤러 만들기를 완료합니다.

## <a name="monitoring-the-creation-status"></a>만들기 상태 모니터링

컨트롤러 만들기를 완료하려면 몇 분 정도 걸립니다. 다음 명령을 사용하여 다른 터미널 창에서 진행률을 모니터링할 수 있습니다.

> [!NOTE]
>  아래 예제 명령은 ‘arc’라는 이름으로 데이터 컨트롤러 및 Kubernetes 네임스페이스를 만들었다고 가정합니다.  다른 네임스페이스/데이터 컨트롤러 이름을 사용한 경우 ‘arc’를 해당 이름으로 바꿀 수 있습니다.

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

아래와 같은 명령을 실행하여 특정 Pod의 생성 상태를 확인할 수도 있습니다.  이 기능은 문제를 해결하는 데 특히 유용합니다.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>생성 문제 해결

생성과 관련한 문제가 발생하는 경우 [문제 해결 가이드](troubleshoot-guide.md)를 참조하세요.