---
sidebar_position: 4
---

# DNS

인터넷을 사용할 때 우리는 `www.google.com`과 같은 도메인 이름을 입력하지만, 실제로 컴퓨터들은 IP 주소로만 통신한다. DNS는 이러한 도메인 이름을 IP 주소로 변환해주는 핵심적인 인터넷 서비스다.

## DNS란 무엇인가?

### DNS의 기본 개념

DNS(Domain Name System)는 **인터넷의 전화번호부**라고 할 수 있다. 사람이 기억하기 쉬운 도메인 이름(예: google.com)을 컴퓨터가 이해할 수 있는 IP 주소(예: 142.250.191.14)로 변환하는 분산된 네이밍 시스템이다.

**DNS가 필요한 이유:**

1. **인간 친화적**: `192.168.1.1`보다 `google.com`이 기억하기 쉽다
2. **유연성**: IP 주소가 변경되어도 도메인 이름은 유지할 수 있다
3. **확장성**: 전 세계 수십억 개의 도메인을 효율적으로 관리할 수 있다

### DNS의 계층 구조

DNS는 **트리 구조**로 설계되어 있으며, 각 레벨마다 다른 기관이 관리한다:

```
                    . (루트)
                   /|\
                  / | \
                .com .org .kr
               /     |     \
           google  naver   co
          /    |      |      \
        www   mail   www    example
```

**계층별 설명:**

1. **루트 도메인** (`.`): 최상위 레벨, 전 세계 13개 루트 서버가 관리
2. **최상위 도메인** (TLD): `.com`, `.org`, `.kr` 등
3. **2차 도메인**: `google`, `naver` 등 (기업이나 조직이 등록)
4. **서브도메인**: `www`, `mail` 등

<br></br>

## DNS 조회 과정

사용자가 브라우저에 `www.google.com`을 입력했을 때 일어나는 DNS 조회 과정을 단계별로 살펴보자.

### 1단계: 계층적 캐시 확인

DNS 조회 시 **순차적으로** 각 레벨의 캐시를 확인한다. 어떤 단계에서든 정보를 찾으면 조회를 즉시 종료한다:

```javascript
// DNS 캐시 확인 순서 (순차적)
1. 브라우저 내부 DNS 캐시 확인
   ↓ (정보 없음)
2. 운영체제 DNS 캐시 확인
   ↓ (정보 없음)
3. 라우터 DNS 캐시 확인
   ↓ (정보 없음)
4. ISP DNS 캐시 확인
   ↓ (정보 없음)
5. 재귀적 DNS 조회 시작 ← 여기서 2단계로 진행
```

**중요한 점:**

- 1-4단계는 **로컬 캐시 확인**이며, 정보를 찾으면 즉시 반환
- **모든 캐시에서 정보를 찾지 못했을 때만** 재귀적 DNS 조회를 시작

**캐시 확인 명령어:**

```bash
# Windows에서 DNS 캐시 확인
ipconfig /displaydns

# DNS 캐시 삭제
ipconfig /flushdns

# macOS/Linux에서 DNS 캐시 삭제
sudo dscacheutil -flushcache  # macOS
sudo systemctl restart systemd-resolved  # Linux
```

### 2단계: 재귀적 DNS 조회 시작

**모든 로컬 캐시에서 정보를 찾지 못한 경우에만** 재귀적 DNS 서버(보통 ISP의 DNS 서버)에 요청한다:

```
클라이언트 → 재귀적 DNS 서버 (예: 168.126.63.1)
             ↓
"www.google.com의 IP 주소를 알려주세요"
```

**재귀적 DNS 서버의 역할:**

- 클라이언트를 대신해서 전체 DNS 조회 과정을 수행
- 루트 → TLD → 권한있는 서버 순으로 질의
- 최종 결과를 클라이언트에게 반환

### 3단계: 루트 DNS 서버 조회

재귀적 DNS 서버가 루트 DNS 서버에 질의한다:

