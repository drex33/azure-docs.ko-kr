---
title: 미사용 데이터의 Speech Service 암호화
titleSuffix: Azure Cognitive Services
description: Microsoft는 Microsoft에서 관리하는 암호화 키를 제공하며 CMK(고객 관리형 키)라고 하는 고유한 키로 Cognitive Services 구독을 관리할 수도 있습니다. 이 문서에서는 Speech Service의 미사용 데이터 암호화에 대해 설명합니다.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/14/2021
ms.author: egeaney
ms.openlocfilehash: cdf8276904fda5098b3192779e0372b4a1bcc9d2
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113766612"
---
# <a name="speech-service-encryption-of-data-at-rest"></a>미사용 데이터의 Speech Service 암호화

Speech Service는 클라우드에 데이터를 저장할 때 자동으로 암호화합니다. Speech Service 암호화는 데이터를 보호하고 조직의 보안 및 규정 준수 약정에 부합하는 데 도움이 됩니다.

## <a name="about-cognitive-services-encryption"></a>Cognitive Services 암호화 정보

데이터는 [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2)를 준수하는 [256비트 AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 암호화를 사용하여 암호화 및 암호 해독됩니다. 암호화 및 암호 해독은 투명하므로 암호화 및 액세스가 자동으로 관리됩니다. 데이터는 기본적으로 안전하며 암호화를 활용하기 위해 코드 또는 애플리케이션을 수정할 필요가 없습니다.

## <a name="about-encryption-key-management"></a>암호화 키 관리 정보

Custom Speech 및 Custom Voice를 사용하는 경우 Speech Service는 클라우드에 다음 데이터를 저장할 수 있습니다.  

* 음성 추적 데이터 - 사용자 지정 엔드포인트에 대한 추적을 설정한 경우에만
* 업로드된 학습 및 테스트 데이터

기본적으로 데이터는 Microsoft 스토리지에 저장되며 구독은 Microsoft에서 관리하는 암호화 키를 사용합니다. 스토리지 계정을 직접 준비할 수도 있습니다. 저장소에 대한 액세스는 관리 ID에 의해 관리되며 Speech Service는 음성 추적 데이터, 사용자 지정 학습 데이터 및 사용자 지정 모델과 같은 자체 데이터에 직접 액세스할 수 없습니다.

관리 ID에 대한 자세한 내용은 [관리 ID란?](../../active-directory/managed-identities-azure-resources/overview.md)을 참조하세요.

한편 사용자 지정 명령을 사용할 때는 사용자 고유의 암호화 키를 사용하여 구독을 관리할 수 있습니다. CMK(고객 관리 키)(BYOK(Bring Your Own Key)라고도 함)를 사용하여 훨씬 더 유연하게 액세스 제어를 만들고, 회전하고, 해제하고, 취소할 수 있습니다. 데이터를 보호하는 데 사용되는 암호화 키를 감사할 수도 있습니다. 사용자 지정 명령 및 CMK에 대한 자세한 내용은 [사용자 지정 명령 미사용 데이터 암호화](custom-commands-encryption-of-data-at-rest.md)를 참조하세요.

## <a name="bring-your-own-storage-byos-for-customization-and-logging"></a>사용자 지정 및 로깅을 위한 BYOS(Bring Your Own Storage)

자체 저장소를 가져오기 위한 액세스를 요청하려면  [Speech Service - BYOS(Bring Your Own Storage) 요청 양식](https://aka.ms/cogsvc-cmk)을 작성하여 제출합니다. 승인되면 사용자 지정 및 로깅에 필요한 데이터를 저장하기 위해 자체 스토리지 계정을 만들어야 합니다. 스토리지 계정을 추가할 때 Speech Service 리소스는 시스템 할당 관리 ID를 사용하도록 설정합니다.

> [!IMPORTANT]
> BYOS 기능이 사용되는 Speech 리소스를 만드는 데 사용하는 사용자 계정에는 [Azure 구독 범위의 소유자 역할](../../cost-management-billing/manage/add-change-subscription-administrator.md#to-assign-a-user-as-an-administrator)이 할당되어야 합니다. 할당되지 않으면 리소스를 프로비저닝하는 동안 권한 부여 오류가 발생합니다.

시스템 할당 관리 ID를 사용하도록 설정하면 이 리소스가 AAD(Azure Active Directory)에 등록됩니다. 등록 후에는 관리 ID에 스토리지 계정에 대한 액세스 권한이 부여됩니다. 관리 ID에 관한 자세한 내용은 [관리 ID란?](../../active-directory/managed-identities-azure-resources/overview.md)을 참조하세요.

> [!IMPORTANT]
> 시스템 할당 관리 ID를 사용하지 않도록 설정하면 스토리지 계정에 대한 액세스가 제거됩니다. 이로 인해 스토리지 계정에 액세스해야 하는 Speech Service 부분의 작동이 중지됩니다.  

Speech Service는 현재 고객 Lockbox를 지원하지 않습니다. 그러나 고객 데이터는 BYOS를 사용하여 저장할 수 있으므로 [고객 Lockbox](../../security/fundamentals/customer-lockbox-overview.md)와 유사한 데이터 제어를 수행할 수 있습니다. Speech Service 데이터는 Speech 리소스가 생성된 지역에서 유지되고 처리됩니다. 이는 미사용 데이터와 전송 중인 데이터에 적용됩니다. Custom Speech 및 Custom Voice와 같은 사용자 지정 기능을 사용할 때 모든 고객 데이터는 BYOS(사용된 경우) 및 Speech Service 리소스가 있는 동일한 지역에서 전송, 저장 및 처리됩니다.

> [!IMPORTANT]
> Microsoft는 고객 데이터를 사용하여 음성 모델을 개선하지 **않습니다**. 또한 엔드포인트 로깅이 사용하지 않도록 설정되고 사용자 지정이 사용되지 않은 경우에는 고객 데이터가 저장되지 않습니다. 

## <a name="next-steps"></a>다음 단계

* [Speech Service - BYOS(Bring Your Own Storage) 요청 양식](https://aka.ms/cogsvc-cmk)
* [관리 ID 란?](../../active-directory/managed-identities-azure-resources/overview.md)
