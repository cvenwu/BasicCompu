FROM node:latest
LABEL description="Basic Computer knowledge images."
# 克隆仓库
WORKDIR /
RUN git clone https://github.com/cvenwu/BasicCompu.git
WORKDIR /BasicCompu/docs
RUN npm install -g docsify-cli@latest
EXPOSE 9002/tcp
ENTRYPOINT docsify serve . -p 9002
