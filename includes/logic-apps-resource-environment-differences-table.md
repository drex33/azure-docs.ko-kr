---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 08/18/2021
ms.openlocfilehash: b4b4bc65520ba3a8d2bec3bc4f3d4184b672702f
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123108299"
---
| 리소스 유형 | 이점 | 리소스 공유 및 사용 | [가격 책정 및 대금 청구 모델](../articles/logic-apps/logic-apps-pricing.md) | [한도 관리](../articles/logic-apps/logic-apps-limits-and-config.md) |
|---------------|----------|----------------------------|---------------------------------------------------------------------------|-----------------------------------------------------------------------------|
| **논리 앱(사용)** <p><p>호스트 환경: 다중 테넌트 Azure Logic Apps | - 시작하기 가장 쉬움 <p><p>- 사용한 만큼 지불 <p><p>- 완전 관리형 | 단일 논리 앱에는 워크플로를 하나만 포함할 수 있습니다. <p><p>다중 테넌트에서 고객이 만든 논리 앱은 동일한 처리(컴퓨팅), 스토리지, 네트워크 등을 공유합니다. | [사용](../articles/logic-apps/logic-apps-pricing.md#consumption-pricing)(종량제) | Azure Logic Apps는 이러한 한도에 대한 기본값을 관리하지만 특정 한도에 대해 옵션이 있는 경우 이러한 값 중 일부를 변경할 수 있습니다. |
| **논리 앱(표준)** <p><p>호스트 환경: <br>단일 테넌트 Azure Logic Apps <p><p>**참고**: 시나리오에 컨테이너가 필요한 경우 [Azure Arc 지원 Logic Apps를 사용하여 단일 테넌트 기반 논리 앱을 만듭니다.](../articles/logic-apps/azure-arc-enabled-logic-apps-create-deploy-workflows.md) 자세한 내용은 [Azure Arc 지원 Logic Apps란?](../articles/logic-apps/azure-arc-enabled-logic-apps-overview.md)을 검토하세요. | - 단일 테넌트 Azure Logic Apps 런타임을 사용하여 Azure Functions를 실행할 수 있는 대부분의 곳에서 실행합니다. 배포 슬롯은 현재 지원되지 않습니다. <p><p>- 더 많은 기본 제공 커넥터로 처리량 증가 및 대규모 비용 절감 <p><p>- 런타임 및 성능 설정에 대한 제어 및 미세 조정 기능 증가 <p><p>- 가상 네트워크 및 프라이빗 엔드포인트에 대한 통합 지원 <p><p>- 자체 기본 제공 커넥터 생성 | 단일 논리 앱에는 여러 [상태 저장 및 상태 비저장](../articles/logic-apps/single-tenant-overview-compare.md#stateful-stateless) 워크플로가 있을 수 있습니다.  <p><p>단일 논리 앱 및 테넌트의 워크플로는 동일한 처리(컴퓨팅), 스토리지, 네트워크 등을 공유합니다. | [표준](../articles/logic-apps/logic-apps-pricing.md#standard-pricing), 선택한 가격 책정 계층의 호스팅 계획에 기반함 <p><p>[외부 스토리지](../articles/azure-functions/storage-considerations.md#storage-account-requirements)를 사용하는 상태 저장 워크플로를 실행하는 경우 Azure Logic Apps 런타임은 [Azure Storage 가격 책정](https://azure.microsoft.com/pricing/details/storage/)을 따르는 스토리지 트랜잭션을 만듭니다. | 시나리오의 요구 사항에 따라 다양한 한도에 대한 기본값을 변경할 수 있습니다. <p><p>**중요**: 일부 한도에는 하드 상한이 있습니다. Visual Studio Code에서 논리 앱 프로젝트 구성 파일의 기본 제한 값에 변경한 사항은 디자이너 환경에 표시되지 않습니다. 자세한 내용은 [단일 테넌트 Azure Logic Apps에서 논리 앱에 대한 앱 및 환경 설정 편집](../articles/logic-apps/edit-app-settings-host-settings.md)을 참조하세요. |
| **논리 앱(사용)** <p><p>호스트 환경: <br>ISE(통합 서비스 환경) | - 대량 워크로드를 위한 엔터프라이즈 규모 <p><p>- 가상 네트워크에 직접 연결되는 20개를 초과하는 ISE별 커넥터 <p><p>- 포함된 사용량 및 고객이 제어하는 크기 조정으로 예측 가능한 가격 책정 | 단일 논리 앱에는 워크플로를 하나만 포함할 수 있습니다. <p><p>동일한 환경의 논리 앱은 동일한 처리(컴퓨팅), 스토리지, 네트워크 등을 공유합니다. | [ISE](../articles/logic-apps/logic-apps-pricing.md#ise-pricing)(고정) | Azure Logic Apps는 이러한 한도에 대한 기본값을 관리하지만 특정 한도에 대해 옵션이 있는 경우 이러한 값 중 일부를 변경할 수 있습니다. |
||||||