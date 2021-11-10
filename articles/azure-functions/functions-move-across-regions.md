---
title: Azure Functions 지역 간에 함수 앱 이동
description: 대상 지역에 기존 Azure Function 리소스의 복사본을 만들어 Azure Functions 리소스를 한 지역에서 다른 지역으로 이동하는 방법을 알아봅니다.
ms.topic: how-to
ms.service: azure-functions
author: nzthiago
ms.date: 05/11/2021
ms.custom: subject-moving-resources
ms.openlocfilehash: c106ec2e5e4592937974f040e1ae14aabc45f354
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132139937"
---
# <a name="move-your-function-app-between-regions-in-azure-functions"></a>Azure Functions 지역 간에 함수 앱 이동

이 문서에서는 Azure Functions 리소스를 다른 Azure 지역으로 이동하는 방법을 설명합니다. 다음 이유 중 하나로 리소스를 다른 지역으로 이동할 수 있습니다.
 + 새 Azure 지역 활용
 + 특정 지역에서만 사용할 수 있는 기능 또는 서비스 배포
 + 내부 정책 및 거버넌스 요구 사항 충족
 + 용량 계획 요구 사항에 응답

Azure Functions 리소스는 지역별로 지정되며 지역 간에 이동할 수 없습니다. 대상 지역에 기존 함수 앱 리소스의 복사본을 만든 다음, 함수 코드를 새 앱에 다시 배포해야 합니다.

최소 가동 중지 시간이 요구되는 경우 두 지역에서 함수 앱을 실행하여 재해 복구 아키텍처를 구현하는 것이 좋습니다.
+ [Azure Functions 지리적 재해 복구](functions-geo-disaster-recovery.md)
+ [Azure Durable Functions의 재해 복구 및 지역 배포](durable/durable-functions-disaster-recovery-geo-distribution.md)

## <a name="prerequisites"></a>필수 조건

+ 대상 지역에서 Azure Functions 리소스를 이동하려는 관련 서비스를 지원하는지 확인합니다.
+ 마이그레이션하는 함수의 원래 소스 코드에 액세스할 수 있습니다.

## <a name="prepare"></a>준비

원본 지역에서 사용되는 모든 함수 앱 리소스를 식별합니다. 여기에는 다음이 포함될 수 있습니다.

+ 함수 앱
+ [호스팅 계획](functions-scale.md#overview-of-plans)
+ [배포 슬롯](functions-deployment-slots.md)
+ [Azure에서 구매한 사용자 지정 도메인](../app-service/manage-custom-dns-buy-domain.md)
+ [TLS/SSL 인증서 및 설정](../app-service/configure-ssl-certificate.md)
+ [구성된 네트워킹 옵션](functions-networking-options.md)
+ [관리 ID](../app-service/overview-managed-identity.md)
+ [구성된 애플리케이션 설정](functions-how-to-use-azure-function-app-settings.md) - 충분한 액세스 권한이 있는 사용자는 포털에서 고급 편집 기능을 사용하여 모든 원본 애플리케이션 설정을 복사할 수 있습니다.
+ [구성 크기 조정](functions-scale.md#scale)

함수는 트리거 또는 바인딩을 사용하여 다른 리소스에 연결할 수 있습니다. 해당 리소스를 지역 간에 이동하는 방법에 대한 자세한 내용은 각 서비스 설명서를 참조하세요.

기존 리소스 에서 [템플릿을 내보낼](../azure-resource-manager/templates/export-template-portal.md)수도 있습니다.

## <a name="move"></a>이동

대상 지역에 함수 앱을 배포하고 구성된 리소스를 검토합니다. 

### <a name="redeploy-function-app"></a>함수 앱 다시 배포

원본 지역에서 함수 앱을 만든 배포 및 자동화 리소스에 액세스할 수 있는 경우 대상 지역에서 동일한 배포 단계를 다시 실행하여 앱을 만들고 다시 배포합니다. 

소스 코드에만 액세스할 수 있지만 배포 및 자동화 리소스에 액세스할 수 없는 경우 사용 가능한 [배포 기술](functions-deployment-technologies.md) 중 하나를 사용하거나 지속적인 배포 방법 중 하나를 사용하여 대상 지역에 함수 앱을 [배포하고](functions-continuous-deployment.md)구성할 수 있습니다.

### <a name="review-configured-resources"></a>구성된 리소스 검토

배포하는 동안 구성되지 않은 경우 대상 지역의 위의 [준비](#prepare) 단계에서 식별된 리소스를 검토하고 구성합니다.

### <a name="move-considerations"></a>이동 고려 사항
+ 배포 리소스 및 자동화에서 함수 앱을 만들지 않는 경우 대상 지역의 [새 호스팅 계획에 동일한 유형의 앱을 만듭니다.](functions-scale.md#overview-of-plans)
+ 함수 앱 이름은 Azure에서 전역적으로 고유하므로 대상 지역의 앱 이름은 원본 지역의 이름과 같을 수 없습니다.
+ 함수 앱을 dependencies에 연결하는 참조 및 애플리케이션 설정을 검토하고 필요한 경우 업데이트해야 합니다. 예를 들어 함수가 호출하는 데이터베이스를 이동하는 경우 대상 지역의 데이터베이스에 연결하도록 애플리케이션 설정 또는 구성도 업데이트해야 합니다. 애플리케이션 Insights 계측 키 또는 함수 앱에서 사용하는 Azure Storage 계정과 같은 일부 애플리케이션 설정은 대상 지역에서 이미 구성될 수 있으며 업데이트할 필요가 없습니다.
+ 구성을 확인하고 대상 지역에서 함수를 테스트해야 합니다.
+ 사용자 지정 도메인을 구성한 경우 [도메인 이름을 다시 매칭합니다.](../app-service/manage-custom-dns-migrate-domain.md#remap-the-active-dns-name)
+ 전용 계획에서 실행되는 함수의 경우 계획이 웹앱과 공유되는 경우 [App Service 마이그레이션 계획도](../app-service/manage-move-across-regions.md) 검토합니다.

## <a name="clean-up-source-resources"></a>원본 리소스 정리

이동이 완료되면 원본 지역에서 함수 앱 및 호스팅 계획을 삭제합니다. 앱 자체가 실행되고 있지 않은 경우에도 Premium 또는 전용 계획에서 함수 앱에 대한 비용을 지불합니다.

## <a name="next-steps"></a>다음 단계

+ [고급](/azure/architecture/browse/?expanded=azure&products=azure-functions) 솔루션 아키텍처의 일부로 여러 지역에서 실행되는 Azure Functions 예제는 Azure 아키텍처 센터 검토하세요.
