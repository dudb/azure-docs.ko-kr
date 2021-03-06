---
title: "개발자 가이드 - 할당량 및 제한 | Microsoft Docs"
description: "Azure IoT Hub 개발자 가이드 - IoT Hub에 적용할 할당량 설명 및 예상되는 제한 동작"
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 425e1b08-8789-4377-85f7-c13131fae4ce
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: c18a1b16cb561edabd69f17ecebedf686732ac34
ms.openlocfilehash: c0f8c779d7f9552dc05ac3791b74c3d57cb1fe64


---
# <a name="reference---quotas-and-throttling"></a>참조 - 할당량 및 제한
## <a name="quotas-and-throttling"></a>할당량 및 제한
각 Azure 구독은 IoT Hub 최대 10개와 무료 허브 최대 1개를 가질 수 있습니다.

각 IoT Hub는 특정 SKU에서 특정한 단위 수로 프로비전됩니다. 자세한 내용은 [Azure IoT Hub 가격 책정][lnk-pricing]을 참조하세요. SKU와 단위 수는 보낼 수 있는 메시지의 최대 일일 할당량을 결정합니다.

또한 SKU는 IoT Hub가 모든 작업에 강제로 적용하는 조정 제한을 결정합니다.

## <a name="operation-throttles"></a>작업 제한
작업 제한은 분 범위에 적용하고 남용을 방지하고자 하는 속도 제한입니다. IoT Hub는 가능한 경우 오류를 반환 하지 않으려 하지만 제한이 너무 오랫동안 위반된 경우 예외를 반환하기 시작합니다.

강제로 적용된 제한 목록은 다음과 같습니다. 값은 개별 허브라고 합니다.

| 제한 | 무료 및 S1 허브 | S2 허브 | S3 허브 | 
| -------- | ------- | ------- | ------- |
| ID 레지스트리 작업(만들기, 검색, 나열, 업데이트 및 삭제) | 100/분/단위 | 100/분/단위 | 5000/분/단위 |
| 장치 연결 | 최대 100/초 또는 12/초/단위 <br/> 예를 들어 S1 단위가 2개인 경우 2\*12 = 초당 24개에 해당합니다. 하지만 단위 전체의 연결 수는 초당 100개 이상이어야 합니다. S1 단위가 9개인 경우 단위 전체에 대한 값은 108/초(9\*12)입니다. | 120/초/단위 | 6000/초/단위 |
| 장치->클라우드 보내기 | 최대 100/초 또는 12/초/단위 <br/> 예를 들어 S1 단위가 2개인 경우 2\*12 = 초당 24개에 해당합니다. 하지만 단위 전체의 연결 수는 초당 100개 이상이어야 합니다. S1 단위가 9개인 경우 단위 전체에 대한 값은 108/초(9\*12)입니다. | 120/초/단위 | 6000/초/단위 |
| 클라우드-장치 보내기 | 100/분/단위 | 100/분/단위 | 5000/분/단위 |
| 클라우드-장치 받기 <br/> (장치가 HTTP를 사용하는 경우에만)| 1000/분/단위 | 1000/분/단위| 50000/분/단위 |
| 파일 업로드 | 파일 업로드 알림 100개/분/단위 | 파일 업로드 알림 100개/분/단위 | 파일 업로드 알림 5000개/분/단위 |
| 직접 메서드 | 10/초/단위 | 30/초/단위 | 1500/초/단위 | 
| 쌍 읽기 | 10/초 | 최대 10/초 또는 1/초/단위 | 50/초/단위 |
| 쌍 업데이트 | 10/초 | 최대 10/초 또는 1/초/단위 | 50/초/단위 |
| 작업 연산 <br/> (만들기, 업데이트, 나열, 삭제) | 100/분/단위 | 100/분/단위 | 5000/분/단위 |
| 장치 단위 작업 연산 처리량 | 10/초 | 최대 10/초 또는 1/초/단위 | 50/초/단위 |

*장치 연결* 제한은 IoT Hub에서 동시에 연결되는 장치의 최대 수가 아니라 새 장치 연결을 설정할 수 있는 속도를 제어합니다. 이 제한은 IoT Hub에 대해 프로비전되는 단위의 수에 따라 달라집니다.

예를 들어 S1 단위 하나를 구매하는 경우 초당 연결 제한은 100개입니다. 즉, 장치 10만 개를 연결하려면 최소 1,000초(약 16분)가 걸립니다. 그러나 ID 레지스트리에 등록한 수만큼의 장치를 동시에 연결할 수 있습니다.

IoT Hub 제한 동작에 대한 자세한 설명을 보려면 블로그 게시물 [IoT Hub 제한][lnk-throttle-blog]을 참조하세요.

> [!NOTE]
> 지정된 시간에 IoT Hub에 프로비전된 단위 수를 늘려 할당량이나 조정 제한을 증가시킬 수 있습니다.
> 
> [!IMPORTANT]
> ID 레지스트리 작업은 장치 관리 및 프로비전 시나리오에서 런타임에 사용하기 위한 것입니다. 많은 수의 장치 ID 읽기 또는 업데이트는 [가져오기 및 내보내기 작업][lnk-importexport]을 통해 지원됩니다.
> 
> 

## <a name="other-limits"></a>기타 제한

IoT Hub는 다양한 기능에 대해 다른 한도를 적용합니다.

| 작업 | 제한 |
| --------- | ----- |
| 파일 업로드 URI | 10000 SAS URI는 한 번에 저장소 계정에 대해 나올 수 있습니다. <br/> 10 SAS URI/장치는 한 번에 나올 수 있습니다. |
| 작업 | 작업 기록은 30일까지 유지됩니다. <br/> 최대 동시 작업은 1개(무료 및 S1의 경우, 5(S2의 경우), 10(S3의 경우)입니다. |

## <a name="next-steps"></a>다음 단계
이 IoT Hub 개발자 가이드의 다른 참조 자료:

* [IoT Hub 끝점][lnk-devguide-endpoints]
* [장치 쌍 및 작업에 대한 IoT Hub 쿼리 언어][lnk-devguide-query]
* [IoT Hub MQTT 지원][lnk-devguide-mqtt]

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[lnk-throttle-blog]: https://azure.microsoft.com/blog/iot-hub-throttling-and-you/
[lnk-importexport]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities

[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md



<!--HONumber=Nov16_HO5-->


