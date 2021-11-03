---
title: Azure Data Box 반송 자습서 | Microsoft Docs
description: 이 자습서에서는 배송 준비, Data Box 배송, 데이터 업로드 확인 및 Data Box에서 데이터 지우기를 포함하여 Azure Data Box를 반환하는 방법에 대해 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 10/26/2021
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 055ffcb662b5a12dfefe194b08e293f277ba5005
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131005618"
---
::: zone target="docs"

# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>자습서: Azure Data Box 반송 및 Azure에 대한 데이터 업로드 확인

::: zone-end

::: zone target="chromeless"

## <a name="return-data-box-and-verify-data-upload-to-azure"></a>Data Box 반환 및 Azure에 대한 데이터 업로드 확인

::: zone-end

::: zone target="docs"

이 자습서에서는 Azure Data Box를 반송하고 Azure에 업로드된 데이터를 확인하는 방법을 설명합니다.

이 자습서에서는 다음과 같은 주제에 대해 학습합니다.

> [!div class="checklist"]
>
> * 사전 요구 사항
> * 배송 준비
> * Microsoft에 Data Box 배송
> * Azure에 대한 데이터 업로드 확인
> * Data Box에서 데이터 지우기

## <a name="prerequisites"></a>필수 구성 요소

시작하기 전에 다음 사항을 확인합니다.

* [자습서: Azure Data Box에 데이터 복사 및 확인](data-box-deploy-copy-data.md)을 완료했는지 확인합니다.
* 복사 작업이 완료되었으며 **연결 및 복사** 페이지에 오류가 없습니다. 복사 작업이 진행 중이거나 **연결 및 복사** 페이지에 오류가 있는 경우에는 **배송 준비** 를 실행할 수 없습니다.

## <a name="prepare-to-ship"></a>배송 준비

[!INCLUDE [data-box-prepare-to-ship](../../includes/data-box-prepare-to-ship.md)]

::: zone-end

::: zone target="chromeless"

데이터 복사가 완료되면 디바이스를 준비하고 배송합니다. 디바이스가 Azure 데이터 센터에 도달하면 데이터가 자동으로 Azure에 업로드됩니다.

## <a name="prepare-to-ship"></a>배송 준비

배송을 준비하기 전에 복사 작업이 완료되었는지 확인합니다.

1. 로컬 웹 UI에서 **배송 준비** 페이지로 이동하여 배송 준비를 시작합니다.
2. 로컬 웹 UI에서 디바이스를 끕니다. 디바이스에서 케이블을 뽑습니다.

다음 단계는 디바이스를 반송하는 위치에 따라 결정됩니다.

::: zone-end


## <a name="ship-data-box-back"></a>Data Box 반송

디바이스에 대한 데이터 복사가 완료되고 **배송 준비** 실행이 성공했는지 확인합니다. 

디바이스를 배송하는 지역에 따라 절차가 다릅니다. 많은 국가/지역에서 Microsoft 관리 배송 또는 자체 관리 배송을 사용할 수 있습니다.

### <a name="microsoft-managed-shipping"></a>Microsoft 관리형 배송

Microsoft 관리형 배송을 사용하는 경우 배송할 지역에 대한 지침을 따르세요.

## <a name="us--canada"></a>[미국 및 캐나다](#tab/in-us-canada)

[!INCLUDE [data-box-shipping-in-us-canada](../../includes/data-box-shipping-in-us-canada.md)]

## <a name="eu"></a>[EU](#tab/in-europe)

[!INCLUDE [data-box-shipping-in-eu](../../includes/data-box-shipping-in-eu.md)]

**독일 또는 스위스에서 배송하는 경우**, [자체 관리형 배송을 사용](#self-managed-shipping)할 수도 있습니다.

## <a name="uk"></a>[UK](#tab/in-uk)

[!INCLUDE [data-box-shipping-in-uk](../../includes/data-box-shipping-in-uk.md)]

## <a name="australia"></a>[오스트레일리아](#tab/in-australia)

[!INCLUDE [data-box-shipping-in-australia](../../includes/data-box-shipping-in-australia.md)]

## <a name="japan"></a>[일본](#tab/in-japan)

[!INCLUDE [data-box-shipping-in-japan](../../includes/data-box-shipping-in-japan.md)]

## <a name="singapore"></a>[싱가포르](#tab/in-singapore)

[!INCLUDE [data-box-shipping-in-singapore](../../includes/data-box-shipping-in-singapore.md)]

## <a name="hong-kong"></a>[홍콩](#tab/in-hk)

[!INCLUDE [data-box-shipping-in-hk](../../includes/data-box-shipping-in-hk.md)]

## <a name="korea"></a>[한국](#tab/in-korea)

[!INCLUDE [data-box-shipping-in-korea](../../includes/data-box-shipping-in-korea.md)]

## <a name="s-africa"></a>[남아프리카 공화국](#tab/in-sa)

[!INCLUDE [data-box-shipping-in-sa](../../includes/data-box-shipping-in-sa.md)]

## <a name="uae"></a>[아랍에미리트](#tab/in-uae)

[!INCLUDE [data-box-shipping-in-uae](../../includes/data-box-shipping-in-uae.md)]

### <a name="self-managed-shipping"></a>자체 관리형 배송

[!INCLUDE [data-box-shipping-self-managed](../../includes/data-box-shipping-self-managed.md)]

---

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Azure에 대한 데이터 업로드 확인

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Data Box에서 데이터 지우기
 
Azure에 대한 업로드가 완료되면 Data Box 디스크의 데이터가 [NIST SP800-88 개정 1 지침](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi)에 따라 지워집니다.

::: zone-end


::: zone target="docs"

## <a name="verify-data-upload-to-azure"></a>Azure에 대한 데이터 업로드 확인

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end

