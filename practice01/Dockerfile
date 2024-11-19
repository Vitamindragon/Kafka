FROM ubuntu:22.04
ARG DEBIAN_FRONTEND=noninteractive

# 서울 시간대 설정
ENV TZ=Asia/Seoul

# 우분투의 아카이브 미러를 카카오 서버로 변경하여 속도 향상
RUN sed -i 's/kr.archive.ubuntu.com/mirror.kakao.com/g' /etc/apt/sources.list

# 패키지 업데이트 및 필수 패키지 설치
RUN apt-get update
RUN apt-get upgrade
RUN apt-get install -y wget  # wget 패키지 설치 (다운로드 용)
RUN apt-get install -y build-essential # 빌드 필수 패키지 설치
RUN apt-get install -y tzdata # 타임존 데이터 설치 
RUN apt-get install -y vim curl # vim (편집기) 및 curl (네트워크 요청) 설치
RUN apt-get install -y openjdk-11-jdk # OpenJDK 11 설치
RUN apt-get clean autoclean # 패키지 캐시 정리
RUN apt-get autoremove -y # 불필요한 패키지 자동 제거
RUN rm -rf /var/lib/{apt,dpkg,cache,log}  # 패키지 캐시 및 로그 파일 삭제하여 이미지 크기 감소


# VM 설정: 시스템 메모리 맵 카운트 및 파일 디스크립터 제한 조정
RUN echo 'vm.max_map_count=100000' | tee -a /etc/sysctl.conf
RUN echo 'kafka soft nofile 100000'| tee -a /etc/security/limits.d/kafka.conf

# Kafka 사용자 계정 생성 및 비밀번호 설정
RUN useradd kafka -m --shell /bin/bash \
    && echo 'kafka:1234' | chpasswd  # kafka 사용자 비밀번호 설정

# Kafka 및 Zookeeper 다운로드
RUN wget https://dlcdn.apache.org/kafka/3.6.2/kafka_2.13-3.6.2.tgz -P /tmp \
    && wget https://downloads.apache.org/zookeeper/zookeeper-3.8.4/apache-zookeeper-3.8.4-bin.tar.gz -P /tmp  # Zookeeper 다운로드

# 필요한 디렉토리 생성
RUN mkdir -p /app /data/zookeeper /logs/zookeeper \
    && mkdir -p /app /data/kafka /logs/kafka \
    && mkdir -p /data/zookeeper-in-kafka /logs/zookeeper-in-kafka \
    && mkdir -p /data/kraft /logs/kraft  # Kafka KRaft 모드 관련 디렉토리 생성

# 다운로드한 압축 파일 해제
RUN tar -xvzf /tmp/apache-zookeeper-3.8.4-bin.tar.gz -C /app/ \
    && tar -xvzf /tmp/kafka_2.13-3.6.2.tgz -C /app/  # Kafka 압축 해제

# 심볼릭 링크 설정: Zookeeper 및 Kafka를 /app/zookeeper 및 /app/kafka로 연결
RUN ln -s /app/apache-zookeeper-3.8.4-bin /app/zookeeper \
    && ln -s /app/kafka_2.13-3.6.2 /app/kafka

# 생성된 디렉토리 및 파일의 소유권을 kafka 사용자에게 부여
RUN chown -R kafka:kafka /app/*zookeeper* /data/*zookeeper* /logs/*zookeeper* \
    && chown -R kafka:kafka /app/kafka* /data/kafka* /logs/kafka*  # Kafka 파일 권한 설정


# Zookeeper.properties
COPY ./zookeeper.properties /app/kafka/config/zookeeper.properties

# Kafka.properties
COPY server.properties /app/kafka/config/server.properties

# 기본 실행 명령어
CMD ["/sbin/init"]
