---
title: 시험 사용 기술 구성, Microsoft 상업용 Marketplace
description: 시험 사용에 대해 알아봅니다. 시험 사용을 사용하면 새 고객이 구매를 커밋하기 전에 제품을 시험할 수 있습니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 10/26/2021
author: trkeya
ms.author: trkeya
ms.openlocfilehash: 04eab06f0ce8aefa675b89613611c0ec9a93f9b6
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131847359"
---
# <a name="test-drive-technical-configuration"></a>시험 사용 기술 구성

Microsoft 상업용 Marketplace의 시험 사용 옵션을 사용하면 제품의 주요 기능에 대한 셀프 가이드 둘러보기 실습을 구성할 수 있습니다. 시험 사용을 사용하면 새 고객이 구매를 커밋하기 전에 제품을 사용해 볼 수 있습니다. 자세한 내용은 [시험 사용이란?](what-is-test-drive.md)을 참조하세요.

더 이상 제품에 대한 시험 사용을 제공하지 않으려는 경우 **제품 설정** 페이지로 돌아가서 **시험 사용** 확인란의 선택을 취소합니다. 모든 제품 유형에 시험 사용이 가능한 것은 아닙니다.

> [!TIP]
> 상업용 Marketplace에서 시험 구매에 대한 고객의 보기를 보려면 [Azure Marketplace란?](/marketplace/azure-marketplace-overview#take-action-on-a-listing) 및 [Microsoft AppSource 무엇인가요?를 참조하세요.](/marketplace/appsource-overview)

## <a name="azure-resource-manager-test-drive"></a>Azure Resource Manager 시험 사용

가상 머신 또는 Azure 앱 제품에 대한 유일한 시험 사용 옵션이며 매우 자세한 설정이 필요합니다. [배포 구독 세부 정보](#deployment-subscription-details) 및 [시험 사용 목록](#test-drive-listings)에 대한 아래 섹션을 읽고, [Azure Resource Manager 시험 사용 구성](azure-resource-manager-test-drive.md)에 대한 별도의 항목을 계속 진행합니다.

## <a name="hosted-test-drive"></a>호스트형 시험 사용

Microsoft는 서비스 프로비전 및 프로비전 해제를 호스팅하고 유지 관리하여 시험 사용 설정의 복잡성을 제거할 수 있습니다. 이 유형의 시험 사용에 대한 구성은 시험 사용이 Dynamics 365 Customer Engagement 또는 Dynamics 365 Operations 대상 그룹을 대상으로 하는지 여부에 관계없이 동일합니다.

- **최대 동시 시험 사용 수**(필수) – 시험 사용을 동시에 사용할 수 있는 최대 고객 수를 설정합니다. 각 동시 사용자는 시험 사용이 활성화된 상태에서 Dynamics 365 라이선스를 이용하므로 최대 제한 집합을 지원하는 라이선스가 충분한지 확인해야 합니다. 권장 값은 3-5입니다.

- **시험 사용 기간**(필수) – 각 고객에게 시험 사용이 활성 상태로 유지되는 시간을 입력합니다. 이 시간이 지난 후에는 세션이 종료되고 더 이상 라이선스를 이용하지 않습니다. 권장 값은 제품의 복잡성에 따라 2~24시간입니다. 이 기간은 정수 시간으로만 설정할 수 있습니다(예: "2시간"은 유효하지만 "1.5시간"은 유효하지 않음). 시간이 만료된 후 시험 사용에 다시 액세스하려는 경우 새 세션을 요청할 수 있습니다.

- **인스턴스 URL**(필수) – 고객이 시험 사용을 시작하는 URL입니다. 일반적으로 샘플 데이터가 설치된 앱을 실행하는 Dynamics 365 인스턴스의 URL입니다(예: `https://testdrive.crm.dynamics.com`).

- **인스턴스 웹 API URL**(필수) – Microsoft 365 계정에 로깅하고 **설정** > **사용자 지정** > **개발자 리소스** > **Instance Web API(서비스 루트 URL)** 로 이동하여 여기서 찾은 URL(예: `https://testdrive.crm.dynamics.com/api/data/v9.0`)을 복사해 Dynamics 365의 웹 API URL을 검색합니다.

- **역할 이름**(필수) – 사용자 지정 Dynamics 365 시험 사용에서 정의한 보안 역할 이름을 제공합니다(예: test-drive-role). 시험 사용 중 사용자에게 이 역할이 할당됩니다.

시험 사용을 위한 Dynamics 365 환경을 설정하고 테넌트에서 시험 사용 사용자를 프로비전 및 프로비전 해제할 수 있는 AppSource 권한을 부여하는 방법에 대한 도움말은 [다음 지침](./test-drive-azure-subscription-setup.md)을 따르세요.

호스트형 시험 사용 나열 및 구성에 대한 단계별 지침은 [호스트형 시험 사용의 자세한 구성](./test-drive-hosted-detailed-config.md) 페이지를 방문하세요.

## <a name="logic-app-test-drive"></a>논리 앱 시험 사용

이 유형의 시험 사용은 Microsoft에서 호스트되지 않습니다. 이를 사용하여 다양한 복잡한 솔루션 아키텍처를 포함하는 Dynamics 365 제품, 또는 기타 사용자 지정 리소스에 연결합니다. 논리 앱 시험 사용을 설정하는 방법에 대한 자세한 내용은 GitHub의 [작업](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) 및 [고객 참여](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app)를 참조하세요.

- **지역**(필수, 단일 선택 드롭다운 목록) - 현재 시험 사용을 사용할 수 있는 26개의 Azure 지원 지역이 있습니다. 논리 앱에 대한 리소스는 선택한 지역에 배포됩니다. 논리 앱이 특정 지역에 저장된 사용자 지정 리소스를 포함하는 경우 여기서 해당 지역을 선택해야 합니다. 포털에서 논리 앱을 Azure 구독에 로컬로 완전히 배포하고 이를 선택하기 전에 제대로 작동하는지 확인하는 것이 가장 좋습니다.

- **최대 동시 시험 사용 수**(필수) – 시험 사용을 동시에 사용할 수 있는 최대 고객 수를 설정합니다. 이러한 시험 사용은 이미 배포되어 있어 고객은 배포를 기다리지 않고 즉시 액세스할 수 있습니다.

- **시험 사용 기간**(필수) – 시험 사용이 활성 상태로 유지되는 기간(시간 단위)을 입력합니다. 이 기간이 끝나면 시험 사용이 자동으로 종료됩니다.

- **Azure 리소스 그룹 이름**(필수) - 논리 앱 시험 사용이 저장된 [Azure 리소스 그룹](../azure-resource-manager/management/overview.md#resource-groups)) 이름을 입력합니다.

- **Azure 논리 앱 이름**(필수) – 시험 사용을 사용자에게 할당하는 논리 앱의 이름을 입력합니다. 이 논리 앱은 위의 Azure 리소스 그룹에 저장되어야 합니다.

- **논리 앱 이름 프로비전 해제**(필수) – 고객이 완료되면 시험 사용 프로비전을 해제하는 논리 앱의 이름을 입력합니다. 이 논리 앱은 위의 Azure 리소스 그룹에 저장되어야 합니다.

## <a name="power-bi-test-drive"></a>Power BI 시험 사용

대화형 Power BI 시각적 개체를 시연하려는 제품은 포함된 링크를 사용하여 사용자 지정 빌드 대시보드를 시험 사용으로 공유할 수 있으며 추가 기술 구성은 필요하지 않습니다. 여기서는 포함된 Power BI URL을 업로드하기만 하면 됩니다.

Power BI 앱 설정에 대한 자세한 내용은 [Power BI 앱이란?](/power-bi/service-template-apps-overview)을 참조하세요

## <a name="deployment-subscription-details"></a>배포 구독 세부 정보

Microsoft가 사용자를 대신해서 시험 사용을 배포하도록 하려면 별도의 고유한 Azure 구독을 만들고 제공합니다(Power BI 시험 사용에는 필요하지 않음).

- **Azure 구독 ID**(Azure Resource Manager 및 Logic Apps에 필요) - 리소스 사용량 보고 및 청구를 위해 Azure 계정 서비스에 대한 액세스 권한을 부여하는 구독 ID를 입력합니다. 아직 없는 경우 시험 사용에 사용할 [별도의 Azure 구독을 만드는 것](../cost-management-billing/manage/create-subscription.md)이 좋습니다. [Azure Portal](https://portal.azure.com/)에 로그인하여 왼쪽 메뉴의 **구독** 탭으로 이동하면 Azure 구독 ID를 확인할 수 있습니다. 이 탭을 선택하면 구독 ID(예: "a83645ac-1234-5ab6-6789-1h234g764ghty")가 표시됩니다.

- **Azure AD 테넌트 ID**(필수) - Azure AD(Active Directory) [테넌트 ID](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)를 입력합니다. 이 ID를 찾으려면 [Azure Portal](https://portal.azure.com/)에 로그인하고 왼쪽 메뉴에서 Active Directory 탭을 선택하고 **속성** 을 선택한 다음 나열된 **디렉터리 ID** 번호(예: 50c464d3-4930-494c-963c-1e951d15360e)를 찾습니다. [https://www.whatismytenantid.com](https://www.whatismytenantid.com)에서 도메인 이름 주소를 사용하여 조직의 테넌트 ID를 조회할 수도 있습니다.

- **Azure AD 테넌트 이름**(Dynamic 365에 필요) – Azure AD(Active Directory) 이름을 입력합니다. 이 이름을 찾으려면 [Azure Portal](https://portal.azure.com/)에 로그인합니다. 그러면 오른쪽 위 계정 이름 아래에 테넌트 이름이 나열됩니다.

- **Azure AD 앱 ID**(필수) – Azure AD(Active Directory) [애플리케이션 ID](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)를 입력합니다. 이 ID를 찾으려면 [Azure Portal](https://portal.azure.com/)에 로그인하고, 왼쪽 탐색 메뉴에서 Active Directory 탭을 선택한 뒤, **앱 등록** 을 선택한 다음, 나열된 **애플리케이션 ID** 번호(예: `50c464d3-4930-494c-963c-1e951d15360e`)를 찾습니다.

- **Azure AD 앱 클라이언트 암호**(필수) – Azure AD 애플리케이션 [클라이언트 암호](../active-directory/develop/howto-create-service-principal-portal.md#option-2-create-a-new-application-secret)를 입력합니다. 이 값을 찾으려면 [Azure Portal](https://portal.azure.com/)에 로그인합니다. 왼쪽 메뉴에서 **Azure Active Directory** 탭을 선택하고 **앱 등록** 을 선택한 다음 시험 사용 앱을 선택합니다. 그런 다음, **인증서 및 암호** 를 선택하고 **새 클라이언트 암호** 를 선택하여 설명을 입력하고, **만료** 에서 **안 함** 을 선택한 뒤 **추가** 를 선택합니다. 이 값을 적어 두어야 합니다. 이 페이지에서 벗어나기 전에 값을 복사합니다.

## <a name="test-drive-listings"></a>시험 사용 목록

**시험 사용 목록** 옵션은 파트너 센터의 **시험 사용** 탭 아래에서 찾을 수 있으며, 시험 사용이 가능한 언어와 시장이 표시됩니다. 현재는 영어(미국)만 지원됩니다. 또한 이 페이지에는 언어별 목록의 상태와 목록이 추가된 날짜/시간이 표시됩니다. 각 언어/시장에 대해 시험 사용 세부 정보(설명, 사용자 설명서, 비디오 등)를 정의해야 합니다.

- **설명**(필수): 시험 사용, 시연 대상, 사용자의 실험 목표, 탐색할 기능 등 사용자가 제품을 가져올지 여부를 결정하는 데 도움이 되는 관련 정보를 설명합니다. 이 필드에는 최대 3,000자의 텍스트를 입력할 수 있습니다.

- **액세스 정보**(Azure Resource Manager 및 논리 시험 사용에 필요): 이 시험 사용에 액세스하고 사용하기 위해 고객이 알아야 할 사항을 설명합니다. 제품 사용에 대한 시나리오와 고객이 시험 사용을 통해 기능에 액세스하기 위해 알아야 할 사항을 정확히 살펴봅니다. 이 필드에는 최대 10,000자의 텍스트를 입력할 수 있습니다.

- **사용자 설명서**(필수): 시험 사용 환경의 심층 지침서입니다. 사용자 설명서는 고객이 시험 사용을 통해 얻을 수 있는 사항을 정확하게 다루며, 고객에게 있을 수 있는 질문에 대한 참조로 사용됩니다. 업로드한 파일은 PDF 형식이어야 하며 최대 255자로 이름이 지정되어야 합니다.

- **동영상: 동영상 추가**(선택 사항): 다른 곳에서 호스팅된 동영상은 여기에서 링크 및 썸네일 이미지(533 x 324픽셀)와 함께 참조되므로, 고객은 제품의 기능을 성공적으로 사용하고 혜택을 강조하는 시나리오를 이해하는 방법을 포함하여 시험 사용을 보다 잘 이해하는 데 유용한 사용 정보를 볼 수 있습니다.
  - **이름**(필수)
  - **URL**(YouTube 또는 Vimeo만 해당, 필수)
  - **썸네일**(533 x 324 픽셀) – 이미지는 PNG 형식이어야 합니다.

현재 파트너 센터에서 시험 사용을 만들고 있는 경우 계속 진행하기 전에 **초안 저장** 을 선택합니다.

호스트형 시험 사용 나열 및 구성에 대한 단계별 지침은 [호스트형 시험 사용의 자세한 구성](./test-drive-hosted-detailed-config.md) 페이지를 방문하세요.

## <a name="next-steps"></a>다음 단계

- [시험 사용 모범 사례](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [개요](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)(PDF. 팝업 차단이 해제되어 있는지 확인)
- [상업용 마켓플레이스에서 기존 제품 업데이트](update-existing-offer.md)