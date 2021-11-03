---
title: 네트워크 격리 및 Private Link -질문 답변
description: 사용자는 질문 응답 리소스에 대한 공용 액세스를 제한할 수 있습니다.
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: e16734ac0ff53f778fad368913d89bcea3869ee3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131103024"
---
#  <a name="network-isolation-and-private-endpoints"></a>네트워크 격리 및 프라이빗 엔드포인트

아래 단계에서는 질문 답변 리소스에 대한 공용 액세스를 제한하는 방법과 Azure Private Link 사용하도록 설정하는 방법을 설명합니다. [가상 네트워크를 구성](../../../cognitive-services-virtual-networks.md?tabs=portal)하여 퍼블릭 액세스에서 Cognitive Services 리소스를 보호합니다.

## <a name="private-endpoints"></a>프라이빗 엔드포인트

Azure 프라이빗 엔드포인트는 Azure Private Link가 제공하는, 서비스에 비공개로 안전하게 연결하는 네트워크 인터페이스입니다.  질문 답변은 Azure Search 서비스에 대한 프라이빗 엔드포인트를 만들 수 있도록 지원합니다.

프라이빗 엔드포인트는 [Azure Private Link](../../../../private-link/private-link-overview.md)에 의해 별도의 서비스로 제공됩니다. 비용에 대한 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/private-link/)를 참조하세요.

## <a name="steps-to-enable-private-endpoint"></a>프라이빗 엔드포인트를 사용하도록 설정하는 단계

1. Azure Search Service 인스턴스에서 언어 리소스에 *기여자* 역할을 할당합니다(컨텍스트에 따라 Text Analytics 리소스로 표시할 수 있음). 이 작업을 수행하려면 구독에 대한 *소유자* 권한이 있어야 합니다. 서비스 리소스의 ID 탭으로 이동하여 ID를 가져옵니다.

> [!div class="mx-imgBorder"]
> ![Text Analytics ID](../../../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoints-identity.png)

2. Azure Search Service IAM 탭으로 이동하여 위의 ID를 *기여자* 로 추가합니다.

![관리형 서비스 IAM](../../../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-access-control.png)

3. 역할 *할당 추가를* 선택하고 ID를 추가하고 *저장을* 선택합니다.

![관리형 역할 할당](../../../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-role-assignment.png)

4. 이제 Azure Search Service 인스턴스의 *네트워킹* 탭으로 이동하고 엔드포인트 연결 데이터를 *퍼블릭* 에서 *프라이빗* 으로 전환합니다. 이 작업은 장기 실행 프로세스이며, 완료하는 데 최대 30분이 걸릴 수 있습니다. 

![관리형 Azure 검색 네트워킹](../../../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking.png)

5. 언어 리소스의 *네트워킹* 탭으로 이동하고 *에서 액세스 허용 아래에서* 선택한 네트워크 및 프라이빗 엔드포인트 옵션을 선택하고 *저장을* 선택합니다. 
 
> [!div class="mx-imgBorder"]
> ![Text Analytics 네트워킹](../../../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking-custom-qna.png)

이렇게 하면 언어 리소스와 Azure Cognitive Search 서비스 인스턴스 간에 프라이빗 엔드포인트 연결이 설정됩니다. Azure Cognitive Search 서비스 인스턴스의 *네트워킹* 탭에서 프라이빗 엔드포인트 연결을 확인할 수 있습니다. 전체 작업이 완료되면 질문 답변이 활성화된 언어 리소스를 사용하는 것이 좋습니다.

![관리형 네트워킹 서비스](../../../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking-3.png)

## <a name="support-details"></a>지원 세부 정보
 * Text Analytics 서비스에 대한 프라이빗 액세스를 사용하도록 설정한 후에는 Azure Cognitive Search 서비스 변경이 지원되지 않습니다. 프라이빗 액세스를 사용하도록 설정한 후 '기능' 탭을 통해 Azure Cognitive Search 서비스를 변경하면 Text Analytics 서비스를 사용할 수 없게 됩니다.

 * 프라이빗 엔드포인트 연결을 설정한 후 Azure Cognitive Search Service 네트워킹을 'Public'으로 전환하면 Text Analytics 서비스를 사용할 수 없습니다. 프라이빗 엔드포인트 연결이 작동하려면 Azure Search 서비스 네트워킹이 '프라이빗'이어야 합니다.

## <a name="restrict-access-to-cognitive-search-resource"></a>Cognitive Search 리소스에 대한 액세스 제한

질문 응답 언어 리소스에 대한 공용 액세스를 제한하려면 아래 단계를 수행합니다. [가상 네트워크를 구성](../../../cognitive-services-virtual-networks.md?tabs=portal)하여 퍼블릭 액세스에서 Cognitive Services 리소스를 보호합니다.

VNet에 따라 Cognitive Service 리소스에 대한 액세스를 제한한 후 온-프레미스 네트워크 또는 로컬 브라우저에서 Language Studio 포털의 기술 자료를 찾아보려면.
- [온-프레미스 네트워크](../../../cognitive-services-virtual-networks.md?tabs=portal#configuring-access-from-on-premises-networks)에 대한 액세스 권한을 부여합니다.
- [로컬 브라우저/컴퓨터](../../../cognitive-services-virtual-networks.md?tabs=portal#managing-ip-network-rules)에 대한 액세스 권한을 부여합니다.
- **네트워킹** 탭의 **방화벽 섹션에서 컴퓨터의 공용 IP 주소** 를 추가합니다. 기본적으로 `portal.azure.com`은 현재 검색 시스템의 공용 IP를 표시하고(이 항목 선택) **저장** 을 선택합니다.

     > [!div class="mx-imgBorder"]
     > [![방화벽 및 가상 네트워크 구성 UI 스크린샷.]( ../../../qnamaker/media/network-isolation/firewall.png)](  ../../../qnamaker/media/network-isolation/firewall.png#lightbox)
