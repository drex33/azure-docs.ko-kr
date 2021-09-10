---
title: DevTest Labs에서 사용자 지정 이미지와 수식 비교 | Microsoft 문서
description: 사용자 환경에 가장 적합한 것이 무엇인지 결정할 수 있도록, VM 기반으로서 사용자 지정 이미지와 수식 사이의 차이점에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: f1fdb46decda2f72e64eff95cf57dba002168943
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2021
ms.locfileid: "122634006"
---
# <a name="compare-custom-images-and-formulas-in-devtest-labs"></a>DevTest Lab에서 사용자 지정 이미지와 수식 비교
[사용자 지정 이미지](devtest-lab-create-template.md) 및 [수식](devtest-lab-manage-formulas.md)은 모두 [새로 생성된 VM](devtest-lab-add-vm.md)의 기반으로 사용될 수 있습니다.  사용자 지정 이미지와 수식의 주요 차이점은 사용자 지정 이미지는 단지 VHD를 기반으로 한 이미지인 반면, 수식은 VHD *뿐만 아니라* 미리 구성된 설정(예: VM 크기, 가상 네트워크, 서브넷 및 아티팩트)을 기반으로 한 이미지라는 점입니다. 미리 구성된 설정은 VM을 만들 때 재정의될 수 있는 기본 값으로 설정됩니다. 

이 문서에서는 수식을 사용하는 것과 사용자 지정 이미지를 사용하는 것의 장단점을 알아봅니다.  “[VM에서 사용자 지정 이미지를 만드는 방법](devtest-lab-create-custom-image-from-vm-using-portal.md)” 및 “[FAQ](devtest-lab-faq.yml)”도 읽을 수 있습니다.

## <a name="custom-image-benefits"></a>사용자 지정 이미지 이점
사용자 지정 이미지는 원하는 환경에서 VM을 만드는 정적이고 변경할 수 없는 방식을 제공합니다. 

|장점|단점|
|----|----|
|<li>사용자 지정 이미지를 통한 VM 프로비전은 이미지를 통해 VM이 작동된 후에 변하는 것이 없을 정도로 빠릅니다. 즉, 사용자 지정 이미지는 설정이 없는 단순한 이미지이기 때문에 적용할 설정이 없습니다. <li>하나의 사용자 지정 이미지를 통해 만들어진 VM은 동일합니다.|<li>사용자 지정 이미지의 일부를 업데이트해야 하는 경우에는 이미지를 다시 만들어야 합니다. |

## <a name="formula-benefits"></a>수식 혜택
  
수식은 원하는 구성/설정을 통해 VM을 만드는 동적인 방식을 제공합니다.

|장점|단점|
|----|----|
|<li>환경이 변하면 아티팩트를 통해 즉시 캡처할 수 있습니다. 예를 들어 릴리스 파이프라인의 최신 비트를 사용하여 VM을 설치하거나 리포지토리의 최신 코드를 요청하려면, 대상 기본 이미지와 함께, 수식에 포함된 최신 비트를 배포하거나 최신 코드를 등록하는 <li>아티팩트만 지정하면 됩니다. 수식을 사용하여 VM을 만들 때 마다, 최신 비트/코드가 VM에 배포/등록됩니다.  <li>수식은 사용자 지정 이미지가 제공할 수 없는 기본 설정(예: VM 크기 및 가상 네트워크 설정)을 정의할 수 있습니다.  <li>수식에 저장된 설정은 기본 값으로 표시되지만 VM을 만들 때 수정될 수 있습니다. |<li> 수식을 통해 VM을 만들면 사용자 지정 이미지를 통해 VM을 만드는 것보다 시간이 더 소요됩니다.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
