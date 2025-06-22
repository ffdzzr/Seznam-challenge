FROM golang:1.16
WORKDIR /go-server
COPY go-simple-server ./

RUN adduser --system --group --no-create-home goserver
RUN chown -R goserver:goserver ./
USER goserver

EXPOSE 8080
ENTRYPOINT ["./go-simple-server"]
