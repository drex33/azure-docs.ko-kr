---
title: Azure Stack Edge Pro 디바이스의 자동 관리 로그 수집 이해
description: Azure Stack Edge Pro 디바이스에서 자동 관리 로그 수집을 수행하는 방법과 사용하지 않도록 설정하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/26/2021
ms.author: alkohli
ms.openlocfilehash: ef54d3a252baa8d84588481b2a73d1b365bb8c13
ms.sourcegitcommit: bb1c13bdec18079aec868c3a5e8b33ef73200592
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/27/2021
ms.locfileid: "114721396"
---
# <a name="proactive-log-collection-on-your-azure-stack-edge-device"></a>Azure Stack Edge 디바이스의 자동 관리 로그 수집

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

자동 관리 로그 수집은 Azure Stack Edge 디바이스에서 시스템 상태 지표를 수집하여 디바이스 문제를 효율적으로 해결하는 데 도움이 됩니다. 자동 관리 로그 수집은 기본적으로 사용하도록 설정됩니다. 이 문서에서는 기록되는 항목, Microsoft에서 데이터를 처리하는 방법 및 자동 관리 로그 수집을 사용하거나 사용하지 않게 설정하는 방법을 설명합니다.

## <a name="about-proactive-log-collection"></a>자동 관리 로그 수집

Microsoft 고객 지원 및 엔지니어링 팀은 Azure Stack Edge 디바이스의 시스템 로그를 사용하여 작업 중에 발생할 수 있는 문제를 효율적으로 식별하고 해결합니다. 자동 관리 로그 수집은 고객의 Azure Stack Edge 어플라이언스에 의해 문제/이벤트가 감지되었다고 Microsoft에 경고하는 방법입니다. 추적되는 이벤트는 [자동 관리 로그 수집 지표](#proactive-log-collection-indicators)를 참조하세요. 문제와 관련한 지원 로그는 Microsoft에서 관리하고 제어하는 Azure Storage 계정에 자동으로 업로드됩니다. Microsoft 지원 및 Microsoft 엔지니어는 이러한 지원 로그를 검토하여 고객의 문제를 해결하기 위한 최상의 조치 과정을 결정합니다.

> [!NOTE]
> 이러한 로그는 디버깅 목적으로만 사용되며 문제가 있는 경우 고객에게 지원을 제공합니다.


## <a name="enabling-proactive-log-collection"></a>자동 관리 로그 수집

자동 관리 로그 수집은 기본적으로 사용하도록 설정됩니다. 로컬 UI를 통해 디바이스를 활성화하려고 할 때 자동 관리 로그 수집을 사용하지 않도록 설정할 수 있습니다. 

1. 디바이스의 로컬 웹 UI에서 **시작** 페이지로 이동합니다.

2. **활성화** 타일에서 **활성화** 를 선택합니다. 

    ![로컬 웹 UI "클라우드 세부 정보" 페이지 1](./media/azure-stack-edge-pro-r-deploy-activate/activate-1.png)

3. **활성화** 창에서 다음을 수행합니다.

   1. [Azure Stack Edge Pro R의 활성화 키 가져오기](azure-stack-edge-pro-r-deploy-prep.md#get-the-activation-key)에서 가져온 **활성화 키** 를 입력합니다.

      활성화되면 자동 관리 로그 수집을 기본적으로 사용하게 되고 Microsoft에서 디바이스의 상태에 따라 로그를 수집할 수 있습니다. 이 로그는 Azure Storage 계정에 업로드됩니다. 

      자동 관리 로그 수집을 사용하지 않도록 설정하여 Microsoft에서 로그 수집을 중지할 수 있습니다.

   1. 디바이스에서 자동 관리 로그 수집을 사용하지 않도록 설정하려면 **사용 안 함** 을 선택합니다.

   1. **활성화** 를 선택합니다.

   ![로컬 웹 UI "클라우드 세부 정보" 페이지 2](./media/azure-stack-edge-pro-r-deploy-activate/activate-2.png)

## <a name="proactive-log-collection-indicators"></a>자동 관리 로그 수집 지표

자동 관리 로그 수집을 사용하도록 설정하면 디바이스에서 다음 이벤트 중 하나가 검색되면 로그가 자동으로 업로드됩니다.  


|경고/오류/조건  |설명  |
|---------|---------|
|AcsUnhealthyCondition     |Azure Consistent Services가 비정상입니다.         |
|IOTEdgeAgentNotRunningCondition      |IoT Edge 에이전트가 실행되고 있지 않습니다.         |
|UpdateInstallFailedEvent | 업데이트를 설치할 수 없습니다.        |

 
Microsoft는 계속해서 이전 목록에 새 이벤트를 추가합니다. 새 이벤트에 대한 추가 동의가 필요하지 않습니다. 최신 자동 관리 로그 수집 지표에 대한 자세한 내용은 이 페이지를 참조하세요.    
 

## <a name="other-log-collection-methods"></a>기타 로그 수집 방법

검색된 특정 문제와 관련된 특정 로그를 수집하는 자동 관리 로그 수집 외에도, 다른 로그 수집을 통해 시스템 상태 및 동작을 심층적으로 이해할 수 있습니다. 일반적으로 이러한 다른 로그는 지원 요청 중에 수집되거나 디바이스의 원격 분석 데이터를 기반으로 Microsoft에서 트리거할 수 있습니다.

## <a name="handling-data"></a>데이터 처리

자동 관리 로그 수집을 사용하도록 설정하면 고객은 여기에서 설명하는 대로 Azure Stack Edge 디바이스에서 로그를 수집하는 Microsoft에 동의합니다. 또한 고객은 Microsoft에서 관리 및 제어하는 Azure Storage 계정에서 해당 로그의 업로드 및 보존을 승인하고 동의합니다.

Microsoft는 시스템 상태 및 문제 해결에만 데이터를 사용합니다. 데이터는 고객 동의 없이 마케팅, 광고 또는 기타 상업적 용도로 사용되지 않습니다. 

Microsoft에서 수집하는 데이터는 표준 개인 정보 취급 방침에 따라 처리됩니다. 고객이 자동 관리 로그 수집에 대한 동의를 철회하기로 결정한 경우 해지 전에 동의를 통해 수집된 데이터는 영향을 받지 않습니다.

## <a name="next-steps"></a>다음 단계

[지원 패키지를 수집](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)하는 방법에 대해 알아봅니다.