```
재귀적 DNS 서버 → 루트 DNS 서버 (예: 198.41.0.4)
                  ↓
"www.google.com을 담당하는 .com DNS 서버 주소는 192.5.6.30입니다"
```

### 4단계: TLD DNS 서버 조회

`.com` 도메인을 관리하는 TLD 서버에 질의한다:

```
재귀적 DNS 서버 → .com TLD 서버 (192.5.6.30)
                  ↓
"google.com을 담당하는 네임서버는 ns1.google.com입니다"
```

### 5단계: 권한있는 DNS 서버 조회

Google의 권한있는 DNS 서버에 최종 질의한다:

```
재귀적 DNS 서버 → ns1.google.com (216.239.32.10)
                  ↓
"www.google.com의 IP 주소는 142.250.191.14입니다"
```

### 6단계: 응답 반환 및 캐싱

```
ns1.google.com → 재귀적 DNS 서버 → 클라이언트
                                   ↓
"www.google.com = 142.250.191.14" (TTL: 300초)
```

각 단계에서 얻은 정보는 TTL(Time To Live) 값에 따라 캐싱된다.

<br></br>

## DNS 레코드 타입

DNS는 다양한 종류의 정보를 저장하기 위해 여러 레코드 타입을 사용한다.

### 주요 레코드 타입

#### A 레코드 (Address Record)

도메인 이름을 IPv4 주소로 매핑한다:

```
www.google.com.    IN    A    142.250.191.14
```

#### CNAME 레코드 (Canonical Name Record)

도메인의 별칭을 정의한다:

```
www.example.com.    IN    CNAME    example.com.
```

#### MX 레코드 (Mail Exchange Record)

이메일 서버 정보를 정의한다:

```
example.com.    IN    MX    10    mail.example.com.
```

#### NS 레코드 (Name Server Record)

도메인의 권한있는 네임서버를 지정한다:

```
google.com.    IN    NS    ns1.google.com.
```

### DNS 조회 명령어

**기본 조회:**

```bash
# Windows
nslookup google.com

# Linux/macOS
dig google.com

# PowerShell
Resolve-DnsName google.com
```

<br></br>

## 웹 개발에서의 DNS

### DNS 최적화 방법

#### 1. DNS 프리페칭

브라우저가 미리 DNS 조회를 수행하도록 힌트 제공:

```html
<link rel="dns-prefetch" href="//fonts.googleapis.com" /> <link rel="dns-prefetch" href="//cdn.jsdelivr.net" />
```

#### 2. 적절한 TTL 설정

```javascript
// 정적 콘텐츠: 긴 TTL (24시간)
static.example.com.  86400  IN  A  192.168.1.100

// 동적 콘텐츠: 짧은 TTL (5분)
api.example.com.     300    IN  A  192.168.1.100
```

### DNS 보안

**주요 보안 위협:**

- **DNS 스푸핑**: 악의적인 DNS 응답으로 잘못된 사이트 접속 유도
- **DNS 하이재킹**: DNS 서버 조작으로 쿼리 가로채기

**보안 강화 방법:**

- **DNS over HTTPS (DoH)**: DNS 질의를 HTTPS로 암호화
- **DNS over TLS (DoT)**: DNS 질의를 TLS로 암호화
- **DNSSEC**: DNS 응답 무결성과 인증 보장

<br></br>

## 자주 묻는 질문 (FAQ)

### Q: DNS 조회가 실패하면 어떻게 되나요?

**DNS 조회 실패 시 브라우저의 대응 방식:**

1. **다른 DNS 서버 시도**: 보조 DNS 서버로 재시도
2. **Stale DNS 캐시 사용**: TTL이 만료되었지만 아직 메모리에 남아있는 DNS 정보를 임시로 사용
3. **오류 페이지 표시**: 모든 시도가 실패하면 "사이트에 연결할 수 없음" 오류

**중요한 점:**

