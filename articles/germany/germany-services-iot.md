---
title: Azure Germany IoT 서비스 | Microsoft Docs
description: 이 문서에서는 Azure Germany Azure IoT Suite의 시작점을 제공합니다.
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfdocs
ms.openlocfilehash: 16259a816413bcf7f57475b12f46c9410cb3ca44
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/26/2021
ms.locfileid: "122566187"
---
# <a name="azure-germany-iot-services"></a>Azure Germany IoT 서비스

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

## <a name="iot-solution-accelerators"></a>IoT 솔루션 가속기
Azure IoT Suite에 필요한 모든 서비스는 Azure Germany에서 사용할 수 있습니다. 

### <a name="variations"></a>변형
Azure Germany의 Azure IoT Suite의 홈페이지는 글로벌 Azure 페이지와 다릅니다.

## <a name="solution-accelerators"></a>솔루션 가속기
다음 솔루션 가속기 중 하나로 시작할 수 있습니다. 

### <a name="remote-monitoring"></a>원격 모니터링
원격 모니터링 솔루션 가속기는 원격 위치에서 실행되는 여러 컴퓨터에 대한 엔드투엔드 모니터링 솔루션을 구현합니다. 솔루션은 비즈니스 시나리오의 제네릭 구현을 제공하는 핵심 Azure 서비스를 결합합니다. 솔루션을 고유한 구현을 위한 시작점으로 사용하고 사용자의 특정 비즈니스 요구 사항에 맞게 사용자 지정할 수 있습니다.

### <a name="predictive-maintenance"></a>예측 유지 관리
예측 유지 관리 솔루션 가속기는 오류가 발생할 가능성이 있는 경우 지점을 예측하는 비즈니스 시나리오에 대한 엔드투엔드 솔루션입니다. 유지 관리를 최적화하는 등의 작업에 솔루션을 사전에 사용할 수 있습니다. 솔루션은 Azure IoT Hub, Stream Analytics, Azure Machine Learning 작업 영역과 같은 주요 Azure IoT Suite 서비스를 결합합니다. 이 작업 영역에는 공용 샘플 데이터 집합에 따라 항공기 엔진의 RUL(잔여 수명)을 예측하는 모델이 포함되어 있습니다. 솔루션은 IoT 비즈니스 시나리오를 시작점으로 구현하여 고유한 특정 비즈니스 요구 사항을 충족하는 솔루션을 계획하고 구현합니다.


## <a name="deploying-the-solution-accelerator"></a>솔루션 가속기 배포

두 솔루션은 웹 사이트 또는 PowerShell을 통해 두 가지 방법으로 배포할 수 있습니다.

### <a name="deploy-via-website"></a>웹 사이트를 통해 배포

앞에서 언급한 홈페이지를 사용하여 [사전 구성된 솔루션에 대한 자습서](/previous-versions/azure/iot-accelerators/about-iot-accelerators)의 지침을 따릅니다.

### <a name="deploy-via-powershell"></a>PowerShell을 통해 배포

*원격 모니터링* 솔루션에 대한 전체 버전(Azure Resource Manager 템플릿 및 Visual Studio 사용)이 있습니다. [GitHub의 Azure-IoT-Remote-Monitoring 리포지토리](https://github.com/Azure/azure-iot-remote-monitoring)에서 다운로드합니다. PowerShell 배포는 Azure Germany와 같은 다른 환경에 사용할 수 있습니다. *환경* 매개 변수 "AzureGermanCloud"를 제공하면 다음과 유사하게 표시됩니다.

```powershell
build.cmd cloud debug AzureGermanCloud
```

Bing Maps는 현재 Azure Germany에서 사용할 수 없으므로 자동으로 구독할 수 없습니다. 글로벌 Azure에서 서비스를 구독하고 해당 서비스를 사용하여 이 문제를 해결할 수 있습니다. 

> [!NOTE]
> 여기에 설명된 대로 Bing Maps를 사용하는 경우, Azure Germany 환경을 그대로 둡니다.

방법은 다음과 같습니다.

1. **+신규** 를 클릭하고 **Enterprise용 Bing Maps API** 를 검색하고 프롬프트에 따라 글로벌 Azure Portal에서 Bing Maps API를 생성합니다.
2. 글로벌 Azure Portal에서 엔터프라이즈용 Bing Maps API 키를 가져옵니다. 
    1. Enterprise Bing Maps API가 글로벌 Azure Portal 있는 리소스 그룹을 찾습니다.
    2. **모든 설정** > **키 관리** 를 클릭합니다. 
    3. MasterKey와 QueryKey 두 개의 키를 볼 수 있습니다. QueryKey에 대한 값을 복사합니다.
3. [GitHub의 Azure-IoT-Remote-Monitoring](https://github.com/Azure/azure-iot-remote-monitoring)에서 최신 코드를 풀다운합니다.
4. `/docs/` 레포지토리 폴더에서 명령줄 배포 지침에 따라 클라우드 배포를 실행합니다. 
5. 배포를 실행한 후에, 루트 폴더에서 배포 중에 **.user.config** 파일이 생성되었는지 확인합니다. 텍스트 편집기에서 이 파일을 엽니다. 
6. `<setting name="MapApiQueryKey" value="" />` QueryKey에서 복사한 값을 포함하도록 다음 줄을 변경합니다.
7. 4단계를 반복하여 솔루션을 다시 배포합니다.
 


## <a name="next-steps"></a>다음 단계
추가 정보 및 업데이트를 보려면 [Azure Germany 블로그](/archive/blogs/azuregermany/)를 구독하세요.