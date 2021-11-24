---
title: Azure Portal을 통해 Apache Kafka for Confluent Cloud 만들기 - Azure 파트너 솔루션
description: 이 문서에서는 Azure Portal을 사용하여 Apache Kafka for Confluent Cloud 인스턴스를 만드는 방법을 설명합니다.
ms.service: partner-services
ms.topic: quickstart
ms.date: 06/07/2021
author: tfitzmac
ms.author: tomfitz
ms.custom: mode-portal
ms.openlocfilehash: 3c3b4a946ec8dba0444794fb60bc324413e4d11f
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133057783"
---
# <a name="quickstart-get-started-with-apache-kafka-for-confluent-cloud---azure-portal"></a>빠른 시작: Apache Kafka for Confluent Cloud 시작 - Azure Portal

이 빠른 시작에서는 Azure Portal을 사용하여 Apache Kafka for Confluent Cloud 인스턴스를 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 계정. 활성 Azure 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
- Azure 구독에 대한 _소유자_ 또는 _기여자_ 역할이 있어야 합니다. Azure와 Confluent 간의 통합은 _소유자_ 또는 _기여자_ 액세스 권한이 있는 사용자만 설정할 수 있습니다. 시작하기 전에 [적절한 액세스 권한이 있는지 확인합니다](../../role-based-access-control/check-access.md).

## <a name="find-offer"></a>제안 찾기

Azure Portal을 사용하여 Apache Kafka for Confluent Cloud 애플리케이션을 찾습니다.

1. 웹 브라우저에서 [Azure Portal](https://portal.azure.com/)로 이동한 후 로그인합니다.

1. 최근 세션에서 **Marketplace** 를 방문한 경우 사용 가능한 옵션에서 아이콘을 선택합니다. 그렇지 않으면 _Marketplace_ 를 검색합니다.

    :::image type="content" source="media/marketplace.png" alt-text="Marketplace 아이콘":::

1. **Marketplace** 페이지에서 원하는 계획 유형에 따라 두 가지 옵션이 제공됩니다. 종량제 요금제 또는 약정 요금제에 가입할 수 있습니다. 종량제는 공개적으로 사용할 수 있습니다. 약정 요금제는 프라이빗 제안에 대해 승인된 고객이 사용할 수 있습니다.

   - **종량제** 고객의 경우 _Apache Kafka on Confluent Cloud_ 를 검색합니다. Apache Kafka on Confluent Cloud에 대한 제안을 선택합니다.

     :::image type="content" source="media/search-pay-as-you-go.png" alt-text="Azure Marketplace 제안 검색":::

   - **약정** 고객의 경우 **프라이빗 제안 보기** 에 대한 링크를 선택합니다. 약정에 따라 최소 지출 금액에 가입해야 합니다. 이 옵션은 서비스가 오랫동안 필요한 경우에만 사용합니다.

     :::image type="content" source="media/view-private-offers.png" alt-text="프라이빗 제안 보기":::

     _Apache Kafka on Confluent Cloud_ 를 찾습니다.

     :::image type="content" source="media/select-from-private-offers.png" alt-text="프라이빗 제안 선택":::

## <a name="create-resource"></a>리소스 만들기

Apache Kafka on Confluent Cloud에 대한 제안을 선택하면 애플리케이션을 설정할 준비가 된 것입니다.

1. 이전 섹션에서 프라이빗 제안을 선택한 경우 계획 유형에 대한 두 가지 옵션이 제공됩니다.

    - Confluent Cloud - 종량제
    - 약정 - 약정 요금제의 경우

   프라이빗 제안을 선택하지 않은 경우 종량제 옵션만 제공됩니다.

   사용할 계획을 선택하고, **설정 + 구독** 을 선택합니다.

    :::image type="content" source="media/setup-subscribe.png" alt-text="설정 및 구독":::

1. **Confluent Cloud 리소스 만들기** 기본 사항 페이지에서 다음 값을 제공합니다. 완료되면 **다음: 태그** 를 선택합니다.

    :::image type="content" source="media/setup-basics.png" alt-text="Confluent Cloud 리소스를 설정하는 양식":::

    | 속성 | Description |
    | ---- | ---- |
    | **구독** | 드롭다운 메뉴에서 배포할 Azure 구독을 선택합니다. _소유자_ 또는 _기여자_ 액세스 권한이 있어야 합니다. |
    | **리소스 그룹** | 새 리소스 그룹을 만들지, 아니면 기존 리소스 그룹을 사용할지 여부를 지정합니다. 리소스 그룹은 Azure 솔루션에 관련된 리소스를 보유하는 컨테이너입니다. 자세한 내용은 [Azure Resource Manager 개요](../../azure-resource-manager/management/overview.md)를 참조하세요. |
    | **Confluent 조직 이름** | SaaS(Software as a Service) 리소스의 이름을 지정합니다. |
    | **지역** | 드롭다운 메뉴에서 다음 지역 중 하나를 선택합니다. <br/><br/> 오스트레일리아 동부, 캐나다 중부, 미국 중부, 미국 동부, 미국 동부 2, 프랑스 중부, 북유럽, 동남 아시아, 영국 남부, 미국 중서부, 서유럽, 미국 서부 2 |
    | **계획** | **종량제** 또는 **약정** 을 선택합니다. |
    | **청구 기간** | 선택한 청구 계획에 따라 미리 채워집니다. |
    | **가격** | 선택한 Confluent 계획에 따라 미리 채워집니다. |

1. **태그** 에서 리소스에 적용하려는 태그에 대한 **이름** 및  **값** 쌍을 제공합니다. 태그가 입력되면 **검토 + 만들기** 를 선택합니다.

    :::image type="content" source="media/setup-tags.png" alt-text="프로젝트 태그 추가":::

1. 제공한 설정을 검토합니다. 준비가 되면 **만들기** 를 선택합니다.

1. 리소스를 만드는 데 몇 분 정도 걸립니다. **알림** 에서 배포 상태를 볼 수 있습니다. 배포가 완료되면 리소스를 선택하여 **개요** 페이지를 확인합니다.

    :::image type="content" source="media/deployment-status.png" alt-text="배포 상태":::

   오류가 발생하면 [Apache Kafka for Confluent Cloud 솔루션 문제 해결](troubleshoot.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Confluent Cloud 리소스 관리](manage.md)
