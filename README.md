# System-Programming-Project
SHA-1 기반 캐시 구조를 바탕으로, 실제 브라우저 HTTP 요청을 처리하며 캐싱·로깅·병렬 처리·시그널·세마포어·스레드 기반 동시성 제어까지 단계적으로 확장한 시스템 프로그래밍 프로젝트

## Project Overview
이 프로젝트는 다음 기능을 단계적으로 구현하며 완성된 구조로 발전했습니다:
- SHA-1 해싱 기반 캐시 구조 설계
- 브라우저 HTTP 요청 분석 및 처리
- HIT/MISS 캐싱 및 응답 반환
- 다중 프로세스 기반 요청 처리 (fork)
- SIGALRM 타임아웃 처리로 비정상 지연 해결
- 세마포어를 이용한 로그 파일 동기화
- 스레드 기반 비동기 로깅 구조 구현

## Project Structure
proxy-server
 - Proxy1-1/     # SHA-1 기반 캐시 구조
 - Proxy1-2/     # HIT/MISS + 로깅
 - Proxy1-3/     # 다중 프로세스 구조
 - Proxy2-1/     # Client–Server 구조
 - Proxy2-2/     # HTTP 요청 파싱 및 자동 요청 필터링
 - Proxy2-3/     # SIGALRM 타임아웃 처리
 - Proxy2-4/     # 실제 HTTP 응답 저장 캐싱
 - Proxy3-1/     # 세마포어 기반 동기화
 - Proxy3-2/     # 스레드 기반 비동기 로깅

## Key Features
🔹 1. 캐시 구조 (Proxy 1-1 ~ 1-3)
    - URL → SHA-1 해시값으로 변환
    - cache/<3자리>/<나머지 해시> 구조로 저장
    - HIT/MISS 판별 및 URL·해시·시간 로그 기록
🔹 2. 브라우저 HTTP 요청 처리 (Proxy 2-1 ~ 2-4)
    - 브라우저의 모든 요청을 Proxy가 수신
    - Host·URL 파싱 후 캐시 검사
    - MISS 시 원 서버로 HTTP GET 전송
    - HTML·CSS·JS 등 실제 웹 응답을 캐싱 후 반환
    - 자동 요청(favicon, css 등) 필터링
🔹 3. 안정성 강화를 위한 시그널 처리 (Proxy 2-3)
    - 응답 지연(sleep 13) 실험
    - SIGALRM 기반 10초 타임아웃 처리
    - 비정상 프로세스 종료 방지
🔹 4. 세마포어 기반 동기화 (Proxy 3-1)
    - 여러 프로세스의 logfile 동시 접근 문제 해결
    - Race Condition을 sleep()으로 의도적으로 재현
    - PID별 waiting → critical zone → exit 상태 출력
🔹 5. 스레드 기반 비동기 로깅 (Proxy 3-2)
    - 자식 프로세스는 요청 처리
    - 스레드는 로그 기록 담당
    - PID + TID 기반 상세 동시성 기록
    - 로그 안정성 및 성능 향상

## Technical Stack
- Language: C (Linux 환경)
- Network: TCP Socket, HTTP GET
- OS Concepts: Process, Thread, Semaphore, Signal
- Hashing: OpenSSL SHA-1
- Environment: Ubuntu 20.04, gcc, make