- **DNS 캐시는 TTL 만료 즉시 삭제되지 않습니다**
- 대부분의 DNS 구현체는 **Stale-while-revalidate** 방식을 사용
- 만료된 캐시는 메모리에 일정 기간 더 보관되어 비상시 사용됩니다

**Stale DNS 캐시 동작 방식:**

```javascript
// DNS 캐시의 실제 동작 방식
class DNSCache {
  constructor() {
    this.cache = new Map();
  }

  set(domain, ip, ttl) {
    this.cache.set(domain, {
      ip,
      timestamp: Date.now(),
      ttl: ttl * 1000, // TTL을 밀리초로 변환
      staleTtl: ttl * 1000 * 2 // Stale 기간 (구현체마다 다름)
    });
  }

  get(domain) {
    const entry = this.cache.get(domain);
    if (!entry) return null;

    const now = Date.now();
    const age = now - entry.timestamp;

    if (age < entry.ttl) {
      // 유효한 캐시
      return { ip: entry.ip, fresh: true };
    } else if (age < entry.staleTtl) {
      // 만료되었지만 Stale 기간 내 - 비상시 사용 가능
      return { ip: entry.ip, fresh: false, stale: true };
    } else {
      // 완전히 만료 - 삭제
      this.cache.delete(domain);
      return null;
    }
  }

  async resolve(domain) {
    const cached = this.get(domain);

    if (cached && cached.fresh) {
      // 신선한 캐시 반환
      return cached.ip;
    }

    try {
      // 새로운 DNS 조회 시도
      const freshIP = await this.performDNSLookup(domain);
      this.set(domain, freshIP, 300); // 5분 TTL
      return freshIP;
    } catch (error) {
      // DNS 조회 실패시 Stale 캐시 사용
      if (cached && cached.stale) {
        console.warn(`Using stale DNS cache for ${domain}: ${cached.ip}`);
        return cached.ip;
      }

      throw new Error(`DNS resolution failed for ${domain}`);
    }
  }
}

// DNS 캐시가 저장되는 구체적인 메모리 위치
const dnsStorageLocations = {
  level1_browser: {
    process: 'Chrome의 Browser Process (메인 프로세스) - 모든 탭이 공유',
    memory_type: 'RAM (Browser Process의 힙 메모리)',
    location: 'Browser Process의 가상 메모리 공간',
    lifetime: 'TTL + Stale 기간',
    실제_위치: '컴퓨터 RAM → Browser Process → DNS 캐시 객체 (모든 탭 공유)',
    확인방법: 'Chrome: chrome://net-internals/#dns',
    중요사실: '여러 탭을 열어도 DNS 캐시는 Browser Process에서 공유됨'
  },

  level2_os: {
    process: 'Windows: svchost.exe (DNS Client), macOS: mDNSResponder, Linux: systemd-resolved',
    memory_type: 'RAM (운영체제 시스템 프로세스의 메모리)',
    location: '운영체제 DNS 리졸버 프로세스의 메모리 공간',
    lifetime: '시스템 설정에 따라 다름',
    실제_위치: '컴퓨터 RAM → OS DNS 서비스 프로세스 → DNS 캐시',
    확인방법: 'Windows: ipconfig /displaydns'
  },

  level3_router: {
    process: '라우터 펌웨어의 DNS 프록시 프로세스',
    memory_type: '라우터 하드웨어의 RAM',
    location: '라우터 장비 내부 메모리',
    lifetime: '라우터 재부팅까지 또는 캐시 만료',
    실제_위치: '라우터 RAM → DNS 캐시 테이블',
    확인방법: '라우터 관리 페이지에서 확인 (제품마다 다름)'
  }
};

// Chrome 프로세스 모델과 DNS 캐시 저장 위치
/*
Chrome의 프로세스 구조:
├── Browser Process (메인 프로세스) ← DNS 캐시가 여기에 저장됨!
│   ├── UI 관리
│   ├── 네트워크 처리 (DNS 포함)
│   ├── 파일 시스템 접근
│   └── 다른 프로세스들 관리
├── Renderer Process #1 (탭 1)
├── Renderer Process #2 (탭 2)
├── Renderer Process #3 (탭 3)
└── GPU Process, Plugin Process 등...

핵심 포인트:
- DNS 캐시는 Browser Process에만 저장됨
- 모든 탭(Renderer Process)이 동일한 DNS 캐시를 공유
- 탭 1에서 google.com을 방문해 DNS 캐시되면, 탭 2에서도 즉시 사용 가능
*/

// 실제 확인 방법
const chromeProcessCheck = {
  작업관리자: 'Chrome 프로세스 여러 개 보임 (각기 다른 PID)',
  하지만: 'DNS 캐시는 Browser Process 하나에만 저장',
  증명방법: [
    '1. 탭 1에서 새로운 사이트 방문',
    '2. chrome://net-internals/#dns에서 DNS 캐시 확인',
    '3. 탭 2 열어서 같은 사이트 방문 → 즉시 로딩 (DNS 캐시 재사용)',
    '4. 모든 탭에서 동일한 DNS 캐시 공유됨을 확인'
  ]
};

// Stale DNS 캐시 보관 기간의 실제 값들
const staleCacheDuration = {
  chrome: {
    기본값: 'TTL의 25% 추가 (최소 1분, 최대 10분)',
    예시: 'TTL 300초 → Stale 75초 추가 → 총 375초',
    설정불가: '사용자가 직접 변경할 수 없음',
    확인방법: 'chrome://net-internals/#dns에서 확인'
  },

  firefox: {
    기본값: '최대 60초 추가',
    설정방법: 'about:config → network.dnsCacheExpiration',
    예시: 'TTL 300초 + 최대 60초 = 최대 360초',
    기본설정: 'network.dnsCacheExpiration = 60'
  },

  windows_os: {
    기본값: '최대 86400초 (24시간) 또는 TTL의 100%',
    설정방법: '레지스트리 수정',
    레지스트리경로: 'HKEY_LOCAL_MACHINE\\SYSTEM\\CurrentControlSet\\Services\\Dnscache\\Parameters',
    주요설정: {
      MaxCacheTtl: '최대 캐시 시간 (기본: 86400초)',
      NegativeCacheTime: '실패한 조회 캐시 시간 (기본: 300초)'
    }
  },

  macos: {
    기본값: 'mDNSResponder가 내부적으로 관리 (정확한 값 비공개)',
    추정값: 'TTL + 30-60초 정도',
    설정방법: '시스템 레벨에서 설정 불가 (Apple 내부 로직)',
    확인방법: 'sudo log show --predicate "process == \\"mDNSResponder\\"" --last 1h'
  },

  linux: {
    systemd_resolved: {
      기본값: '최대 30초 추가',
      설정파일: '/etc/systemd/resolved.conf',
      설정옵션: 'CacheFromLocalhost=yes/no'
    },
    bind: {
      기본값: 'TTL + max-cache-ttl 설정값',
      설정파일: '/etc/bind/named.conf',
      예시: 'max-cache-ttl 3600; // 최대 1시간'
    }
  }
};

// 실제 테스트로 확인하는 방법
const testStaleDNS = {
  단계1: 'dig example.com +trace로 TTL 확인',
  단계2: 'TTL 만료까지 대기',
  단계3: 'DNS 서버를 일시적으로 차단 (방화벽 등)',
  단계4: 'dig example.com 재실행',
  결과: 'Stale 캐시 사용 여부와 추가 보관 시간 확인 가능'
};
*/
```

