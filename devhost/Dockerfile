FROM leeyanzhe/arm-cross:base

RUN apt -y update \
    && apt -y install qemu

COPY --from=leeyanzhe/arm-cross:opencv /opt/opencv /opt/cross-host-root

RUN mkdir -m 700 .ssh \
    && echo "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCybcYHKuSDNMSQawcTrplJ/1+18DRkqybnknCybsaOQ/lE2ZRqbYziQXF6tZUWaFaS+7yqSmbWXUFCBtlU0mi3LOXW1q66vaKcGDf8gkL9gWbW82pX96t32mz/k5PQkWUPXAef/6BURkU+jmUSPjt67nx2EQNyTMx9i3PVQ+XNbPbpyXdhi2IZ7IOntjUXingdiQjhGWdQsaww3xvFFB/kRMpZfanLxg41DaflBA1MVt6khuPGPEiDPGj0c+Bygd12gpuxQnUQ8zdGogk5EgDufXwpDFJpPwCFNls76Df/wfzASW2APbFlYmB8bNNlN3fEi1FT1jU4ATlpBymYBgLD6oZMTAaFK9ECgUKh8UA5TPkn3CdWd4ncNlrSRm3ry9gAo4mPEyAavzDqpWbGUGiE2sOz7xDOsomqkOFyVvHZ5N50Y3QQ6aQ/Vp6+DezYVBGTzP9fX2jCciZOM5j0+41bskkgiOemXwLGgozD14j0IwCZk8hfgPd4oFSjhJiZpqvarxbg9+rmsBobVbY25UuUo6c/EWyJcDw9qG0lElUAXnFuniqhYsnICkMD//9I3vDdoC4ybyS1olRauxkZn/PkAaNa0Mgz+JStLMPdkynV+RP7sIqzj1SqwHA5NX20OLh9YVE609Oi3oYcsy5HXhaLiBMZ+YlZvo2nLB4iWenJaw== openssl:rsa4096" > .ssh/authorized_keys
