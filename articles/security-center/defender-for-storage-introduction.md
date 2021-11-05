---
title: Microsoft Defender for Storage - 이점 및 기능
description: Microsoft Defender for Storage의 이점 및 기능에 대해 알아봅니다.
author: memildin
ms.author: memildin
ms.date: 02/04/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 0b6bf15e416d64d283f834bc9f91f15715d4fa7a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131428751"
---
# <a name="introduction-to-microsoft-defender-for-storage"></a>Microsoft Defender for Storage 소개

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

**Microsoft Defender for Storage** 는 스토리지 계정에 액세스하거나 악용하려는 비정상적이고 잠재적으로 유해한 시도를 탐지하는 Azure 네이티브 보안 인텔리전스 계층입니다. 보안 AI 및 [Microsoft Threat Intelligence](https://go.microsoft.com/fwlink/?linkid=2128684)의 고급 기능을 활용하여 상황별 보안 경고 및 권장 사항을 제공합니다.

보안 경고는 비정상 활동이 발생할 때 트리거됩니다. 이러한 경고는 Microsoft Defender for Cloud에 표시되며, 의심스러운 활동의 세부 정보와 위협을 조사하고 해결하는 방법에 대한 권장 사항을 포함하여 이메일을 통해 구독 관리자에게 전송됩니다.

## <a name="availability"></a>가용성

|양상|세부 정보|
|----|:----|
|릴리스 상태:|GA(일반 공급)|
|가격 책정:|**Microsoft Defender for Storage** 는 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/security-center/)에 표시된 대로 요금이 청구됩니다.|
|보호된 스토리지 형식:|[Blob Storage](https://azure.microsoft.com/services/storage/blobs/)<br>[Azure 파일](../storage/files/storage-files-introduction.md)<br>[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)|
|클라우드:|:::image type="icon" source="./media/icons/yes-icon.png"::: 상용 클라우드<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Azure Government<br>:::image type="icon" source="./media/icons/no-icon.png"::: Azure 중국 21Vianet|
|||


## <a name="what-are-the-benefits-of-microsoft-defender-for-storage"></a>Microsoft Defender for Storage의 이점은 무엇인가요?

Microsoft Defender for Storage는 다음을 제공합니다.

- **Azure 네이티브 보안** - 한 번의 클릭으로 Defender for Storage는 Azure Blob, Azure Files 및 Data Lakes에 저장된 데이터를 보호합니다. Azure 네이티브 서비스인 Defender for Storage는 Azure에서 관리하는 모든 데이터 자산에 중앙 집중화된 보안을 제공하며 Microsoft Sentinel과 같은 다른 Azure 보안 서비스와 통합됩니다.
- **다양한 검색 제품군** - Microsoft Threat Intelligence로 구동되는 Defender for Storage의 검색은 익명 액세스, 손상된 자격 증명, 사회 공학, 권한 남용 및 악의적인 콘텐츠 등의 주요 스토리지 위협을 다룹니다.
- **대규모 응답** - Defender for Cloud의 자동화 도구를 사용하면 식별된 위협을 더 쉽게 예방하고 대응할 수 있습니다. [Defender for Cloud 트리거에 대한 응답 자동화](workflow-automation.md)에서 자세히 알아보세요.

:::image type="content" source="media/defender-for-storage-introduction/defender-for-storage-high-level-overview.png" alt-text="Microsoft Defender for Storage의 기능에 대한 개략적인 개요.":::


## <a name="what-kind-of-alerts-does-microsoft-defender-for-storage-provide"></a>Microsoft Defender for Storage는 어떤 종류의 경고를 제공하나요?

다음과 같은 경우에 보안 경고가 트리거됩니다.

- **의심스러운 액세스 패턴** - Tor 종료 노드 또는 Microsoft 위협 인텔리전스에서 의심스러운 것으로 간주되는 IP에서 성공한 액세스 등
- **의심스러운 활동** - 비정상적인 데이터 추출 또는 비정상적인 액세스 권한 변경 등
- **악의적인 콘텐츠 업로드** - 잠재적인 맬웨어 파일(해시 평판 분석 기반) 또는 피싱 콘텐츠 호스팅 등

경고에는 경고를 트리거한 인시던트의 세부 정보와 위협을 조사하고 완화하는 방법에 대한 권장 사항이 포함됩니다. 경고는 Microsoft Sentinel이나 기타 타사 SIEM 또는 기타 외부 도구로 내보낼 수 있습니다.

> [!TIP]
> 구독 수준에서 [Microsoft Defender for Storage를 구성](../storage/common/azure-defender-storage-configure.md?tabs=azure-security-center)하는 것이 모범 사례이지만 [개별 스토리지 계정에서 구성](../storage/common/azure-defender-storage-configure.md?tabs=azure-portal)할 수도 있습니다.


## <a name="what-is-hash-reputation-analysis-for-malware"></a>맬웨어에 대한 해시 평판 분석이란?

업로드된 파일이 의심스러운지 확인하기 위해 Microsoft Defender for Storage는 [Microsoft 위협 인텔리전스](https://go.microsoft.com/fwlink/?linkid=2128684)에서 지원하는 해시 평판 분석을 사용합니다. 위협 방지 도구는 업로드된 파일을 검사하는 것이 아니라 스토리지 로그를 검사하고 새로 업로드된 파일의 해시를 알려진 바이러스, 트로이 목마, 스파이웨어 및 랜섬웨어의 해시와 비교합니다. 

파일에 맬웨어가 들어 있는 것으로 의심되면 Defender for Cloud는 경고를 표시하고 경우에 따라 의심스러운 파일을 삭제하기 위해 스토리지 소유자에 삭제 승인을 요청하는 이메일을 보낼 수 있습니다. 해시 평판 분석 결과, 맬웨어를 포함하고 있는 것으로 보이는 파일을 자동으로 제거하도록 설정하려면 ["스토리지 계정에 잠재적 맬웨어가 업로드"되었다는 내용의 경고 발생 시 트리거되는 워크플로 자동화를 배포](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-respond-to-potential-malware-uploaded-to-azure-storage/ba-p/1452005)합니다.

> [!NOTE]
> Defender for Cloud의 위협 방지 기능을 사용하도록 설정하려면 해당 워크로드가 포함된 구독에서 향상된 보안 기능을 사용하도록 설정해야 합니다.
>
> **Microsoft Defender for Storage** 는 구독 수준 또는 리소스 수준에서 사용하도록 설정할 수 있습니다.

## <a name="trigger-a-test-alert-for-microsoft-defender-for-storage"></a>Microsoft Defender for Storage에 대한 테스트 경고 트리거

사용자 환경의 Microsoft Defender for Storage에서 보안 경고를 테스트하려면 다음 단계를 수행하여 "Tor 출구 노드에서 스토리지 계정으로 액세스"라는 경고를 생성합니다.

1. Microsoft Defender for Storage를 사용하도록 설정한 상태에서 스토리지 계정을 엽니다.
1. 사이드바에서 "컨테이너"를 선택하고 기존 컨테이너를 열거나 새 컨테이너를 만듭니다.

    :::image type="content" source="media/defender-for-storage-introduction/opening-storage-container.png" alt-text="Azure Storage 계정에서 Blob 컨테이너 열기." lightbox="media/defender-for-storage-introduction/opening-storage-container.png":::

1. 해당 컨테이너에 파일을 업로드합니다.

    > [!CAUTION]
    > 중요한 데이터가 포함된 파일을 업로드하지 마세요.

1. 업로드된 파일에서 상황에 맞는 메뉴를 사용하여 "SAS 생성"을 선택합니다.

    :::image type="content" source="media/defender-for-storage-introduction/generate-sas.png" alt-text="Blob 컨테이너의 파일에 대한 SAS 생성 옵션.":::

1. 기본 옵션을 그대로 두고 **SAS 토큰 및 URL 생성** 을 선택합니다.

1. 생성된 SAS URL을 복사합니다.

1. 로컬 머신에서 원하는 Tor 브라우저를 엽니다.

    > [!TIP]
    > Tor 프로젝트 사이트 [https://www.torproject.org/download/](https://www.torproject.org/download/)에서 Tor를 다운로드할 수 있습니다.

1. Tor 브라우저에서 SAS URL로 이동합니다.

1. 3단계에서 업로드한 파일을 다운로드합니다.

    2시간 내에 Defender for Cloud에서 다음과 같은 보안 경고를 받게 됩니다.

    :::image type="content" source="media/defender-for-storage-introduction/tor-access-alert-storage.png" alt-text="Tor 출구 노드에서의 액세스와 관련된 보안 경고.":::

## <a name="next-steps"></a>다음 단계

이 문서에서는 Microsoft Defender for Storage에 대해 알아보았습니다.

관련 자료는 다음 문서를 참조하세요. 

- 경고가 Defender for Cloud에서 생성되거나 Defender for Cloud가 다른 보안 제품으로부터 수신하는지에 관계없이 내보낼 수 있습니다. 경고를 Microsoft Sentinel, 타사 SIEM 또는 기타 외부 도구로 내보내려면 [SIEM으로 경고 내보내기](continuous-export.md)의 지침을 따릅니다.
- [Advanced Defender for Storage를 사용하도록 설정하는 방법](../storage/common/azure-defender-storage-configure.md)
- [Microsoft Defender for Storage 경고 목록](alerts-reference.md#alerts-azurestorage)
- [Microsoft의 위협 인텔리전스 기능](https://go.microsoft.com/fwlink/?linkid=2128684)