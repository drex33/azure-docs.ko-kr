---
title: Azure Data Factory에서 데이터 팩터리 복사 또는 복제
description: Azure Data Factory에서 데이터 팩터리를 복사 또는 복제하는 방법 알아보기
ms.service: data-factory
ms.subservice: data-movement
author: joshuha-msft
ms.author: joowen
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 6be0b181ce7ca297b71cb258fb782e5ab1e336ea
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131844528"
---
# <a name="copy-or-clone-a-data-factory-in-azure-data-factory"></a>Azure Data Factory에서 데이터 팩터리 복사 또는 복제

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

이 문서에서는 Azure Data Factory에서 데이터 팩터리를 복사 또는 복제하는 방법을 설명합니다.

## <a name="use-cases-for-cloning-a-data-factory"></a>데이터 팩터리 복제 사용 사례

다음은 데이터 팩터리를 복사 또는 복제하는 것이 유용할 수 있는 몇 가지 상황입니다.

- **Data Factory** 를 새 지역으로 이동합니다. Data Factory를 다른 지역으로 이동하려면 대상 지역에 복사본을 만들고 기존 항목을 삭제하는 것이 가장 좋습니다.

- **Data Factory의 이름을 바꿉니다**. Azure는 리소스 이름 바꾸기를 지원하지 않습니다. 데이터 팩터리 이름을 바꾸려는 경우 데이터 팩터리를 다른 이름으로 복제한 다음 기존 데이터 팩터리를 삭제할 수 있습니다.

- 디버그 기능으로 충분하지 않을 경우 **변경 내용 디버그**. 대부분의 시나리오에서는 [디버그](iterative-development-debugging.md)를 사용할 수 있습니다. 다른 시나리오에서는 복제된 샌드박스 환경에서 변경 사항을 테스트하는 것이 더 좋습니다. 예를 들어, 매개 변수가 있는 ETL 파이프라인이 파일이 도착하는 즉시 트리거되는 경우 동작하는 방법과 연속 창으로 트리거되는 경우 동작하는 방법은 디버그 하나만으로 쉽게 테스트할 수 없습니다. 이러한 경우 샌드박스 환경을 복제하여 실험하는 것이 좋습니다. Azure Data Factory는 주로 실행 횟수에 따라 요금이 부과되므로 두 번째 팩터리가 추가 요금을 발생하지는 않습니다.

## <a name="how-to-clone-a-data-factory"></a>데이터 팩터리 복제 방법

1. 필수 요소로, 먼저 Azure Portal에서 대상 데이터 팩터리를 만들어야 합니다.

1. GIT 모드에 있는 경우:
    1. 포털에서 게시할 때마다 팩터리의 Resource Manager 템플릿이 리포지토리의 adf\_게시 분기에 있는 GIT에 저장됩니다.
    1. 새 팩터리를 _동일한_ 리포지토리에 연결하고 adf\_게시 분기에서 빌드합니다. 파이프라인, 데이터 세트 및 트리거와 같은 리소스는 다음을 통해 수행됩니다.

1. 라이브 모드에 있는 경우:
    1. Data Factory UI를 사용하면 데이터 팩터리의 전체 페이로드를 Resource Manager 템플릿 파일 및 매개 변수 파일로 내보낼 수 있습니다. 여기에는 포털의 **ARM 템플릿 / Resource Manager 템플릿 내보내기** 단추에서 액세스할 수 있습니다.
    1. 매개 변수 파일을 적절하게 변경하고 새 팩터리에 대한 새 값으로 교환할 수 있습니다.
    1. 다음으로 표준 Resource Manager 템플릿 배포 방법을 통해 배포할 수 있습니다.

1. 원본 팩터리에 SelfHosted IntegrationRuntime이 있는 경우 대상 팩터리에도 동일한 이름으로 미리 만들어야 합니다. 서로 다른 팩터리 간에 SelfHosted Integration Runtime을 공유하려면 SelfHosted IR 공유에서 [여기](create-shared-self-hosted-integration-runtime-powershell.md)에 게시된 패턴을 사용할 수 있습니다.

1. 보안상의 이유로 생성된 Resource Manager 템플릿에는 연결된 서비스에 대한 암호와 같은 암호 정보가 포함되지 않습니다. 따라서 자격 증명을 배포 매개 변수로 제공해야 합니다. 설정에 자격 증명을 수동으로 입력하는 것이 바람직하지 않은 경우 대신 Azure Key Vault에서 연결 문자열과 암호를 검색하는 것이 좋습니다. [자세히 보기](store-credentials-in-key-vault.md)

## <a name="next-steps"></a>다음 단계

[Azure Data Factory UI를 사용하여 데이터 팩터리 만들기](quickstart-create-data-factory-portal.md)에서 Azure Portal을 통해 데이터 팩터리를 만들기 위한 지침을 검토하세요.
