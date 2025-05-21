#
# NOTE: THIS DOCKERFILE IS GENERATED VIA "update.sh"
#
# PLEASE DO NOT EDIT IT DIRECTLY.
#
FROM hello2008/kylin-os:v10-2403


ENV NGINX_VERSION=1.28.0 \
    NJS_VERSION=0.8.10 \
    PKG_RELEASE=1.el8 \
    DYNPKG_RELEASE=1.el8

# 创建 nginx 用户/组
RUN yum install -y shadow-utils && \
    groupadd --system --gid 101 nginx && \
    useradd --system --gid nginx --no-create-home \
    --home-dir /nonexistent --comment "nginx user" \
    --shell /bin/false --uid 101 nginx && \
    yum clean all

# 安装基础依赖
RUN set -x && \
    yum install -y \
    ca-certificates \
    curl \
    gettext \
    && yum clean all


# 添加 RHEL 仓库配置
RUN echo $'[nginx-stable]\n\
name=nginx stable repo\n\
baseurl=https://nginx.org/packages/rhel/8/$basearch/\n\
gpgcheck=1\n\
enabled=1\n\
gpgkey=https://nginx.org/keys/nginx_signing.key\n\
module_hotfixes=true\n' > /etc/yum.repos.d/nginx.repo

# 安装 NGINX 及模块
RUN set -x && \
    yum install -y \
    nginx-${NGINX_VERSION} \
    nginx-module-xslt-${NGINX_VERSION} \
    nginx-module-image-filter-${NGINX_VERSION} \
    nginx-module-njs-${NGINX_VERSION}+${NJS_VERSION} \
    && yum clean all

# 日志和入口点配置
RUN ln -sf /dev/stdout /var/log/nginx/access.log \
    && ln -sf /dev/stderr /var/log/nginx/error.log \
    && mkdir /docker-entrypoint.d

COPY docker-entrypoint.sh /
COPY 10-listen-on-ipv6-by-default.sh /docker-entrypoint.d
COPY 15-local-resolvers.env.sh /docker-entrypoint.d
COPY 20-envsubst-on-templates.sh /docker-entrypoint.d
COPY 30-tune-worker-processes.sh /docker-entrypoint.d

ENTRYPOINT ["/docker-entrypoint.sh"]
EXPOSE 80
STOPSIGNAL SIGQUIT
CMD ["nginx", "-g", "daemon off;"]