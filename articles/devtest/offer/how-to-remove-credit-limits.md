---
title: 신용 한도 제거 및 Azure 개발/테스트 제안 변경
description: 신용 한도를 제거 하 고 Azure 개발/테스트 제품을 변경 하는 방법을 설명 합니다. 종 량 제에서 다른 제품으로 전환 합니다.
author: jamestramel
ms.author: jametra
ms.prod: visual-studio-windows
ms.topic: how-to
ms.date: 10/04/2021
ms.custom: devtestoffer
ms.openlocfilehash: ddeff5d88aa69af7d4958bddb90ad4add61170b4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131102950"
---
# <a name="changing-azure-devtest-offers--remove-limits"></a>Azure DevTest 제품 변경 & 제거 제한

현재 종 량 제 요금이 있는 구독이 있는 경우 Azure 구독을 다른 제품으로 전환할 수 있습니다. 제품을 변경 하는 이유는 조직 구조에 따라 달라질 수 있습니다. 하지만 몇 가지 일반적인 이유는 구독 제품을 변경 하거나 신용 한도를 제거 하는 것입니다.  

- 신용 한도를 통해 신용 카드를 청구 하지 않고도 실험, 테스트 등을 실행할 수 있습니다. 서비스를 실행 하는 경우 비용을 신속 하 게 사용할 수 있습니다. 신용 한도는 구독 주위의 가드 레일을 배치 합니다. 원하는 아키텍처를 만들어 과도 하 게 지출 하지 않고 필요에 따라 실행할 수 있습니다.  
- 다른 조직이 나 조직 내의 변경 내용으로 작업 하려면 제품 유형을 변경 해야 할 수 있습니다.  
- 현재 제품 유형을 수정 해야 하는 Azure 구독에 대 한 요구 사항이 변경 될 수 있습니다. 프로젝트에서 작업 하는 방법을 변경 해야 할 수도 있습니다. 이 경우 필요한 서비스를 실행 하려면 신용 한도 구조에서 종 량 제 구조체로 전환 해야 할 수 있습니다.  
- 중요 한 프로젝트를 실행 하 고 더 많은 시간이 필요 합니다. $150 또는 $50 제한 보다 약간 더 필요한 경우 제한을 제거할 수 있습니다.  
- 현재 제품이 만료 되었습니다.  

특정 제품 유형에 대해서는 구독 제품을 변경 하는 것이 쉽습니다. [Azure 제품 전환 설명서](../../cost-management-billing/manage/switch-azure-offer.md)를 따라 하거나 지원에 문의 하 여 추가 지원을 받을 수 있습니다.  

## <a name="understand-spending-limits-and-cost-management"></a>지출 한도 및 비용 관리 이해

Azure 월간 크레딧 구독 [비용 관리](../../cost-management-billing/costs/cost-mgt-best-practices.md) 는 클라우드 투자를 극대화 하기 위한 계획, 분석 및 비용 절감을 위한 도구를 제공 합니다. 이러한 도구는 지출 한도를 제거 하 고 다시 추가 하는 기능을 제공 합니다.  

Azure의 지출 한도는 과도 한 지출을 방지 합니다. 제한을 변경할 수는 없지만 지출 한도를 제거할 수는 있습니다. 한도 또는 한도는 크레딧 양과 제한이 없습니다.  

Azure 구독과 연결 된 유효한 지불 방법이 있는 경우 언제 든 지 [지출 한도를 제거할](../../cost-management-billing/manage/spending-limit.md) 수 있습니다.  

추가 Cost Management 문서:  

* [Cost Management 데이터 이해](../../cost-management-billing/costs/understand-cost-mgt-data.md)  
* [Azure Cost Management를 사용 하 여 클라우드 투자 최적화](../../cost-management-billing/costs/cost-mgt-best-practices.md)  

## <a name="payment-methods-and-overages"></a>지불 방법 및 과도 한 연령

한도를 제거 하거나 제품을 변경 하 [는 경우 새 크레딧을 업데이트 하거나 추가](../../cost-management-billing/manage/change-credit-card.md)해야 할 수 있습니다. 크레딧이 소진 될 때까지 신용 카드를 과도 하 게 사용할 수 없습니다.  

Azure Portal에서 기본 결제 방법을 새 신용 카드로 변경하고 신용 카드 세부 정보를 업데이트할 수 있습니다. 이러한 변경을 수행하려면 [계정 관리자](../../cost-management-billing/understand/subscription-transfer.md#whoisaa)이거나 올바른 [MCA 권한](../../cost-management-billing/manage/understand-mca-roles.md)이 있어야 합니다.  

삭제 신용 카드를 만들려면 [Azure 청구 지불 방법 삭제](../../cost-management-billing/manage/delete-azure-payment-method.md) 를 참조 하세요.  

신용 카드 대신 결제 방법을 확인/실시간 전송으로 업데이트할 수도 있습니다. 먼저 청구서로 지불 하는 승인이 필요 합니다. 승인 받으려면 [청구서 별 Azure 구독 요금 청구](../../cost-management-billing/manage/pay-by-invoice.md) 를 참조 하세요.  