### Q: www가 있는 도메인과 없는 도메인의 차이는?

**핵심 차이점:**

`www.example.com`과 `example.com`은 **DNS 관점에서 완전히 다른 도메인**입니다.

```javascript
// DNS는 이 둘을 다른 도메인으로 인식
example.com     ← 루트 도메인 (Apex 도메인)
www.example.com ← 서브도메인 (www는 그냥 이름일 뿐)

// 마치 이것과 같음
mail.google.com ← 서브도메인
drive.google.com ← 서브도메인
```

**실제로는 어떻게 동작하나요?**

대부분의 웹사이트는 두 도메인을 **같은 곳으로 연결**되도록 설정합니다:

```javascript
// 일반적인 설정
example.com     → 192.168.1.100 (실제 서버)
www.example.com → 192.168.1.100 (같은 서버로 연결)

// 하지만 기술적으로는 다르게 설정 가능
example.com     → 192.168.1.100 (메인 서버)
www.example.com → 192.168.1.200 (다른 서버)
```

**왜 중요한가요?**

1. **SEO 문제**: 검색엔진이 중복 콘텐츠로 인식할 수 있음
2. **사용자 혼란**: 한쪽만 접속되면 사용자가 혼란스러워함
3. **쿠키 범위**: 쿠키 공유 범위가 달라짐

