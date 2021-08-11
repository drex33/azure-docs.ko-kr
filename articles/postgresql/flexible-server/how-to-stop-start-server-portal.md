---
title: 중지/시작 - Azure Portal - Azure Database for PostgreSQL 유연한 서버
description: 이 문서에서는 Azure Portal을 통해 Azure Database for PostgreSQL에서 작업을 중지/시작하는 방법에 대해 설명합니다.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: a08c9244644228bd6d9154bdc4882fc91334bc62
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/02/2021
ms.locfileid: "110797377"
---
# <a name="stopstart-an-azure-database-for-postgresql---flexible-server-preview-using-azure-portal"></a>Azure Portal을 사용하여 Azure Database for PostgreSQL Flexible Server(미리 보기) 중지/시작

> [!IMPORTANT]
> Azure Database for PostgreSQL - 유연한 서버는 현재 미리 보기로 제공됩니다.

이 문서에서는 유연한 서버를 중지하고 시작하는 단계별 지침을 제공합니다.

## <a name="pre-requisites"></a>필수 구성 요소

이 방법 가이드를 완료하려면 다음이 필요합니다.

-   Azure Database for PostgreSQL 유연한 서버가 있어야 합니다.

## <a name="stop-a-running-server"></a>실행 중인 서버 중지

1.  [Azure Portal](https://portal.azure.com/)에서 중지하려는 유연한 서버를 선택합니다.

2.  **개요** 페이지의 도구 모음에서 **중지** 단추를 클릭합니다.

> [!NOTE]
> 서버가 중지된 후에는 유연한 서버에서 다른 관리 작업을 사용할 수 없습니다.

중지된 서버는 7일 후에 자동으로 다시 시작된다는 점을 알아두세요. 보류 중인 유지 관리 업데이트는 다음에 서버를 시작할 때 적용됩니다.

## <a name="start-a-stopped-server"></a>중지된 서버 시작

1.  [Azure Portal](https://portal.azure.com/)에서 시작하려는 유연한 서버를 선택합니다.

2.  **개요** 페이지의 도구 모음에서 **시작** 단추를 클릭합니다.

> [!NOTE]
> 서버가 시작된 후에는 이제 유연한 서버에서 모든 관리 작업을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Database for PostgreSQL 유연한 서버의 계산 및 스토리지 옵션](./concepts-compute-storage.md)에 대한 자세한 정보를 알아봅니다
