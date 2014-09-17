FROM google/golang

RUN apt-get update && apt-get install -y curl

ADD test.sh /test.sh

CMD /test.sh http://$SUT_PORT_80_TCP_ADDR:$SUT_PORT_80_TCP_PORT