**쿠키의 차이:**

```javascript
// 쿠키 설정에 따른 접근 범위 차이
document.cookie = 'user=john; domain=.example.com';
// → example.com, www.example.com, api.example.com 모두 접근 가능

document.cookie = 'user=john; domain=www.example.com';
// → www.example.com에서만 접근 가능
```

**결론:**

- **기술적으로**: 완전히 다른 도메인
- **실제 사용**: 대부분 같은 곳으로 연결
- **권장사항**: 하나로 통일하고 다른 쪽은 리디렉션 설정

### Q: DNS 캐시는 언제 갱신되나요?

**DNS 캐시 갱신 조건:**

1. **TTL 만료**: 레코드에 설정된 시간이 지나면 자동 갱신
2. **수동 삭제**: 사용자가 캐시를 강제로 삭제
3. **프로세스 종료**: 브라우저나 시스템 프로세스 종료 시 해당 캐시 초기화

**중요한 구분:**

- **Chrome 완전 종료** → Chrome DNS 캐시만 삭제 (OS DNS 캐시는 유지)
- **운영체제 재부팅** → 모든 레벨의 DNS 캐시 삭제

**Chrome 종료/재시작 시 DNS 캐시 동작:**

```javascript
// Chrome 프로세스와 DNS 캐시 관계
const chromeProcessLifecycle = {
  시나리오1_탭만_닫기: {
    동작: '탭 닫기 (Ctrl+W)',
    결과: 'DNS 캐시 유지됨 (Browser Process 살아있음)',
    이유: 'Renderer Process만 종료, Browser Process는 계속 실행',
  },

  시나리오2_창_닫기: {
    동작: '창 닫기 (Alt+F4), 하지만 다른 Chrome 창이 열려있음',
    결과: 'DNS 캐시 유지됨 (Browser Process 살아있음)',
    이유: '다른 창이 있어서 Browser Process 계속 실행',
  },

  시나리오3_완전_종료: {
    동작: '모든 Chrome 창 닫기 또는 작업관리자에서 chrome.exe 종료',
    결과: 'Chrome DNS 캐시 완전 삭제',
    이유: 'Browser Process 종료 → 프로세스 메모리 해제',
  },

  시나리오4_재시작: {
    동작: 'Chrome 완전 종료 후 다시 실행',
    결과: '빈 DNS 캐시로 시작 (하지만 OS 캐시는 유지)',
    순서: ['1. Chrome DNS 캐시: 비어있음', '2. OS DNS 캐시: 기존 캐시 유지', '3. 첫 DNS 조회 시 OS 캐시부터 확인'],
  },
};

// 실제 테스트 방법
const testChromeRestart = {
  단계1: 'Chrome에서 chrome://net-internals/#dns 접속',
  단계2: '새로운 사이트 방문하여 DNS 캐시 생성',
  단계3: 'DNS 캐시에 해당 도메인 확인',
  단계4: 'Chrome 완전 종료 (모든 창 닫기)',
  단계5: 'Chrome 재시작',
  단계6: 'chrome://net-internals/#dns 다시 접속',
  결과: 'DNS 캐시가 비어있음을 확인',
};

// 구체적인 시나리오: www.google.com 방문 후 Chrome 종료
const specificScenario = {
  상황: 'Chrome에서 www.google.com 방문 후 Chrome 완전 종료',
  
  방문_시_저장된_DNS_캐시: {
    Chrome_캐시: [
      'www.google.com → 142.250.191.14',
      'fonts.gstatic.com → 142.251.12.95',      // Google 폰트
      'ssl.gstatic.com → 142.251.1.110',        // 정적 리소스
      'www.gstatic.com → 142.251.1.95'          // 기타 리소스
    ],
    OS_캐시: [
      'www.google.com → 142.250.191.14',        // Chrome이 조회한 것
      'fonts.gstatic.com → 142.251.12.95',
      'ssl.gstatic.com → 142.251.1.110',
      'www.gstatic.com → 142.251.1.95',
      '기타_시스템_도메인들...'                    // 다른 프로그램들이 조회한 것
    ]
  },
  
  Chrome_종료_후: {
    Chrome_캐시: '완전 삭제됨 (프로세스 메모리 해제)',
    OS_캐시: [
      'www.google.com → 142.250.191.14',        // 그대로 유지
      'fonts.gstatic.com → 142.251.12.95',     // 그대로 유지
      'ssl.gstatic.com → 142.251.1.110',       // 그대로 유지
      'www.gstatic.com → 142.251.1.95',        // 그대로 유지
      '기타_시스템_도메인들...'                    // 그대로 유지
    ]
  },
  
  Chrome_재시작_후_google_com_재방문: {
    과정: [
      '1. Chrome DNS 캐시 확인 → 비어있음',
      '2. OS DNS 캐시 확인 → www.google.com 발견!',
      '3. OS 캐시에서 IP 주소 가져옴 → 즉시 연결',
      '4. Chrome 캐시에도 다시 저장'
    ],
    결과: 'DNS 조회 없이 빠른 접속 (OS 캐시 덕분)'
  }
};
```

