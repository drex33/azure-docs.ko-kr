---
title: '빠른 시작: Azure Portal을 사용하여 첫 번째 컨테이너 앱 배포'
description: Azure Portal을 사용하여 첫 번째 애플리케이션을 Azure Container Apps 미리 보기에 배포합니다.
services: container-apps
author: cebundy
ms.service: container-apps
ms.topic: quickstart
ms.date: 11/09/2021
ms.author: v-bcatherine
ms.custom: ''
ms.openlocfilehash: e2f2bf2cafc60bca637bee3309f4b721965f30fd
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132710283"
---
# <a name="quickstart-deploy-your-first-container-app-using-the-azure-portal"></a>빠른 시작: Azure Portal을 사용하여 첫 번째 컨테이너 앱 배포

Azure Container Apps Preview를 사용하면 서버리스 플랫폼에서 마이크로서비스 및 컨테이너화된 애플리케이션을 실행할 수 있습니다. Container Apps를 사용하면 클라우드 인프라와 복잡한 컨테이너 오케스트레이터를 수동으로 구성해야 하는 걱정을 버리고 컨테이너를 실행하는 이점을 누릴 수 있습니다.

이 빠른 시작에서는 보안 Container Apps 환경을 만들고 Azure Portal을 사용하여 첫 번째 컨테이너 앱을 배포합니다.

## <a name="prerequisites"></a>필수 구성 요소

활성 구독이 있는 Azure 계정이 필요합니다. 계정이 아직 없는 경우 [체험 계정을 만들](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 수 있습니다.

## <a name="setup"></a>설정

먼저 [Azure Portal](https://portal.azure.com)에 로그인합니다.
<!--
Do we need to include steps to login?  Probably not..
-->

## <a name="create-a-container-app"></a>컨테이너 앱 만들기

컨테이너 앱을 만들려면 Azure Portal 홈 페이지에서 시작합니다.

1. 위쪽 검색 창에서 **Container Apps** 를 검색합니다.
1. 검색 결과에서 **Container Apps** 를 선택합니다.
1. **만들기** 단추를 선택합니다.

### <a name="basics-tab"></a>기본 사항 탭

*기본 사항* 탭에서 다음 작업을 수행합니다.

#### <a name="enter-project-details"></a>프로젝트 세부 정보 입력

| 설정 | 작업 |
|---|---|
| Subscription | Azure 구독을 선택합니다. |
| Resource group | **새로 만들기** 를 선택하고, **my-container-apps** 를 입력합니다. |
| 컨테이너 앱 이름 |  **my-container-app** 을 입력합니다. |

#### <a name="create-an-environment"></a>환경 만들기
 
1. *Container App 환경 만들기* 필드에서 **새로 만들기** 를 선택합니다.
1. *기본 사항* 탭의 *Container App 환경 만들기* 페이지에서 다음 값을 입력합니다.

    | 설정 | 값 |
    |---|---|
    | 환경 이름 | **my-environment** 를 입력합니다. | 
    | 지역 | **캐나다 중부** 를 선택합니다. |

1. **모니터링** 탭을 선택하여 Log Analytics 작업 영역을 만듭니다.
1. *Log Analytics 작업 영역* 필드에서 **새로 만들기** 를 선택합니다.
1. *새 Log Analytics 작업 영역 만들기* 대화 상자의 *이름* 필드에서 **my-container-apps-logs** 를 입력합니다.
  
    *위치* 필드는 *캐나다 중부* 로 미리 채워져 있습니다.
1. **확인** 을 선택합니다.
1. *Container App 환경 만들기* 페이지 아래쪽에서 **만들기** 단추를 선택합니다.
1. 페이지 아래쪽에서 **다음: 앱 설정** 단추를 선택합니다.

### <a name="app-settings-tab"></a>앱 설정 탭

*앱 설정* 탭에서 다음 작업을 수행합니다.

| 설정 | 작업 |
|---|---|
| 빠른 시작 이미지 사용 | 확인란을 **선택 취소** 합니다. |
| 이름 | **my-app** 을 입력합니다. <!-- I don't know what name to use --> |
| 이미지 원본 | **Docker Hub 또는 기타 레지스트리** 를 선택합니다. |
| 이미지 형식 | **공용** 을 선택합니다. |
| 레지스트리 로그인 서버 | `mcr.microsoft.com`를 입력합니다. |  
| 이미지 및 태그 | **/azuredocs/containerapps-helloworld:latest** 를 입력합니다. |

#### <a name="application-ingress-settings"></a>애플리케이션 수신 설정

| 설정 | 작업 |
|---|---|
| 수신 | **사용** 을 선택합니다. |
| 수신 표시 유형 | **외부** 를 선택합니다. |
| 대상 포트 | **80** 을 입력합니다. |

### <a name="deploying-the-container-app"></a>Container App 배포

1. 페이지 아래쪽에서 **검토 및 만들기** 단추를 선택합니다.  

    다음으로, Container App의 설정을 확인합니다. 오류가 없으면 *만들기* 단추가 사용하도록 설정됩니다.  

    오류가 있으면 오류가 포함된 탭이 빨간색 점으로 표시됩니다.  해당 탭으로 이동합니다. 오류가 포함된 필드가 빨간색으로 강조 표시되어 있습니다.  모든 오류가 해결되면 **검토 및 만들기** 를 다시 선택합니다.

1. **만들기** 를 선택합니다.

    *배포 진행 중* 이라는 메시지가 있는 페이지가 표시됩니다.  배포가 성공적으로 완료되면 *배포가 완료됨* 이라는 메시지가 표시됩니다.

### <a name="view-your-deployed-application"></a>배포된 애플리케이션 보기

**리소스로 이동** 을 선택하여 새 컨테이너 앱을 봅니다.  *애플리케이션 URL* 옆에 있는 링크를 선택하여 애플리케이션을 봅니다. 브라우저에 다음 메시지가 표시됩니다.

:::image type="content" source="media/get-started/azure-container-apps-quickstart.png" alt-text="첫 Azure Container Apps 배포.":::

## <a name="clean-up-resources"></a>리소스 정리

이 애플리케이션을 계속 사용하지 않으려면 리소스 그룹을 제거하여 Azure Container Apps 인스턴스 및 모든 관련 서비스를 삭제할 수 있습니다.

1. *개요* 섹션에서 **my-container-apps** 리소스 그룹을 선택합니다.
1. 리소스 그룹 *개요* 위쪽에서 **리소스 그룹 삭제** 단추를 선택합니다.
1. *"my-container-apps"를 삭제하시겠습니까?* 확인 대화 상자에서 **my-container-apps** 라는 리소스 그룹 이름을 입력합니다.
1. **삭제** 를 선택합니다.  
    리소스 그룹을 삭제하는 프로세스를 완료하는 데 몇 분 정도 걸릴 수 있습니다.


> [!TIP]
> 문제가 있나요? [Azure Container Apps 리포지토리](https://github.com/microsoft/azure-container-apps)에서 문제를 열어 GitHub에 알려주세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Container Apps의 환경](environment.md)
