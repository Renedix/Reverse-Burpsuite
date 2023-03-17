from nginx:stable-alpine

COPY dockerca.crt /usr/local/share/ca-certificates/
run /usr/sbin/update-ca-certificates

run apk add iptables
