---
title: 내보내기 순서로 Azure Data Box를 반송하는 자습서 | Microsoft Docs
description: 내보내기 순서가 완료된 후 Azure Data Box를 Microsoft로 배송하는 방법 알아보기
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 10/26/2021
ms.author: alkohli
ms.openlocfilehash: ea9a918ee32a3ada83651085290fa7c7b26c6e0e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131024375"
---
# <a name="tutorial-return-azure-data-box"></a>자습서: Azure Data Box 반환

이 자습서에서는 Azure Data Box를 반환하는 방법을 설명하고 디바이스가 Azure 데이터에서 수신되면 데이터가 지워집니다.

이 자습서에서는 다음과 같은 주제에 대해 학습합니다.

> [!div class="checklist"]
>
> * 사전 요구 사항
> * 배송 준비
> * Microsoft에 Data Box 배송
> * Data Box에서 데이터 지우기

## <a name="prerequisites"></a>필수 구성 요소

시작하기 전에 다음 사항을 확인합니다.

* [자습서: Azure Data Box에서 데이터 복사](data-box-deploy-export-copy-data.md)를 완료했습니다.
* 복사 작업이 완료되었습니다. 복사 작업이 진행 중이면 배송 준비를 실행할 수 없습니다.

## <a name="prepare-to-ship"></a>배송 준비

[!INCLUDE [data-box-export-prepare-to-ship](../../includes/data-box-export-prepare-to-ship.md)]

다음 단계는 디바이스를 반송하는 위치에 따라 결정됩니다.

## <a name="ship-data-box-back"></a>Data Box 반송

디바이스에서 데이터 복사가 완료되고 **배송 준비** 실행이 성공했는지 확인합니다.

디바이스를 배송하는 지역에 따라 절차가 다릅니다. 많은 국가/지역에서 Microsoft 관리 배송 또는 자체 관리 배송을 사용할 수 있습니다.

### <a name="microsoft-managed-shipping"></a>Microsoft 관리형 배송

Microsoft 관리형 배송을 사용하는 경우 배송할 지역에 대한 지침을 따르세요.

## <a name="us--canada"></a>[미국 및 캐나다](#tab/in-us-canada)

[!INCLUDE [data-box-shipping-in-us-canada](../../includes/data-box-shipping-in-us-canada.md)]

## <a name="eu"></a>[EU](#tab/in-eu)

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

## <a name="erasure-of-data-from-data-box"></a>Data Box에서 데이터 지우기

디바이스가 Azure 데이터 센터에 도달하면 Data Box는 [NIST SP 800-88 개정 1 지침](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi)에 따라 디스크의 데이터를 지웁니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음과 같은 항목에 대해 알아보았습니다.

> [!div class="checklist"]
> * 필수 구성 요소
> * 배송 준비
> * Microsoft에 Data Box 배송
> * Data Box에서 데이터 지우기

다음 문서로 넘어가서 Data Box를 관리하는 방법을 알아보세요.

> [!div class="nextstepaction"]
> [Azure Portal을 통해 Data Box 관리](./data-box-portal-admin.md)
