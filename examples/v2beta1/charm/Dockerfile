FROM adityapb/charm-netlrts:latest
#ADD file:8efdcc3201e79c8a09fc9c1ade08492ea33f838047332a7c61988f6357339dee in / 
CMD ["bash"]
ARG port=2222
USER root
RUN apt update && apt install -y --no-install-recommends 			openssh-server 			openssh-client             libcap2-bin 		&& rm -rf /var/lib/apt/lists/* # buildkit
RUN mkdir -p /var/run/sshd # buildkit
RUN CAP_NET_BIND_SERVICE=+eip /usr/sbin/sshd # buildkit
RUN apt remove libcap2-bin -y # buildkit
RUN sed -i "s/[ #]\(.*StrictHostKeyChecking \).*/ \1no/g" /etc/ssh/ssh_config     && echo "    UserKnownHostsFile /dev/null" >> /etc/ssh/ssh_config     && sed -i "s/[ #]\(.*Port \).*/ \1$port/g" /etc/ssh/ssh_config     && sed -i "s/#\(StrictModes \).*/\1no/g" /etc/ssh/sshd_config     && sed -i "s/#\(Port \).*/\1$port/g" /etc/ssh/sshd_config # buildkit
#RUN useradd -m mpiuser # buildkit
WORKDIR /home/mpiuser
#COPY --chown=mpiuser sshd_config .sshd_config
#RUN |1 port=2222 /bin/sh -c echo "Port $port" >> /home/mpiuser/.sshd_config # buildkit
RUN apt update     && apt install -y --no-install-recommends openmpi-bin     && rm -rf /var/lib/apt/lists/* # buildkit
RUN apt update # buildkit
RUN apt install -y build-essential cmake zlib1g-dev git # buildkit
#RUN mkdir /app/ # buildkit
USER mpiuser
RUN git clone https://github.com/charmplusplus/charm.git # buildkit
RUN cd charm/ && git checkout shrinkexpand-shmem # buildkit
RUN cd charm/ && ./build charm++ netlrts-linux-x86_64 --enable-shrinkexpand -j8 --force # buildkit
RUN cd charm/examples/charm++/shrink_expand && make # buildkit
RUN cd charm/examples/charm++/shrink_expand/jacobi2d-iter && make # buildkit
USER root
RUN cp charm/examples/charm++/shrink_expand/jacobi2d-iter/jacobi2d /app/ # buildkit
RUN cp charm/examples/charm++/shrink_expand/jacobi2d-iter/charmrun /app/ # buildkit
RUN chmod -R 777 /app/ # buildkit
WORKDIR /app
