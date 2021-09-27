---
title: 랩 만들기
description: 이 문서는 Azure Portal 및 Azure DevTest Labs를 사용하여 랩을 만드는 과정을 안내합니다.
ms.topic: how-to
ms.date: 10/12/2020
ms.openlocfilehash: 68b560125a4cacceee613b5c7c9bfaafc5c95c06
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128602900"
---
# <a name="create-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs에서 랩 만들기

Azure DevTest Labs에서 랩은 VM(Virtual Machines)과 같은 리소스 그룹을 포함하는 인프라로서, 이를 통해 한도 및 할당량을 지정하여 해당 리소스를 더 잘 관리할 수 있습니다. 이 문서는 Azure Portal을 사용하여 랩을 만드는 과정을 안내합니다.

## <a name="prerequisites"></a>필수 구성 요소

랩을 만들려면 다음이 필요합니다.

* Azure 구독 Azure 구입 옵션에 대해 알아보려면 [Azure 구입 방법](https://azure.microsoft.com/pricing/purchase-options/) 또는 [1개월 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요. 랩을 만들려면 구독 소유자여야 합니다.

## <a name="get-started-with-azure-devtest-labs-in-minutes"></a>몇 분 이내에 Azure DevTest Labs 시작

다음 링크를 클릭하면 Azure Portal 페이지로 전송되고, Azure DevTest Labs에서 새 랩을 만들기 시작할 수 있습니다.

[몇 분 이내에 Azure DevTest Labs 시작](https://go.microsoft.com/fwlink/?LinkID=627034&clcid=0x409)

## <a name="fill-out-settings-for-your-new-account"></a>새 계정의 설정 입력

**DevTest Labs 만들기** 페이지에서 다음 설정을 입력합니다.

> [!TIP]
> 각 페이지의 맨 아래에는 **자동화를 위한 템플릿을 다운로드** 할 수 있는 링크가 있습니다.

### <a name="basic-settings"></a>기본 설정

기본적으로 **기본 설정** 탭이 표시됩니다. 

![기본 설정](./media/devtest-lab-create-lab/basic-settings.png)

다음 값을 입력합니다.

|이름|Description|
|---|---|
|**구독** | 필수 요소. **구독** 을 선택하여 랩과 연결합니다.|
|**리소스 그룹**| 필수 요소. 랩의 **리소스 그룹에 대한 이름** 을 입력합니다. 새 항목을 만듭니다(없는 경우).|
|**랩 이름**| 필수 요소. 랩에 대한 **이름** 을 입력합니다.|
|**위치**|필수 요소. 랩을 저장할 위치를 선택합니다.|
|**공용 환경**| [퍼블릭 환경 구성 및 사용](devtest-lab-configure-use-public-environments.md)을 참조하세요.

### <a name="auto-shutdown-settings"></a>자동 종료 설정

**자동 종료** 페이지로 전환하여 설정을 확인합니다. 자동 종료를 사용하면 매일 예약된 시간에 랩의 모든 머신을 자동으로 종료할 수 있습니다.

![자동 종료 탭](./media/devtest-lab-create-lab/auto-shutdown.png)

페이지에서 **자동 종료** 를 사용하도록 설정하고 랩의 모든 VM에 대한 자동 종료 매개 변수를 정의할 수 있습니다. 자동 종료 기능은 주로 VM을 자동으로 종료하려는 경우를 지정할 수 있는 비용 절감 기능입니다. [Azure DevTest Labs에서 랩에 대한 모든 정책 관리](./devtest-lab-set-lab-policy.md#set-auto-shutdown) 문서에 간략히 나와 있는 단계에 따라 랩을 만든 후 자동 종료 설정을 변경할 수 있습니다.

### <a name="networking"></a>네트워킹

랩을 만들 때 기본 네트워크가 자동으로 생성됩니다. **네트워킹** 탭으로 전환하여 설정을 원하는 대로 변경/구성합니다. 예를 들어 기존 가상 네트워크를 선택합니다.

![네트워킹 탭 ](./media/devtest-lab-create-lab/networking.png)

### <a name="tags"></a>태그들

랩에서 만들 모든 리소스에 추가되는 사용자 지정 태그를 만들려는 경우 **태그** 에 대한 **이름** 및 **값** 정보를 입력합니다. 태그는 랩 리소스를 범주별으로 관리하고 구성하는 데 유용합니다. 랩에서 만든 후에 태그를 추가하는 방법을 포함하여 태그에 대한 자세한 내용은 [랩에 태그 추가](devtest-lab-add-tag.md)를 참조하세요.

![태그 탭 ](./media/devtest-lab-create-lab/tags.png)

### <a name="review-and-create"></a>검토 후 만들기

완료되면 **만들기** 를 선택합니다. 포털 페이지 오른쪽 위에 있는 **알림** 영역을 확인하여 랩 만들기 프로세스 상태를 모니터링할 수 있습니다. 

![만들기 탭](./media/devtest-lab-create-lab/create-1.png)

## <a name="completed-the-creation"></a>만들기 완료

완료되면 **리소스로 이동** 단추가 페이지 맨 아래와 알림 창에 표시됩니다. 또는 **DevTest Labs** 페이지를 새로 고쳐 랩 목록에서 새로 만든 랩을 확인합니다.  

![서비스 만들기](./media/devtest-lab-create-lab/create-2.png)

**리소스로 이동** 단추를 누르면 새 DevTest Labs 계정의 홈페이지로 이동됩니다.

![리소스](./media/devtest-lab-create-lab/go-to-resource.png)

Azure Portal에서 **DevTest Labs** 를 검색할 수도 있습니다. 목록에서 새 계정을 선택하고 홈페이지로 이동합니다. 

![서비스 생성됨](./media/devtest-lab-create-lab/created.png)

## <a name="next-steps"></a>다음 단계

랩을 만들었으면 다음 단계를 고려 합니다.

* [랩에 대한 보안 액세스](devtest-lab-add-devtest-user.md)
* [랩 정책 설정](devtest-lab-set-lab-policy.md)
* [랩 템플릿 만들기](devtest-lab-create-template.md)
* [VM에 대한 사용자 지정 아티팩트 만들기](devtest-lab-artifact-author.md)
* [랩에 VM 추가](devtest-lab-add-vm.md)