### Q: HTTPS 사이트에서 DNS 보안이 중요한 이유는?

**HTTPS와 DNS의 관계:**

```javascript
// HTTPS 연결 과정에서 DNS의 역할
1. DNS 조회: example.com → 192.168.1.100
2. TCP 연결: 192.168.1.100:443으로 연결
3. TLS 핸드셰이크: 인증서 검증 (example.com 도메인 확인)
4. 암호화된 HTTP 통신 시작

// DNS 스푸핑 공격 시나리오
1. DNS 조회: example.com → 192.168.1.200 (공격자 서버)
2. TCP 연결: 192.168.1.200:443으로 연결
3. TLS 핸드셰이크: 인증서 불일치로 경고 (하지만 사용자가 무시할 수 있음)
4. 공격자 서버와 통신 (피싱 사이트)
```

**DNS 보안 강화 방법:**

```javascript
// HSTS (HTTP Strict Transport Security) 활용
// DNS 공격을 받아도 HTTPS 강제로 보안 강화
response.setHeader('Strict-Transport-Security', 'max-age=31536000; includeSubDomains');

// CAA 레코드로 인증서 발급 제한
example.com.  IN  CAA  0 issue "letsencrypt.org"
example.com.  IN  CAA  0 iodef "mailto:security@example.com"
```

### Q: DNS는 어떻게 확장성을 보장하나요?

**DNS의 확장성 특징:**

1. **분산 구조**: 전 세계에 분산된 DNS 서버가 협력하여 동작
2. **계층적 네임스페이스**: 트리 구조로 관리 부담을 분산
3. **캐싱 시스템**: 중복 조회를 줄여 전체 시스템 부하 감소

```javascript
// DNS 계층별 관리 주체
const dnsHierarchy = {
  root: '전 세계 13개 루트 서버 (ICANN 관리)',
  tld: {
    '.com': 'Verisign',
    '.org': 'Public Interest Registry',
    '.kr': 'NIDA (한국인터넷진흥원)',
  },
  domain: '개별 도메인 소유자가 관리',
  subdomain: '도메인 소유자가 자유롭게 생성/관리',
};

// 전 세계 DNS 쿼리 분산 처리
// 하루 약 4조 건의 DNS 쿼리를 처리
```

