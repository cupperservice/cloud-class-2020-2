FROM centos:7

RUN curl -sL https://rpm.nodesource.com/setup_12.x | bash -
RUN yum install -y nodejs
RUN curl -sL https://dl.yarnpkg.com/rpm/yarn.repo | tee /etc/yum.repos.d/yarn.repo
RUN yum install -y yarn
RUN yum install -y git
RUN mkdir -p /opt
RUN git clone https://github.com/weseek/growi.git /opt/growi
RUN cd /opt/growi && \
git checkout -b v3.8.2 refs/tags/v3.8.2 && \
yarn
