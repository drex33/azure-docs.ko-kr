---
title: 'ML 스튜디오(클래식): 데이터 내보내기 및 삭제하기 - Azure'
description: Machine Learning 스튜디오(클래식)가 저장하는 제품 내 데이터는 Azure Portal 및 인증된 REST API를 통해 내보내고 삭제할 수 있습니다. 원격 분석 데이터는 Azure 개인 정보 보호 포털을 통해 액세스할 수 있습니다. 이 문서에서 그 방법을 보여줍니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 05/25/2018
ms.openlocfilehash: 0f590f52224eed6fd32dbb2af0297669b99a3fe8
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122688856"
---
# <a name="export-and-delete-in-product-user-data-from-machine-learning-studio-classic"></a>Machine Learning Studio(클래식)에서 제품 내 사용자 데이터 내보내기 및 삭제

**적용 대상:**  ![적용 대상:](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio(클래식)  ![적용되지 않는 대상:](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

[!INCLUDE [ML Studio (classic) retirement](../../../includes/machine-learning-studio-classic-deprecation.md)]

Azure Portal, 스튜디오(클래식) 인터페이스, PowerShell, 인증된 REST API를 사용하여 Machine Learning 스튜디오(클래식)에서 저장한 제품 내 데이터를 삭제하거나 내보낼 수 있습니다. 이 문서에서는 이러한 작업을 수행하는 방법에 대해 설명합니다. 

원격 분석 데이터는 Azure 개인 정보 보호 포털을 통해 액세스할 수 있습니다. 

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-kinds-of-user-data-does-studio-classic-collect"></a>스튜디오(클래식)에서 수집하는 사용자 데이터의 종류는 무엇인가요?

이 서비스의 경우 사용자 데이터는 작업 영역에 액세스하도록 권한이 부여된 사용자 및 서비스와의 사용자 상호 작용에 대한 원격 분석 레코드에 대한 정보로 구성됩니다.

Machine Learning 스튜디오(클래식)에는 다음 두 가지 종류의 사용자 데이터가 있습니다.
- **개인 계정 데이터:** 계정과 연결된 계정 ID 및 이메일 주소
- **고객 데이터:** 분석을 위해 업로드한 데이터

## <a name="studio-classic-account-types-and-how-data-is-stored"></a>스튜디오(클래식) 계정 유형 및 데이터 저장 방식

Machine Learning 스튜디오(클래식)에는 다음 세 가지 종류의 계정이 있습니다. 보유한 계정의 종류에 따라 데이터가 저장되는 방법과 데이터를 삭제하거나 내보내는 방법이 결정됩니다.

- **게스트 작업 영역** 은 익명 체험 계정입니다. 이메일 주소 또는 암호와 같은 자격 증명을 제공하지 않고 등록합니다.
    -  게스트 작업 영역이 만료되면 데이터가 제거됩니다.
    - 게스트 사용자는 UI, REST API 또는 PowerShell 패키지를 통해 고객 데이터를 내보낼 수 있습니다.
- **체험 작업 영역** 은 Microsoft 계정 자격 증명(이메일 주소 및 암호)으로 로그인하는 체험 계정입니다.
    - DSR(데이터 주체 권한) 요청에 따라 개인 및 고객 데이터를 내보내고 삭제할 수 있습니다.
    - UI, REST API 또는 PowerShell 패키지를 통해 고객 데이터를 내보낼 수 있습니다.
    - Azure AD 계정을 사용하지 않는 체험 작업 영역의 경우 개인 정보 보호 포털을 사용하여 원격 분석을 내보낼 수 있습니다.
    - 작업 영역을 삭제하면 모든 개인 고객 데이터가 삭제됩니다.
- **표준 작업 영역** 은 로그인 자격 증명으로 액세스하는 유료 계정입니다.
    - DSR 요청에 따라 개인 및 고객 데이터를 내보내고 삭제할 수 있습니다.
    - Azure 개인 정보 보호 포털을 통해 데이터에 액세스할 수 있습니다.
    - UI, REST API 또는 PowerShell 패키지를 통해 개인 및 고객 데이터를 내보낼 수 있습니다.
    - Azure Portal에서 데이터를 삭제할 수 있습니다.

## <a name="delete-workspace-data-in-studio-classic"></a><a name="delete"></a>스튜디오(클래식)에서 작업 영역 데이터 삭제 

### <a name="delete-individual-assets"></a>개별 자산 삭제

사용자가 작업 영역에서 자산을 선택한 다음, 삭제 단추를 선택하여 자산을 삭제할 수 있습니다.

![Machine Learning 스튜디오(클래식)에서 자산 삭제](./media/export-delete-personal-data-dsr/delete-studio-asset.png)

### <a name="delete-an-entire-workspace"></a>전체 작업 영역 삭제

사용자가 전체 작업 영역을 삭제할 수도 있습니다.
- 유료 작업 영역: Azure Portal을 통해 삭제합니다.
- 체험 작업 영역: **설정** 창에서 삭제 단추를 사용합니다.

![Machine Learning 스튜디오(클래식)에서 사용 가능한 작업 영역 삭제](./media/export-delete-personal-data-dsr/delete-studio-data-workspace.png)
 
## <a name="export-studio-classic-data-with-powershell"></a>PowerShell을 사용하여 스튜디오(클래식) 데이터 내보내기
PowerShell을 사용하면 명령을 통해 Machine Learning 스튜디오(클래식)의 모든 정보를 이식 가능 형식으로 내보낼 수 있습니다. 자세한 내용은 [Machine Learning 스튜디오(클래식)용 PowerShell 모듈](powershell-module.md) 문서를 참조하세요.

## <a name="next-steps"></a>다음 단계

웹 서비스 및 약정 플랜 청구에 대한 설명서는 [Machine Learning 스튜디오(클래식) REST API 참조](/rest/api/machinelearning/)를 참조하세요.