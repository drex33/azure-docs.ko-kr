---
title: Azure 비정상 스튜디오를 사용 하 여 네트워크 보안 그룹 오류를 사용 하 여 인터넷 DNS 중단 복제
description: 네트워크 보안 그룹 오류를 사용 하 여 DNS 중단을 만들어 비정상 스튜디오를 시작 하세요.
services: chaos-studio
author: johnkemnetz
ms.topic: article
ms.date: 08/26/2021
ms.author: johnkem
ms.service: chaos-studio
ms.custom: ignite-fall-2021
ms.openlocfilehash: dc7a04c7bd20f1b778aa23f163b57161c0fe7745
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053757"
---
# <a name="quickstart-internet-dns-outage-using-network-security-group-fault"></a>빠른 시작: 네트워크 보안 그룹 오류를 사용 하 여 인터넷 DNS 중단

네트워크 보안 그룹 결함을 사용 하면 비정상 실험의 일부로 기존 네트워크 보안 그룹 규칙을 수정할 수 있습니다. 이 오류를 사용 하 여 Azure 리소스에 대 한 네트워크 트래픽을 차단 하 여 종속 리소스의 연결 또는 중단의 손실을 시뮬레이션할 수 있습니다. 이 빠른 시작에서는 15 분 동안 외부 (인터넷) DNS 서버에 대 한 모든 트래픽을 차단 하는 비정상 실험을 만듭니다. 이 실험을 사용 하 여 대상 네트워크 보안 그룹과 연결 된 Azure 가상 네트워크에 연결 된 리소스가 외부 DNS 서버에 종속 되지 않도록 할 수 있습니다 .이를 통해 위험 위협 모델 요구 사항 중 하나를 확인할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- 실험에서 대상으로 하려는 Azure 리소스와 연결 된 네트워크 보안 그룹입니다.  
- [Azure Cloud Shell](../cloud-shell/quickstart.md)에서 Bash 환경을 사용 합니다.

## <a name="create-the-network-security-group-fault-provider"></a>네트워크 보안 그룹 오류 공급자 만들기

먼저 비정상 스튜디오에서 네트워크 보안 그룹을 조작할 수 있도록 호스트 되는 구독에 오류 공급자를 등록 합니다.

1. 다음 내용을 사용 하 여 **AzureNetworkSecurityGroupChaos** 이라는 파일을 만들고 로컬 컴퓨터에 저장 합니다.

      ```json
      { 
        "properties": {
          "enabled": true,
          "providerConfiguration": {
            "type": "AzureNetworkSecurityGroupChaos"
          }
        }
      }
      ```

