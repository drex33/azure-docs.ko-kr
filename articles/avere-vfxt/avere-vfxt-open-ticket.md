---
title: Avere vFXT for Azure에 대한 지원을 받는 방법
description: Azure Portal을 통해 지원 티켓을 만들어 Avere vFXT for Azure를 배포하거나 사용하는 동안 발생할 수 있는 문제를 해결하는 방법을 알아봅니다.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 2a21e7628857eea002892c3395c3d8e426cb3ffa
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124829051"
---
# <a name="get-help-with-your-system"></a>시스템 지원 받기

Avere vFXT for Azure 시스템에 대한 도움이 필요한 경우 지원을 받는 방법은 다음과 같습니다.

* **Avere vFXT 문제** - [아래](#open-a-support-ticket-for-your-avere-vfxt)에서 설명한 대로 Azure Portal을 사용하여 Avere vFXT에 대한 지원 티켓을 엽니다.
* **할당량** - 할당량 관련 문제가 있으면 [할당량 증가를 요청](#request-a-quota-increase)합니다.
* **설명서 및 예제** - 이 설명서 또는 예제에 문제가 있으면, 문제가 있는 페이지에서 아래쪽으로 스크롤하고 **피드백** 섹션을 사용하여 기존 문제를 검색하고 필요한 경우 새 문제를 제출합니다.

## <a name="open-a-support-ticket-for-your-avere-vfxt"></a>Avere vFXT에 대한 지원 티켓 열기

Avere vFXT를 배포하거나 사용하는 동안 문제가 발생하면 Azure Portal을 통해 지원을 요청합니다.

다음 단계에 따라 지원 티켓의 태그가 클러스터의 리소스로 지정되었는지 확인합니다. 티켓에 태그를 지정하면 올바른 지원 리소스로 라우팅하는 데 도움이 됩니다.

1. [https://portal.azure.com](https://portal.azure.com)에서 **리소스 그룹** 을 선택합니다. 문제가 발생한 vFXT 클러스터가 포함된 리소스 그룹을 찾아서 Avere 클러스터 가상 머신 중 하나를 클릭합니다.

    ![원으로 표시된 특정 VM이 있는 Azure Portal 리소스 그룹 "개요" 패널의 스크린샷](media/avere-vfxt-ticket-vm.png)

1. VM 페이지의 왼쪽 패널에서 아래쪽으로 스크롤하고 **새 지원 요청** 을 클릭합니다.

    ![이전 스크린샷의 VM에 대한 Azure Portal VM 페이지의 스크린샷 왼쪽 메뉴가 하단으로 스크롤되고 '새 지원 요청'이 원으로 표시됩니다.](media/avere-vfxt-ticket-request.png)

1. 지원 요청의 첫 번째 페이지에서 문제 유형을 선택하고 올바른 구독이 선택되었는지 확인합니다.

   **서비스** 에서 **모든 서비스** 를 클릭하고 **저장소** 에서 **Avere vFXT** 를 선택합니다.

   간단한 요약을 추가하고 문제 유형을 선택합니다.

    ![Azure Portal에 있는 새 지원 요청 창의 스크린샷 기본 탭이 선택되어 있습니다. 화면 항목에는 문제 유형, 구독, 서비스, 요약 및 문제 유형이 포함됩니다.](media/ticket-basics.png)

   **다음** 을 클릭하여 계속합니다.

1. 지원 양식의 두 번째 페이지에는 요약 설명을 기반으로 문제를 해결하기 위한 제안 사항이 포함되어 있습니다. 지원 티켓을 여전히 생성해야 하는 경우 하단의 **다음** 단추를 클릭합니다.

   ![솔루션 탭이 선택된 새 지원 요청 화면의 스크린샷 가운데에 있는 텍스트 필드에는 '권장 솔루션'이라는 제목이 있으며 가능한 해결 방법을 설명합니다.](media/ticket-solutions.png)

1. 세 번째 페이지에서는 클러스터에 대한 정보, 문제가 발생한 시간, 심각도, 문의 방법이 포함된 세부 정보를 제공합니다. 정보를 입력하고 아래쪽에서 **다음** 단추를 클릭합니다.

   ![세부 정보 탭이 선택된 새 지원 요청 화면의 스크린샷 정보 필드는 '문제 세부 정보', '지원 방법' 및 '연락처 정보' 범주로 구성됩니다.](media/ticket-details.png)

1. 마지막 페이지의 정보를 검토하고 **만들기** 를 클릭합니다. 확인 및 티켓 번호가 사용자의 이메일 주소로 전송되고, 지원 담당자가 연락을 드립니다.

## <a name="request-a-quota-increase"></a>할당량 증가 요청

Avere vFXT for Azure를 배포하는 데 필요한 구성 요소를 확인하려면 [vFXT 클러스터에 대한 할당량](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)을 참조하세요. Azure Portal에서 [할당량 증가를 요청](../azure-portal/supportability/regional-quota-requests.md)할 수 있습니다.