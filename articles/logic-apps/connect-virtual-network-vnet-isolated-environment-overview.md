---
title: 개요 - Azure 가상 네트워크에 액세스
description: ISE(통합 서비스 환경)를 사용하여 Azure Logic Apps에서 Azure VNET(가상 네트워크)에 액세스하는 방법 알아보기
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: conceptual
ms.date: 05/16/2021
ms.openlocfilehash: 6abcc030d77f9b7d06f9d5f43d32611a0670053b
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130162364"
---
# <a name="access-to-azure-virtual-networks-from-azure-logic-apps-using-an-integration-service-environment-ise"></a>ISE(통합 서비스 환경)를 사용하여 Azure Logic Apps에서 Azure 가상 네트워크에 액세스

논리 앱 워크플로가 Azure 가상 네트워크 안에 있거나 이 네트워크에 연결된 VM(가상 머신), 기타 시스템 또는 서비스와 같은 보호된 리소스에 액세스해야 하는 경우가 있습니다. 일반적으로 다중 테넌트 Azure Logic Apps에서 실행되는 워크플로에서 이러한 리소스에 직접 액세스하려면 대신 ISE(*통합 서비스 환경*)에서 논리 앱을 만들고 실행할 수 있습니다. ise는 실제로는 글로벌 다중 테 넌 트 Azure 환경과는 별도로 전용 리소스에서 별도로 실행 되는 Azure Logic Apps 인스턴스이고 [ise를 배포 하는 지역 외부에서 데이터를 저장, 처리 또는 복제](https://azure.microsoft.com/global-infrastructure/data-residency#select-geography)하지 않습니다.

예를 들어 일부 Azure 가상 네트워크는 Azure Storage, Azure Cosmos DB 또는 Azure SQL Database와 같은 Azure PaaS 서비스, 파트너 서비스 또는 Azure에서 호스트되는 고객 서비스에 액세스를 제공하기 위해 프라이빗 엔드포인트([Azure Private Link](../private-link/private-link-overview.md))를 사용합니다. 논리 앱 워크플로에서 프라이빗 엔드포인트를 사용하는 가상 네트워크에 액세스해야 하는 경우 다음과 같은 옵션이 있습니다.

* **논리 앱(소비)** 리소스 유형을 사용하여 워크플로를 개발하고 워크플로에서 프라이빗 엔드포인트를 사용해야 하는 경우 ISE에서 논리 앱을 *반드시* 생성, 배포 및 실행해야 합니다. 자세한 내용은 [ISE(통합 서비스 환경)를 사용하여 Azure Logic Apps에서 Azure 가상 네트워크에 연결](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)을 참조하세요.

* **논리 앱(표준)** 리소스 유형을 사용하여 워크플로를 개발하려는 경우 워크플로에서 프라이빗 엔드포인트를 사용해야 하는 경우 ISE가 필요하지 않습니다. 대신 워크플로는 인바운드 트래픽에 대해 프라이빗 엔드포인트를 사용하고 아웃바운드 트래픽에 대해 가상 네트워크 통합을 사용하여 가상 네트워크와 비공개로 안전하게 통신할 수 있습니다. 자세한 내용은 [프라이빗 엔드포인트를 사용하여 가상 네트워크와 단일 테넌트 Azure Logic Apps 간의 트래픽 보호](secure-single-tenant-workflow-virtual-network-private-endpoint.md)를 검토하세요.

자세한 내용은 [다중 테넌트 Azure Logic Apps와 통합 서비스 환경 간의 차이점](logic-apps-overview.md#resource-environment-differences)을 검토하세요.

## <a name="how-an-ise-works-with-a-virtual-network"></a>ISE가 가상 네트워크에서 작동하는 방식

ISE를 만들 때 Azure에서 ISE를 *주입* 하거나 배포할 Azure 가상 네트워크를 선택합니다. 이 가상 네트워크에 액세스해야 하는 논리 앱 및 통합 계정을 만들 때 ISE를 해당 논리 앱 및 통합 계정의 호스트 위치로 선택할 수 있습니다. ISE 내에서 논리 앱은 다중 테넌트 Azure Logic Apps 환경의 다른 리소스와 별도로 전용 리소스에서 실행됩니다. ISE의 데이터는 [해당 ISE를 만들고 배포하는 동일한 지역](https://azure.microsoft.com/global-infrastructure/data-residency/)에 유지됩니다.

![통합 서비스 환경 선택](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

Azure Storage에서 사용하는 암호화 키를 더 세밀하게 제어하려면 [Azure Key Vault](../key-vault/general/overview.md)를 사용하여 자체 키를 설정, 사용 및 관리할 수 있습니다. 이 기능은 BYOK("Bring Your Own Key")라고도 하며, 키는 "고객 관리형 키"라고 합니다. 자세한 내용은 [Azure Logic Apps의 ISE(통합 서비스 환경)에 대해 미사용 데이터를 암호화하도록 고객 관리형 키 설정](../logic-apps/customer-managed-keys-integration-service-environment.md)을 검토하세요.

이 개요에서는 [ISE를 사용해야 하는 이유](#benefits), [전용 테넌트 Logic Apps 서비스와 다중 테넌트 Logic Apps 서비스의 차이점](#difference), Azure 가상 네트워크 내부에 있거나 이 네트워크에 연결된 리소스에 직접 액세스하는 방법에 대한 추가 정보를 제공합니다.

<a name="benefits"></a>

## <a name="why-use-an-ise"></a>ISE를 사용하는 이유

고유한 별도의 전용 인스턴스에서 논리 앱을 실행하면 다른 Azure 테넌트가 앱 성능에 줄 수 있는 영향(["사용량이 많은 인접 항목" 효과](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors)로 알려짐)을 줄일 수 있습니다. ISE는 다음과 같은 이점도 제공합니다.

* 가상 네트워크 내부에 있거나 가상 네트워크에 연결된 리소스에 직접 액세스

  ISE에서 만들고 실행하는 논리 앱은 [ISE에서 실행되는 특별히 설계된 커넥터](../connectors/managed.md#ise-connectors)를 사용할 수 있습니다. 온-프레미스 시스템 또는 데이터 원본에 ISE 커넥터가 있는 경우 [온-프레미스 데이터 게이트웨이](../logic-apps/logic-apps-gateway-connection.md)를 사용하지 않고 직접 연결할 수 있습니다. 자세한 내용은 이 항목 뒷부분의 [전용 테넌트와 다중 테넌트 비교](#difference) 및 [온-프레미스 시스템에 액세스](#on-premises)를 참조하세요.

* 가상 네트워크 외부에 있거나 가상 네트워크에 연결되지 않은 리소스에 계속 액세스

  ISE에서 만들고 실행하는 논리 앱은 ISE 관련 커넥터를 사용할 수 없을 때도 다중 테넌트 Logic Apps 서비스에서 실행되는 커넥터를 계속 사용할 수 있습니다. 자세한 내용은 [전용 테넌트와 다중 테넌트 비교](#difference)를 참조하세요.

* 다중 테넌트 서비스의 논리 앱에서 공유되는 고정 IP 주소와는 별개의 고정 IP 주소입니다. 대상 시스템과 통신하도록 단일 공용, 정적 및 예측 가능한 아웃바운드 IP 주소를 설정할 수도 있습니다. 이런 방식으로 각 ISE에 대한 해당 대상 시스템에서 추가 방화벽을 설정할 필요가 없습니다.

* 실행 지속 시간, 스토리지 보존, 처리량, HTTP 요청 및 응답 시간 제한, 메시지 크기 및 사용자 지정 커넥터 요청에 대한 제한이 증가합니다. 자세한 내용은 [Azure Logic Apps에 대한 제한 및 구성](logic-apps-limits-and-config.md)을 참조하세요.

<a name="difference"></a>

## <a name="dedicated-versus-multi-tenant"></a>전용 테넌트와 다중 테넌트 비교

ISE에서 논리 앱을 만들고 실행하는 경우 다중 테넌트 Logic Apps 서비스와 동일한 사용자 환경 및 유사한 기능을 사용할 수 있습니다. 다중 테넌트 Logic Apps 서비스에서 사용할 수 있는 동일한 기본 제공 트리거, 작업 및 관리되는 커넥터를 모두 사용할 수 있습니다. 일부 관리되는 커넥터는 추가 ISE 버전을 제공합니다. ISE 커넥터와 비 ISE 커넥터의 차이점은 실행되는 위치 및 ISE 내에서 작업할 때 Logic App Designer에서 갖는 레이블에 있습니다.

![ISE에 레이블이 있거나 없는 커넥터](./media/connect-virtual-network-vnet-isolated-environment-overview/labeled-trigger-actions-integration-service-environment.png)

* HTTP와 같은 기본 제공 트리거 및 작업은 **CORE** 레이블을 표시하고 논리 앱과 동일한 ISE에서 실행됩니다.

* **ISE** 레이블을 표시하는 관리되는 커넥터는 특별히 ISE용으로 설계되었으며 항상 논리 앱과 동일한 ISE에서 실행됩니다. 예를 들어 [ISE 버전을 제공하는 일부 커넥터](../connectors/managed.md#ise-connectors)는 다음과 같습니다.<p>

  * Azure Blob Storage, File Storage 및 Table Storage
  * Azure Service Bus, Azure Queues, Azure Event Hubs
  * Azure Automation, Azure Key Vault, Azure Event Grid, Azure Monitor 로그
  * FTP, SFTP-SSH, 파일 시스템, SMTP
  * SAP, IBM MQ, IBM DB2, IBM 3270
  * SQL Server, Azure Synapse Analytics, Azure Cosmos DB
  * AS2, X12 및 EDIFACT

  드문 경우 온-프레미스 시스템 또는 데이터 원본에 ISE 커넥터를 사용할 수 있는 경우 [온-프레미스 데이터 게이트웨이](../logic-apps/logic-apps-gateway-connection.md)를 사용하지 않고 직접 연결할 수 있습니다. 자세한 내용은 이 항목 뒷부분의 [온-프레미스 시스템에 액세스](#on-premises)를 참조하세요.

* **ISE** 레이블을 표시하지 않는 관리되는 커넥터는 ISE 내의 논리 앱을 위해 계속 작동합니다. 이러한 커넥터는 ISE가 아니라 항상 다중 테넌트 Logic Apps 서비스에서 실행됩니다.

* ISE 외부에서 만든 사용자 지정 커넥터는 [온-프레미스 데이터 게이트웨이](../logic-apps/logic-apps-gateway-connection.md)가 필요한지 여부에 상관없이 ISE 내부의 논리 앱을 위해 계속 작동합니다. 그러나 ISE 내부에서 만든 사용자 지정 커넥터는 온-프레미스 데이터 게이트웨이에서 작동하지 않습니다. 자세한 내용은 [온-프레미스 시스템에 액세스](#on-premises)를 참조하세요.

<a name="on-premises"></a>

## <a name="access-to-on-premises-systems"></a>온-프레미스 시스템에 액세스

ISE 내에서 실행되는 논리 앱은 다음 항목을 사용하여 Azure 가상 네트워크 내부에 있거나 가상 네트워크에 연결된 온-프레미스 시스템 및 데이터 원본에 직접 액세스할 수 있습니다.<p>

* **CORE** 레이블을 표시하는 HTTP 트리거 또는 작업

* 온-프레미스 시스템 또는 데이터 원본의 **ISE** 커넥터(사용할 수 있는 경우)

  ISE 커넥터를 사용할 수 있는 경우 [온-프레미스 데이터 게이트웨이](../logic-apps/logic-apps-gateway-connection.md) 없이 시스템 또는 데이터 원본에 직접 액세스할 수 있습니다. 그러나 ISE에서 SQL Server에 액세스하고 Windows 인증을 사용해야 하는 경우에는 커넥터의 비 ISE 버전 및 온-프레미스 데이터 게이트웨이를 사용해야 합니다. 커넥터의 ISE 버전은 Windows 인증을 지원하지 않습니다. 자세한 내용은 [ISE 커넥터](../connectors/managed.md#ise-connectors) 및 [통합 서비스 환경에서 연결](../connectors/managed.md#integration-account-connectors)을 참조하세요.

* 사용자 지정 커넥터

  * ISE 외부에서 만든 사용자 지정 커넥터는 [온-프레미스 데이터 게이트웨이](../logic-apps/logic-apps-gateway-connection.md)가 필요한지 여부에 상관없이 ISE 내부의 논리 앱을 위해 계속 작동합니다.

  * ISE 내부에서 만든 사용자 지정 커넥터는 온-프레미스 데이터 게이트웨이에서 작동하지 않습니다. 그러나 이러한 커넥터는 ISE를 호스트하는 가상 네트워크 내부에 있거나 가상 네트워크에 연결된 온-프레미스 시스템 및 데이터 원본에 직접 액세스할 수 있습니다. 따라서 ISE 내부에 있는 논리 앱은 일반적으로 이러한 리소스에 액세스할 때 데이터 게이트웨이가 필요하지 않습니다.

ISE 커넥터가 없거나 가상 네트워크 외부에 있거나 가상 네트워크에 연결되지 않은 온-프레미스 시스템 및 데이터 원본에 액세스하려면 여전히 온-프레미스 데이터 게이트웨이를 사용해야 합니다. ISE 내의 논리 앱은 **CORE** 또는 **ISE** 레이블이 없는 커넥터를 계속 사용할 수 있습니다. 이러한 커넥터는 ISE가 아니라 다중 테넌트 Logic Apps 서비스에서 실행됩니다. 

<a name="ise-level"></a>

## <a name="ise-skus"></a>ISE SKU

ISE를 만들 때 개발자 SKU 또는 프리미엄 SKU를 선택할 수 있습니다. 이 SKU 옵션은 ISE를 만들 때만 사용할 수 있으며 나중에 변경할 수 없습니다. 이러한 SKU 간의 차이점은 다음과 같습니다.

* **개발자**

  탐색, 실험, 개발 및 테스트에 사용할 수 있지만 프로덕션 또는 성능 테스트에는 사용할 수 없는 저비용 ISE를 제공합니다. 개발자 SKU에는 기본 제공 트리거 및 작업, 표준 커넥터, 엔터프라이즈 커넥터 및 단일 [무료 계층](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)이 [고정 월별 가격](https://azure.microsoft.com/pricing/details/logic-apps)으로 포함됩니다. 

  > [!IMPORTANT]
  > 이 SKU에는 SLA(서비스 수준 계약), 스케일 업 기능 또는 재생 중 중복도가 없습니다. 즉, 지연 또는 가동 중지 시간이 발생할 수 있습니다. 백 엔드 업데이트로 서비스가 일시적으로 중단될 수 있습니다.

  용량 및 한도 정보는 [Azure Logic Apps의 ISE 한도](logic-apps-limits-and-config.md#integration-service-environment-ise)를 참조하세요. ISE의 요금 청구 방식은 [Logic Apps 가격 책정 모델](../logic-apps/logic-apps-pricing.md#ise-pricing)을 참조하세요.

* **Premium**

  프로덕션 및 성능 테스트에 사용할 수 있는 ISE를 제공합니다. 프리미엄 SKU에는 SLA 지원, 기본 제공 트리거 및 작업, 표준 커넥터, 엔터프라이즈 커넥터, 단일 [표준 계층](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) 통합 계정, 스케일 업 기능 및 재생 중 중복도가 [고정된 월별 가격](https://azure.microsoft.com/pricing/details/logic-apps)으로 포함됩니다.

  용량 및 한도 정보는 [Azure Logic Apps의 ISE 한도](logic-apps-limits-and-config.md#integration-service-environment-ise)를 참조하세요. ISE의 요금 청구 방식은 [Logic Apps 가격 책정 모델](../logic-apps/logic-apps-pricing.md#ise-pricing)을 참조하세요.

<a name="endpoint-access"></a>

## <a name="ise-endpoint-access"></a>ISE 엔드포인트 액세스

ISE를 만들 때 내부 또는 외부 액세스 엔드포인트 중 하나를 사용하도록 선택할 수 있습니다. 이 선택에 따라 ISE의 논리 앱에서 요청 또는 웹후크 트리거가 가상 네트워크 외부의 호출을 받을 수 있을지 여부가 결정됩니다. 이러한 엔드포인트는 논리 앱의 실행 기록에서 입력 및 출력에 액세스할 수 있는 방법에도 영향을 줍니다.

> [!IMPORTANT]
> 액세스 엔드포인트는 ISE를 만드는 동안에만 선택할 수 있고 나중에 이 옵션을 변경할 수 없습니다.

* **내부**: 프라이빗 엔드포인트는 가상 네트워크 내부에서만 논리 앱 실행 기록의 입력 및 출력을 보고 액세스할 수 있는 ISE의 논리 앱에 대한 호출을 허용합니다.

  > [!IMPORTANT]
  > 이러한 웹후크 기반 트리거를 사용해야 하고 서비스가 가상 네트워크 및 피어링된 가상 네트워크 외부에 있는 경우 ISE를 만들 때 내부 엔드포인트가 *아닌* 외부 엔드포인트를 사용합니다.
  > 
  > * Azure DevOps
  > * Azure Event Grid
  > * Common Data Service
  > * Office 365
  > * SAP(다중 테넌트 버전)
  > 
  > 또한 실행 기록에 액세스하려는 컴퓨터와 프라이빗 엔드포인트 간에 네트워크 연결이 있어야 합니다. 그렇지 않으면 논리 앱의 실행 기록을 보려고 할 때 ‘예기치 않은 오류. 가져오지 못함’ 오류가 발생합니다.
  >
  > ![방화벽을 통해 트래픽을 보내지 못해 발생하는 Azure Storage 작업 오류](./media/connect-virtual-network-vnet-isolated-environment-overview/integration-service-environment-error.png)
  >
  > 예를 들어 클라이언트 컴퓨터는 ISE의 가상 네트워크 내부 또는 피어링이나 VPN(가상 사설망)을 통해 ISE의 가상 네트워크에 연결된 가상 네트워크 내부에 있을 수 있습니다. 

* **외부**: 퍼블릭 엔드포인트는 가상 네트워크 외부에서만 논리 앱 실행 기록의 입력 및 출력을 보고 액세스할 수 있는 ISE의 논리 앱에 대한 호출을 허용합니다. NSG(네트워크 보안 그룹)를 사용하는 경우 실행 기록의 입력 및 출력에 대한 액세스를 허용하는 인바운드 규칙으로 설정되어 있는지 확인합니다. 자세한 내용은 [ISE 액세스 사용](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access)을 참조하세요.

ISE가 내부 액세스 엔드포인트를 사용하는지 외부 액세스 엔드포인트를 사용하는지 확인하려면 ISE의 메뉴에서 **설정** 아래에 있는 **속성** 을 선택하고 **액세스 엔드포인트** 속성을 찾습니다.

![ISE 액세스 엔드포인트 찾기](./media/connect-virtual-network-vnet-isolated-environment-overview/find-ise-access-endpoint.png)

<a name="pricing-model"></a>

## <a name="pricing-model"></a>가격 책정 모델

ISE에서 실행되는 논리 앱, 기본 제공 트리거, 기본 제공 작업 및 커넥터는 사용량 기반 요금제와는 다른 고정 요금제를 사용합니다. 자세한 내용은 [Logic Apps 가격 책정 모델](../logic-apps/logic-apps-pricing.md#ise-pricing)을 참조하세요. 가격 책정 요금은 [Logic Apps 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps/)을 참조하세요.

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>ISE와의 통합 계정

ISE(통합 서비스 환경) 내에서 논리 앱을 통해 통합 계정을 사용할 수 있습니다. 그러나 해당 통합 계정은 연결된 논리 앱과 *동일한 ISE* 를 사용해야 합니다. ISE의 논리 앱은 같은 ISE에 있는 통합 계정만 참조할 수 있습니다. 통합 계정을 만들 때는 통합 계정의 위치로 ISE를 선택할 수 있습니다. ISE가 포함된 통합 계정의 가격 책정 및 요금 청구 방식은 [Logic Apps 가격 책정 모델](../logic-apps/logic-apps-pricing.md#ise-pricing)을 참조하세요. 가격 책정 요금은 [Logic Apps 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps/)을 참조하세요. 한도 정보는 [통합 계정 한도](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Logic Apps에서 Azure 가상 네트워크에 연결](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)에 대해 자세히 알아보기
* [Azure 서비스에 대한 가상 네트워크 통합](../virtual-network/virtual-network-for-azure-services.md)에 대해 알아보기
