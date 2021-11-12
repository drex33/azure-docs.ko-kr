---
title: 사용자 지정 이미지 및 수식 비교
description: 사용자 환경에 가장 적합한 것이 무엇인지 결정할 수 있도록, VM 기반으로서 사용자 지정 이미지와 수식 사이의 차이점에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: 1eca53c8d38dc7cd51b7a91c2e40518e5adfc05f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132286605"
---
# <a name="compare-custom-images-and-formulas-in-devtest-labs"></a>DevTest Lab에서 사용자 지정 이미지와 수식 비교
[사용자 지정 이미지와](devtest-lab-create-template.md) [수식은](devtest-lab-manage-formulas.md) 모두 [새 랩 VM(가상 머신)을 만들기](devtest-lab-add-vm.md)위한 기반으로 사용할 수 있습니다. 사용자 지정 이미지와 수식 간의 주요 차이점은 사용자 지정 이미지는 단순히 VHD(가상 하드 드라이브)를 기반으로 하는 이미지라는 것입니다. 수식은 VHD 및 미리 구성된 설정을 기반으로 하는 이미지입니다. 미리 구성된 설정에는 VM 크기, 가상 네트워크, 서브넷 및 아티팩트 등이 포함될 수 있습니다. 이러한 미리 구성된 설정은 VM을 만들 때 재정의할 수 있는 기본값으로 설정됩니다. 

이 문서에서는 사용자 지정 이미지 사용과 수식 사용의 장단점을 알아봅니다.  [VM에서 사용자 지정 이미지를 만드는 방법](devtest-lab-create-custom-image-from-vm-using-portal.md) 및 [DevTest Labs FAQ를](devtest-lab-faq.yml)읽을 수도 있습니다.

## <a name="custom-image-benefits"></a>사용자 지정 이미지 이점
사용자 지정 이미지는 원하는 환경에서 VM을 만들 수 있는 정적, 불변의 방법을 제공합니다. 

|장점|단점|
|----|----|
|<li>사용자 지정 이미지에서 VM을 프로비전하는 것은 빠르며, VM이 이미지에서 회전된 후에는 아무 것도 변경되지 않습니다. 즉, 사용자 지정 이미지는 설정이 없는 이미지이기 때문에 적용할 설정이 없습니다. <li>하나의 사용자 지정 이미지를 통해 만들어진 VM은 동일합니다.|<li>사용자 지정 이미지의 일부 측면을 업데이트해야 하는 경우 이미지를 다시 만들어야 합니다. |

## <a name="formula-benefits"></a>수식 혜택
  
수식은 원하는 구성 및 설정에서 VM을 만드는 동적 방법을 제공합니다.

|장점|단점|
|----|----|
|<li>환경이 변하면 아티팩트를 통해 즉시 캡처할 수 있습니다. 예를 들어 릴리스 파이프라인의 최신 비트와 함께 VM을 설치하거나 리포지토리의 최신 코드를 등록하려면 수식을 사용합니다. 수식은 대상 기본 이미지와 함께 최신 비트를 배포하거나 최신 코드를 참여시키는 아티팩트 를 지정할 수 있습니다. 이 수식을 사용하여 VM을 만들 때마다 최신 비트 또는 코드가 배포되거나 VM에 등록됩니다.  <li>수식은 VM 크기 및 가상 네트워크 설정과 같이 사용자 지정 이미지가 제공할 수 없는 기본 설정을 정의할 수 있습니다.  <li>수식에 저장된 설정은 기본 값으로 표시되지만 VM을 만들 때 수정될 수 있습니다. |<li> 수식을 통해 VM을 만들면 사용자 지정 이미지를 통해 VM을 만드는 것보다 시간이 더 소요됩니다.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
