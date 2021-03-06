---
title: "DocumentDB 방화벽 지원 | Microsoft Docs"
description: "Azure DocumentDB 데이터베이스 계정에서 방화벽을 지원하도록 IP 액세스 제어 정책을 사용하는 방법을 알아봅니다."
keywords: "IP 액세스 제어, 방화벽 지원"
services: documentdb
author: shahankur11
manager: jhubbard
editor: 
tags: azure-resource-manager
documentationcenter: 
ms.assetid: c1b9ede0-ed93-411a-ac9a-62c113a8e887
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2016
ms.author: ankshah; kraman
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: eb3c5c2adbaedc4bfb1e68f26b88079aeabe50f5


---
# <a name="documentdb-firewall-support"></a>DocumentDB 방화벽 지원
Azure DocumentDB 데이터베이스 계정에 저장된 데이터를 보호하기 위해 DocumentDB는 강력한 HMAC(해시 기반 메시지 인증 코드)를 활용하는 암호 기반 [권한 부여 모델](https://msdn.microsoft.com/library/azure/dn783368.aspx)을 지원했습니다. 이제 DocumentDB는 암호 기반 권한 부여 모델 외에도 인바운드 방화벽 지원에 대한 정책 중심 IP 기반 액세스 제어를 지원합니다. 이 모델은 기존 데이터베이스 시스템의 방화벽 규칙과 매우 유사하며 DocumentDB 데이터베이스 계정에 추가 보안을 제공합니다. 이제 이 모델에서는 승인된 컴퓨터 및/또는 클라우드 서비스에서만 액세스할 수 있도록 DocumentDB 데이터베이스 계정을 구성할 수 있습니다. 이러한 승인된 컴퓨터 및 서비스에서 DocumentDB 리소스에 액세스하려면 여전히 호출자가 유효한 권한 부여 토큰을 제공해야 합니다.

## <a name="ip-access-control-overview"></a>IP 액세스 제어 개요
기본적으로 요청에 유효한 권한 부여 토큰이 포함되어 있으면 공용 인터넷에서 DocumentDB 데이터베이스 계정에 액세스할 수 있습니다. IP 정책 기반 액세스 제어를 구성하려면 사용자가 지정된 데이터베이스 계정에 대해 허용된 클라이언트 IP 목록으로 포함할 IP 주소 또는 IP 주소 범위를 CIDR 형식으로 제공해야 합니다. 이 구성이 적용되면 이 주소 이외의 컴퓨터에서 보내는 모든 요청을 서버에서 차단합니다.  IP 기반 액세스 제어의 연결 처리 흐름은 다음 다이어그램에 설명되어 있습니다.

![IP 기반 액세스 제어의 연결 프로세스를 보여주는 다이어그램](./media/documentdb-firewall-support/documentdb-firewall-support-flow.png)

## <a name="connections-from-cloud-services"></a>클라우드 서비스에서 연결
Azure에서 클라우드 서비스는 DocumentDB를 사용하여 중간 계층 서비스 논리를 호스팅하는 매우 일반적인 방법입니다. 클라우드 서비스에서 DocumentDB 데이터베이스 계정에 액세스할 수 있게 하려면 [Azure 지원에 문의](#configure-ip-policy)하여 클라우드 서비스의 공용 IP 주소를 DocumentDB 데이터베이스 계정에 연결된 허용된 IP 주소 목록에 추가해야 합니다.  이렇게 하면 클라우드 서비스의 모든 역할 인스턴스가 DocumentDB 데이터베이스 계정에 액세스할 수 있습니다. 다음 스크린샷처럼 Azure Portal에서 클라우드 서비스의 IP 주소를 검색할 수 있습니다. 

![Azure Portal에 표시된 클라우드 서비스의 공용 IP 주소를 보여주는 스크린샷](./media/documentdb-firewall-support/documentdb-public-ip-addresses.png)

추가 역할 인스턴스를 추가하여 클라우드 서비스를 규모 확장할 때 이러한 새 인스턴스는 자동으로 DocumentDB 데이터베이스 계정에 대한 액세스 권한을 갖습니다. 동일한 클라우드 서비스에 포함되기 때문입니다.

## <a name="connections-from-virtual-machines"></a>가상 컴퓨터에서 연결
[가상 컴퓨터](https://azure.microsoft.com/services/virtual-machines/) 또는 [가상 컴퓨터 크기 집합](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) 역시 DocumentDB를 사용하여 중간 계층 서비스를 호스트하는 데 사용할 수 있습니다.  가상 컴퓨터에서 액세스할 수 있도록 DocumentDB 데이터베이스 계정을 구성하려면 [Azure 지원에 문의](#configure-ip-policy)하여 가상 컴퓨터 및/또는 가상 컴퓨터 크기 집합의 공용 IP 주소를 DocumentDB 데이터베이스 계정에 허용되는 IP 주소 중 하나로 구성해야 합니다. 다음 스크린샷처럼 Azure Portal에서 가상 컴퓨터의 IP 주소를 검색할 수 있습니다.

![Azure Portal에 표시된 가상 컴퓨터의 공용 IP 주소를 보여주는 스크린샷](./media/documentdb-firewall-support/documentdb-public-ip-addresses-dns.png)

그룹에 추가 가상 컴퓨터 인스턴스를 추가하면 DocumentDB 데이터베이스 계정에 대한 액세스 권한이 자동으로 부여됩니다.

## <a name="connections-from-the-internet"></a>인터넷에서 연결
인터넷에 있는 컴퓨터에서 DocumentDB 데이터베이스 계정에 액세스하려면 컴퓨터의 클라이언트 IP 주소 또는 IP 주소 범위를 DocumentDB 데이터베이스 계정에 허용되는 IP 주소 목록에 추가해야 합니다. 

## <a name="a-idconfigure-ip-policya-configuring-the-ip-access-control-policy"></a><a id="configure-ip-policy"></a> IP 액세스 제어 정책 구성
데이터베이스 계정에서 IP 액세스 제어 정책을 사용하도록 설정하려면 Azure Portal을 사용하여 [Azure 지원](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)을 통해 요청을 제출해야 합니다.

1. [도움말 + 지원](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 블레이드에서 **새 지원 요청**을 선택합니다.
2. **새 지원 요청 블레이드**에서 **기본**을 선택합니다.
3. **기본** 블레이드에서 다음을 선택합니다.
   * **문제 유형**: 할당량
   * **구독**: IP 액세스 제어 정책을 추가할 계정에 연결된 구독입니다.
   * **할당량 유형**: DocumentDB
   * **지원 계획**: 할당량 지원 - 포함됨.
4. **문제** 블레이드에서 다음을 수행합니다.
   * **심각도**: C - 최소 영향 선택
   * **세부 정보**: 상자에 다음 텍스트를 복사하고 계정 이름 및 IP 주소를 포함합니다. "DocumentDB 데이터베이스 계정에 대한 방화벽 지원을 사용하도록 설정하겠습니다. 데이터베이스 계정: *계정 이름 입력*. 허용 IP 주소/범위: *IP 주소/범위를 CIDR 형식으로 입력(예: 13.91.6.132, 13.91.6.1/24)*."
   * **Next**를 클릭합니다. 
5. **연락처 정보** 블레이드에서 연락처 세부 정보를 입력하고 **만들기**를 클릭합니다. 

요청이 수신되면 24시간 내에 IP 액세스 제어가 활성화됩니다. 요청이 완료되면 알림이 제공됩니다.

![도움말 + 지원 블레이드의 스크린샷](./media/documentdb-firewall-support/documentdb-firewall-support-request-access.png)

![문제 블레이드의 스크린샷](./media/documentdb-firewall-support/documentdb-firewall-support-request-access-ticket.png)

## <a name="troubleshooting-the-ip-access-control-policy"></a>IP 액세스 제어 정책 문제 해결
### <a name="portal-operations"></a>포털 작업
DocumentDB 데이터베이스 계정에 대해 IP 액세스 제어 정책을 사용하도록 설정하면 허용되는 IP 주소 범위 목록 이외의 컴퓨터에서 시도하는 모든 DocumentDB 데이터베이스 계정 액세스가 차단됩니다. 이 모델 때문에 액세스 제어의 무결성을 보장하기 위해 포털에서 데이터 평면 작업을 검색하는 기능도 차단됩니다. 

### <a name="sdk-rest-api"></a>SDK 및 Rest API
보안상의 이유로 허용 목록에 없는 컴퓨터에서 SDK 또는 REST API를 통해 액세스를 시도하면 추가 정보 없이 일반적인 404 찾을 수 없음 응답이 반환됩니다. DocumentDB 데이터베이스 계정에 대해 구성된 허용 IP 목록을 확인하여 DocumentDB 데이터베이스 계정에 올바른 정책 구성이 적용되고 있는지 확인하세요.

## <a name="next-steps"></a>다음 단계
네트워크 관련 성능 팁에 대한 정보는 [성능 팁](documentdb-performance-tips.md)을 참조하세요.




<!--HONumber=Nov16_HO3-->


