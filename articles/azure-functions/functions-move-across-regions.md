---
title: Azure Functions에서 지역 간에 함수 앱 이동
description: 대상 지역에 기존 Azure 함수 리소스의 복사본을 만들어 한 지역에서 다른 지역으로 Azure Functions 리소스를 이동 하는 방법을 알아봅니다.
ms.topic: how-to
ms.service: azure-functions
author: nzthiago
ms.date: 11/09/2021
ms.custom: subject-moving-resources
ms.openlocfilehash: 7b5fffa371ef8457f0a25605cc2f975386693bb7
ms.sourcegitcommit: e9e332a512ed615a3c8ad5a11baa21649f14116d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133097344"
---
# <a name="move-your-function-app-between-regions-in-azure-functions"></a>Azure Functions에서 지역 간에 함수 앱 이동

이 문서에서는 Azure Functions 리소스를 다른 Azure 지역으로 이동 하는 방법을 설명 합니다. 다음 이유 중 하나로 리소스를 다른 지역으로 이동할 수 있습니다.
 + 새 Azure 지역 활용
 + 특정 지역 에서만 사용할 수 있는 기능 또는 서비스 배포
 + 내부 정책 및 거버넌스 요구 사항 충족
 + 용량 계획 요구 사항에 대응

Azure Functions 리소스는 지역에 따라 달라 지 며 지역 간에 이동할 수 없습니다. 대상 지역에 기존 함수 앱 리소스의 복사본을 만든 다음 함수 코드를 새 앱에 다시 배포 해야 합니다.

가동 중지 시간을 최소화 해야 하는 경우 재해 복구 아키텍처를 구현 하기 위해 두 지역 모두에서 함수 앱을 실행 하는 것이 좋습니다.
+ [Azure Functions 지리적 재해 복구](functions-geo-disaster-recovery.md)
+ [Azure Durable Functions의 재해 복구 및 지역 배포](durable/durable-functions-disaster-recovery-geo-distribution.md)

## <a name="prerequisites"></a>필수 구성 요소

+ 대상 지역이 이동 하려는 리소스의 Azure Functions 및 관련 서비스를 지원 하는지 확인 합니다.
+ 마이그레이션하려는 함수에 대 한 원래 소스 코드에 액세스할 수 있습니다.

## <a name="prepare"></a>준비

원본 지역에서 사용 되는 모든 함수 앱 리소스를 식별 합니다. 여기에는 다음이 포함 될 수 있습니다.

+ 함수 앱
+ [호스팅 계획](functions-scale.md#overview-of-plans)
+ [배포 슬롯](functions-deployment-slots.md)
+ [Azure에서 구매한 사용자 지정 도메인](../app-service/manage-custom-dns-buy-domain.md)
+ [TLS/SSL 인증서 및 설정](../app-service/configure-ssl-certificate.md)
+ [구성 된 네트워킹 옵션](functions-networking-options.md)
+ [관리 ID](../app-service/overview-managed-identity.md)
+ [구성 된 응용 프로그램 설정](functions-how-to-use-azure-function-app-settings.md) -충분 한 액세스 권한이 있는 사용자는 포털의 고급 편집 기능을 사용 하 여 모든 원본 응용 프로그램 설정을 복사할 수 있습니다.
+ [구성 크기 조정](functions-scale.md#scale)

함수는 트리거 또는 바인딩을 사용 하 여 다른 리소스에 연결할 수 있습니다. 해당 리소스를 지역 간에 이동하는 방법에 대한 자세한 내용은 각 서비스 설명서를 참조하세요.

또한 [기존 리소스에서 템플릿을 내보낼](../azure-resource-manager/templates/export-template-portal.md)수 있어야 합니다.

## <a name="move"></a>이동

대상 지역에 함수 앱을 배포 하 고 구성 된 리소스를 검토 합니다. 

### <a name="redeploy-function-app"></a>함수 앱 다시 배포

원본 지역에서 함수 앱을 만든 배포 및 automation 리소스에 대 한 액세스 권한이 있는 경우 대상 지역에서 동일한 배포 단계를 다시 실행 하 여 앱을 만들고 다시 배포 합니다. 

소스 코드에만 액세스할 수 있지만 배포 및 자동화 리소스는 액세스할 수 없는 경우 사용 가능한 [배포 기술](functions-deployment-technologies.md) 또는 [연속 배포 방법](functions-continuous-deployment.md)중 하나를 사용 하 여 대상 지역에서 함수 앱을 배포 하 고 구성할 수 있습니다.

### <a name="review-configured-resources"></a>구성 된 리소스 검토

배포 중에 구성 되지 않은 경우 대상 지역에서 위의 [준비](#prepare) 단계에서 식별 된 리소스를 검토 하 고 구성 합니다.

### <a name="move-considerations"></a>이동 고려 사항
+ 배포 리소스 및 자동화에서 함수 앱을 만들지 않는 경우 대상 지역의 [새 호스팅 계획에 동일한 유형의 앱을 만듭니다](functions-scale.md#overview-of-plans) .
+ 함수 앱 이름은 Azure에서 전역적으로 고유 하므로 대상 지역의 앱은 원본 지역의 앱과 동일한 이름을 가질 수 없습니다.
+ 함수 앱을 종속성에 연결 하는 참조 및 응용 프로그램 설정을 검토 하 고 필요한 경우 업데이트 해야 합니다. 예를 들어 함수가 호출 하는 데이터베이스를 이동 하는 경우 대상 지역의 데이터베이스에 연결 하도록 응용 프로그램 설정 또는 구성도 업데이트 해야 합니다. 함수 앱에서 사용 하는 Application Insights 계측 키 또는 Azure storage 계정과 같은 일부 응용 프로그램 설정은 이미 대상 지역에서 구성 될 수 있으며 업데이트할 필요가 없습니다.
+ 구성을 확인 하 고 대상 지역에서 함수를 테스트 해야 합니다.
+ 사용자 지정 도메인이 구성 된 경우 [도메인 이름을 다시 매핑합니다](../app-service/manage-custom-dns-migrate-domain.md#remap-the-active-dns-name) .
+ 웹 앱과 계획을 공유 하는 경우 전용 계획에서 실행 되는 함수의 경우에도 [App Service 마이그레이션 계획](../app-service/manage-move-across-regions.md) 을 검토 합니다.

## <a name="clean-up-source-resources"></a>원본 리소스 정리

이동이 완료 되 면 소스 지역에서 함수 앱 및 호스팅 계획을 삭제 합니다. 앱 자체가 실행 되지 않는 경우에도 Premium 또는 전용 계획의 함수 앱에 대 한 비용을 지불 합니다.

## <a name="next-steps"></a>다음 단계

+ [Azure 아키텍처 센터](/azure/architecture/browse/?expanded=azure&products=azure-functions) 를 검토 하 여 고급 솔루션 아키텍처의 일부로 여러 지역에서 실행 되는 Azure Functions의 예