1. [Cloud Shell](https://shell.azure.com/)를 시작 합니다.
1. **$SUBSCRIPTION _ID** 를 실험에서 사용 하려는 네트워크 보안 그룹을 포함 하는 AZURE 구독 ID로 바꾸고 다음 명령을 실행 하 여 공급자가 올바른 구독에 등록 되었는지 확인 합니다.

    ```bash
    az account set --subscription $SUBSCRIPTION_ID
    ```

1. **AzureNetworkSecurityGroupChaos** 를 cloud shell 창으로 끌어서 놓아 파일을 업로드 합니다.
1. 이전 단계에서 사용 된 **$SUBSCRIPTION _ID** 을 바꾸고 다음 명령을 실행 하 여 AzureNetworkSecurityGroupChaos fault 공급자를 등록 합니다.

    ```bash
    az rest --method put --url "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/microsoft.chaos/chaosProviderConfigurations/AzureNetworkSecurityGroupChaos?api-version=2021-06-21-preview" --body @AzureNetworkSecurityGroupChaos.json --resource "https://management.azure.com"
    ```

1. 필드 더 이상 필요 하지 않으므로 이전에 만든 **AzureNetworkSecurityGroupChaos** 파일을 삭제 하 고 Cloud Shell를 닫습니다.

## <a name="create-a-chaos-experiment"></a>비정상 실험 만들기

네트워크 보안 그룹 오류 공급자를 만들었으면 이제 비정상 스튜디오에서 실험을 만들 수 있습니다.

1. 비정상 스튜디오 기능 플래그를 사용 하 여 Azure Portal를 엽니다.
    * 계정을 사용 하는 경우 @microsoft.com [이 링크를 클릭](https://ms.portal.azure.com/?microsoft_azure_chaos_assettypeoptions={%22chaosStudio%22:{%22options%22:%22%22},%22chaosExperiment%22:{%22options%22:%22%22}}&microsoft_azure_chaos=true)합니다.
    * 외부 계정을 사용 하는 경우 [이 링크를 클릭](https://portal.azure.com/?feature.customPortal=false&microsoft_azure_chaos_assettypeoptions={%22chaosStudio%22:{%22options%22:%22%22},%22chaosExperiment%22:{%22options%22:%22%22}})합니다.
1. **실험 추가** 를 클릭 합니다.

    ![Azure Portal에서 실험 추가](images/add-an-experiment.png)

1. 실험에 사용할 이름을 입력 하 고 실험을 만들 구독, 리소스 그룹 및 위치 (지역)를 선택 합니다.
1. 다음을 클릭 합니다. **실험 디자이너 >** 합니다.

    ![Azure Portal에서 실험 만들기](images/create-an-experiment-dns-outage.png)

1. **오류 추가** 를 클릭 합니다.
1. 오류 드롭다운 메뉴에서 **네트워크 보안 그룹 오류** 를 선택 합니다.

    ![Azure Portal에서 NSG 오류 선택](images/network-security-group-fault.png)

1. 다음 매개 변수를 채웁니다.

    | 매개 변수 | 값 |
    | -- | -- |
    | Duration | `15` |
    | Name | `block_internet_dns` |
    | 원본 주소 접두사 | `*` |
    | 대상 주소 접두사 | `Internet` |
    | Access | `Deny` |
    | 원본 포트 범위 | `*` |
    | 대상 포트 범위 | `53` |
    | 우선 순위 | `1001` |
    | 방향 | `Outbound` |

    ![Azure Portal의 네트워크 보안 그룹 매개 변수](images/qs-network-outage-dns-parameters.png)

> [!NOTE]
> 이름 및 우선 순위 필드는 대상 네트워크 보안 그룹의 기존 규칙에 이미 있는 경우 조정 해야 할 수 있습니다.

> [!NOTE]
> 인터넷에 대 한 포트 53 트래픽을 명시적으로 허용 하는 규칙이 있는 경우 우선 순위를 줄여야 할 수 있습니다.

1. **다음: 대상 리소스 >** 를 클릭 합니다.
1. 이 실험의 대상으로 지정할 네트워크 보안 그룹을 선택 합니다.
1. **추가** 를 클릭합니다.

    ![대상 리소스 선택](images/nsg-fault-targets.png)

1. **검토 + 만들기** 클릭

    ![실험 검토 및 만들기](images/review-create.png)

1. **생성**

    ![실험 만들기 단추](images/create.png)

## <a name="grant-the-chaos-experiment-access-to-the-network-security-group"></a>비정상 실험에 네트워크 보안 그룹에 대 한 액세스 권한 부여

안전 예방 조치로 모든 비정상 실험에 실험을 대상으로 하는 Azure 리소스에 대 한 액세스 권한을 부여 해야 합니다.

1. 실험에서 대상으로 지정 된 리소스로 이동 합니다.
1. **액세스 제어(IAM)** 클릭

    ![액세스 권한 수정](images/access-control.png)

1. **추가** 를 클릭합니다.

    ![추가 단추](images/add.png)

1. **역할 할당 추가** 클릭

    ![역할 할당 추가 단추](images/add-role-assignment.png)

1. **역할** 선택`Network Contributor`

    ![네트워크 참가자 선택](images/role-network-contributor.png)

1. **선택** 에서 비정상 실험의 이름을 입력 한 다음 클릭 합니다.

    ![실험 이름 선택](images/qs-network-outage-dns-select.png)

1. 페이지 맨 아래에 있는 **저장**

    ![변경 내용 저장](images/save-discard.png)

## <a name="run-the-chaos-experiment"></a>비정상 실험 실행

1. 비정상 스튜디오 기능 플래그를 사용 하 여 Azure Portal를 엽니다.
    * 계정을 사용 하는 경우 @microsoft.com [이 링크를 클릭](https://ms.portal.azure.com/?microsoft_azure_chaos_assettypeoptions={%22chaosStudio%22:{%22options%22:%22%22},%22chaosExperiment%22:{%22options%22:%22%22}}&microsoft_azure_chaos=true)합니다.
    * 외부 계정을 사용 하는 경우 [이 링크를 클릭](https://portal.azure.com/?feature.customPortal=false&microsoft_azure_chaos_assettypeoptions={%22chaosStudio%22:{%22options%22:%22%22},%22chaosExperiment%22:{%22options%22:%22%22}})합니다.
1. 실험 이름 옆의 확인란을 선택 하 고 **실험 시작** 을 클릭 합니다.

    ![실험 시작](images/qs-network-outage-dns-start.png)

1. **예** 를 클릭 하 여 비정상 실험을 시작 하 시겠습니까를 확인 합니다.

    ![실험을 시작할 것인지 확인 합니다.](images/start-experiment-confirmation.png)
1. 필드 실험의 실행 상태에 대 한 자세한 보기를 보려면 실험 이름을 클릭 합니다.
1. 실험을 시작한 후에는 기존 모니터링, 원격 분석 및/또는 로깅 도구를 사용 하 여 서비스에 대 한 실행 비정상 실험의 영향을 확인할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

이 실험을 계속 사용 하 고 삭제 하려는 경우 다음 단계를 수행 합니다.

비정상 스튜디오 기능 플래그를 사용 하 여 Azure Portal를 엽니다.
    * 계정을 사용 하는 경우 @microsoft.com [이 링크를 클릭](https://ms.portal.azure.com/?microsoft_azure_chaos_assettypeoptions={%22chaosStudio%22:{%22options%22:%22%22},%22chaosExperiment%22:{%22options%22:%22%22}}&microsoft_azure_chaos=true)합니다.
    * 외부 계정을 사용 하는 경우 [이 링크를 클릭](https://portal.azure.com/?feature.customPortal=false&microsoft_azure_chaos_assettypeoptions={%22chaosStudio%22:{%22options%22:%22%22},%22chaosExperiment%22:{%22options%22:%22%22}})합니다.
1. 실험 이름 옆의 확인란을 선택 하 고 **삭제** 를 클릭 합니다.
1. **예** 를 클릭 하 여 실험을 삭제할 것임을 확인 합니다.

네트워크 보안 그룹과 관련 된 오류를 계속 사용 하지 않으려는 경우 다음 단계를 수행 하세요.

1. [Cloud Shell](https://shell.azure.com/)를 시작 합니다.
1. **$SUBSCRIPTION _ID** 를 네트워크 보안 그룹 오류 공급자가 프로 비전 된 AZURE 구독 ID로 바꾸고 다음 명령을 실행 합니다.

    ```bash
    az rest --method delete --url "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/microsoft.chaos/chaosProviderConfigurations/AzureNetworkSecurityGroupChaos?api-version=2021-06-21-preview" --resource "https://management.azure.com"
    ```