### Q: DNS 라운드 로빈과 로드 밸런싱은 어떻게 동작하나요?

**DNS 라운드 로빈:**

```javascript
// 동일한 도메인에 여러 IP 주소 설정
example.com.    IN    A    192.168.1.10
example.com.    IN    A    192.168.1.11
example.com.    IN    A    192.168.1.12

// DNS 서버는 순환적으로 다른 IP를 반환
// 첫 번째 요청: 192.168.1.10
// 두 번째 요청: 192.168.1.11
// 세 번째 요청: 192.168.1.12
// 네 번째 요청: 192.168.1.10 (반복)
```

**한계점과 개선 방법:**

```javascript
// 라운드 로빈의 한계
const limitations = {
  '서버 상태 미고려': '다운된 서버에도 트래픽 전송',
  '지리적 위치 무시': '가장 가까운 서버로 라우팅 불가',
  '서버 성능 차이 무시': '모든 서버를 동등하게 처리',
};

// 개선된 DNS 기반 로드 밸런싱
const advancedDNS = {
  GeoDNS: '사용자 위치 기반 서버 선택',
  'Health Check': '서버 상태 모니터링 후 라우팅',
  'Weighted Round Robin': '서버 성능에 따른 가중치 적용',
  'Latency-based Routing': '응답 시간이 가장 빠른 서버 선택',
};
```

### Q: DNS 플러싱(Flushing)은 언제 필요한가요?

**DNS 플러싱이 필요한 상황:**

1. **웹사이트에 접속되지 않을 때**: DNS 캐시 오류로 인한 접속 불가
2. **도메인 변경 후**: 새로운 IP 주소로 업데이트가 안 될 때
3. **보안 위협 의심**: DNS 하이재킹이나 스푸핑 의심 시
4. **네트워크 문제 해결**: 일반적인 네트워크 트러블슈팅 과정

```javascript
// 상황별 DNS 플러싱 방법

// 1. 개발 환경에서 자주 도메인이 변경될 때
function developmentDNSFlush() {
  // 개발용 DNS 캐시 무력화
  const timestamp = Date.now();
  const devUrl = `https://dev.example.com?_dns=${timestamp}`;
  return fetch(devUrl);
}

// 2. 사용자가 DNS 문제를 겪을 때 안내
const dnsFlushInstructions = {
  windows: [
    '1. 관리자 권한으로 명령 프롬프트 실행',
    '2. ipconfig /flushdns 입력',
    '3. "DNS 확인자 캐시를 성공적으로 플러시했습니다" 메시지 확인',
  ],
  mac: ['1. 터미널 실행', '2. sudo dscacheutil -flushcache 입력', '3. 관리자 비밀번호 입력'],
  browser: [
    '1. Chrome: chrome://net-internals/#dns → Clear host cache',
    '2. Firefox: about:networking#dns → Clear DNS Cache',
    '3. Safari: 개발자 메뉴 → 캐시 비우기',
  ],
};

// 3. 프로그래매틱 DNS 캐시 체크
async function checkDNSCache(domain) {
  const cacheHit = localStorage.getItem(`dns_${domain}`);
  const lastCheck = localStorage.getItem(`dns_${domain}_timestamp`);

  if (cacheHit && Date.now() - lastCheck < 300000) {
    // 5분 캐시
    console.log(`DNS cache hit for ${domain}: ${cacheHit}`);
    return cacheHit;
  }

  // 캐시 미스 시 새로 조회
  const freshDNS = await performDNSLookup(domain);
  localStorage.setItem(`dns_${domain}`, freshDNS);
  localStorage.setItem(`dns_${domain}_timestamp`, Date.now());
  return freshDNS;
}
```
