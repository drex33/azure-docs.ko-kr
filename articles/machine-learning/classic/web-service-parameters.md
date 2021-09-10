---
title: 'ML Studio(클래식): 웹 서비스 매개 변수 - Azure'
description: Machine Learning 웹 서비스 매개 변수를 사용하여 웹 서비스에 액세스할 때 모델의 동작을 수정하는 방법입니다.
services: machine-learning
author: likebupt
ms.author: keli19
ms.assetid: c49187db-b976-4731-89d6-11a0bf653db1
ms.service: machine-learning
ms.subservice: studio-classic
ms.workload: data-services
ms.topic: how-to
ms.date: 01/12/2017
ms.openlocfilehash: f26a6541fc3c5720043d5b00578bea374cfcb959
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122689508"
---
# <a name="use-machine-learning-studio-classic-web-service-parameters"></a>Machine Learning Studio(클래식) 웹 서비스 매개 변수 사용

**적용 대상:**  ![적용 대상:](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio(클래식)  ![적용되지 않는 대상:](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

[!INCLUDE [ML Studio (classic) retirement](../../../includes/machine-learning-studio-classic-deprecation.md)]

Machine Learning 웹 서비스는 구성 가능한 매개 변수로 모듈이 포함된 실험을 게시하여 만듭니다. 경우에 따라 웹 서비스가 실행되는 동안 모듈 동작을 변경할 수도 있습니다. *웹 서비스 매개 변수* 를 통해 이 태스크를 수행할 수 있습니다. 

일반적인 예는 게시된 웹 서비스의 사용자가 웹 서비스에 액세스할 때 다른 데이터 원본을 지정할 수 있도록 [데이터 가져오기][reader] 모듈을 설정하는 것입니다. 또는 다른 대상을 지정할 수 있도록 [데이터 내보내기][writer] 모듈을 구성하는 것입니다. 다른 예로는 [기능 해싱][feature-hashing] 모듈에 대한 비트 수 변경 또는 [필터 기반 기능 선택][filter-based-feature-selection] 모듈에 대한 원하는 기능 수가 있습니다. 

실험에서 웹 서비스 매개 변수를 설정하여 하나 이상의 모듈 매개 변수와 연결하고 이러한 매개 변수가 필수인지 또는 선택 사항인지 지정할 수 있습니다. 그러면 웹 서비스 사용자는 웹 서비스를 호출할 때 이러한 매개 변수에 대한 값을 제공할 수 있습니다. 



## <a name="how-to-set-and-use-web-service-parameters"></a>웹 서비스 매개 변수를 설정 및 사용하는 방법
모듈 매개 변수 옆에 있는 아이콘을 클릭하고 "웹 서비스 매개 변수로 설정"을 선택하여 웹 서비스 매개 변수를 정의합니다. 새로운 웹 서비스 매개 변수가 만들어지고 해당 모듈 매개 변수에 연결됩니다. 그런 다음 웹 서비스에 액세스할 때 사용자가 웹 서비스 매개 변수의 값을 지정할 수 있으며, 모듈 매개 변수에 값이 적용됩니다.

웹 서비스 매개 변수를 정의하고 나면 실험의 다른 모든 모듈에서 사용할 수 있습니다. 한 모듈의 매개 변수와 연결된 웹 서비스 매개 변수를 정의하는 경우 매개 변수에 동일한 형식의 값이 예상되는 한 동일한 웹 서비스 매개 변수를 다른 모든 모듈에 사용할 수 있습니다. 예를 들어 웹 서비스 매개 변수가 숫자 값인 경우 숫자 값이 필요한 모듈 매개 변수에만 사용할 수 있습니다. 사용자가 웹 서비스 매개 변수의 값을 설정하는 경우 연결된 모든 모듈 매개 변수에 적용됩니다.

웹 서비스 매개 변수에 대한 기본값을 제공할지 여부를 결정할 수 있습니다. 제공하는 경우 매개 변수는 웹 서비스 사용자에게 선택 사항입니다. 기본값을 제공하지 않는 경우 사용자가 웹 서비스에 액세스할 때 값을 입력해야 합니다.

웹 서비스에 대한 API 설명서에는 웹 서비스에 액세스할 때 프로그래밍 방식으로 웹 서비스 매개 변수를 지정하는 방법과 관련해서 웹 서비스 사용자를 위한 정보가 포함됩니다.

> [!NOTE]
> 클래식 웹 서비스에 대한 API 설명서는 Machine Learning Studio(클래식)에서 웹 서비스 **대시보드** 의 **API 도움말 페이지** 링크를 통해 제공됩니다. 새 웹 서비스에 대한 API 설명서는 웹 서비스에 대한 **사용** 및 **Swagger API** 페이지의 [Machine Learning 웹 서비스](https://services.azureml.net/Quickstart) 포털을 통해 제공됩니다.
> 
> 

## <a name="example"></a>예제
예를 들어 Azure Blob Storage로 정보를 전송하는 [데이터 내보내기][writer] 모듈이 포함된 실험이 있다고 가정합니다. 고객이 새 데이터(PUT Blob, PUT 블록, PUT 페이지, PUT 파일 등)를 Blob 또는 File Storage에 기록할 경우 모든 기록 내용이 가장 강력한 블록 암호화 중 하나인 256비트 AES 암호화를 사용하여 암호화됩니다.

1. Machine Learning Studio(클래식)에서 [데이터 내보내기][writer] 모듈을 클릭하여 선택합니다. 실험 캔버스 오른쪽에 있는 속성 창에 해당 속성이 표시됩니다.
2. 스토리지 형식을 지정합니다.
   
   * **데이터 대상 지정** 에서 "Azure Blob Storage"를 선택합니다.
   * **인증 유형 지정** 에서 "계정"을 선택합니다.
   * Blob Storage 내에서, File Storage 내에서 또는 Blob Storage에서 파일 스토리지로 혹은 그 반대로 파일을 복사할 때 이 옵션을 사용할 수 있습니다. 

3. **컨테이너 매개 변수로 시작하는 blob에 대한 경로** 의 오른쪽에 있는 아이콘을 클릭합니다. 다음과 같이 표시됩니다.
   
   ![웹 서비스 매개 변수 아이콘](./media/web-service-parameters/icon.png)
   
   "웹 서비스 매개 변수로 설정&quot;을 선택합니다.
   
   속성 창 맨 아래의 **웹 서비스 매개 변수** 에 &quot;컨테이너로 시작하는 Blob 경로”라는 이름으로 항목이 추가됩니다. 이제 이 웹 서비스 매개 변수가 이 [데이터 내보내기][writer] 모듈 매개 변수와 연결됩니다.
4. 웹 서비스 매개 변수의 이름을 바꾸려면 이름을 클릭하고 &quot;Blob 경로&quot;를 입력한 다음 **Enter** 키를 누릅니다. 
5. 웹 서비스 매개 변수에 대한 기본값을 제공하려면 이름 오른쪽에 있는 아이콘을 클릭하고 &quot;기본값 제공&quot;을 선택한 다음 값(예: &quot;container1/output1.csv")을 입력하고 **Enter** 키를 누릅니다.
   
   ![웹 서비스 매개 변수](./media/web-service-parameters/parameter.png)
6. **실행** 을 클릭합니다. 
7. **웹 서비스 배포** 를 클릭하고 **웹 서비스 배포[클래식]** 또는 **웹 서비스 배포[신규]** 를 선택하여 웹 서비스를 배포합니다.

> [!NOTE] 
> 새 웹 서비스를 배포하려면 웹 서비스를 배포하려는 구독에 충분한 권한이 있어야 합니다. 자세한 내용은 [Machine Learning 웹 서비스 포털을 사용하여 웹 서비스 관리](manage-new-webservice.md)를 참조하세요. 

이제 웹 서비스 사용자가 웹 서비스에 액세스할 때 [데이터 내보내기][writer] 모듈에 대한 새 대상을 지정할 수 있습니다.

## <a name="more-information"></a>자세한 정보
자세한 예제는 [Machine Learning 블로그](/archive/blogs/machinelearning/azureml-web-service-parameters)의 [웹 서비스 매개 변수](/archive/blogs/machinelearning/azureml-web-service-parameters) 항목을 참조하세요.

Machine Learning 웹 서비스에 액세스하는 방법에 대한 자세한 내용은 [Machine Learning 웹 서비스를 사용하는 방법](consume-web-services.md)을 참조하세요.

<!-- Module References -->
[feature-hashing]: /azure/machine-learning/studio-module-reference/feature-hashing
[filter-based-feature-selection]: /previous-versions/azure/dn905854(v=azure.100)
[reader]: /azure/machine-learning/studio-module-reference/import-data
[writer]: /azure/machine-learning/studio-module-reference/export-data