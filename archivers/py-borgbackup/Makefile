# Created by: José García Juanino <jjuanino@gmail.com>
# $FreeBSD$

PORTNAME=	borgbackup
DISTVERSION=	1.1.2
CATEGORIES=	archivers python
MASTER_SITES=	CHEESESHOP
PKGNAMEPREFIX=	${PYTHON_PKGNAMEPREFIX}

MAINTAINER=	jjuanino@gmail.com
COMMENT=	Deduplicating backup program

LICENSE=	BSD3CLAUSE
LICENSE_FILE=	${WRKSRC}/LICENSE

BUILD_DEPENDS=	${PYTHON_PKGNAMEPREFIX}setuptools_scm>=1.7:devel/py-setuptools_scm
LIB_DEPENDS=	liblz4.so:archivers/liblz4
RUN_DEPENDS=	${PYTHON_PKGNAMEPREFIX}msgpack-python>=0.4.6:devel/py-msgpack-python

OPTIONS_DEFINE=	FUSE
FUSE_DESC=	Support to mount locally borg backup files
OPTIONS_DEFAULT=

FUSE_RUN_DEPENDS=	${PYTHONPREFIX_SITELIBDIR}/llfuse.so:devel/py-llfuse

USES=		python:3.4+ ssl
USE_PYTHON=	autoplist distutils

.include <bsd.port.pre.mk>

.if defined(PACKAGE_BUILDING) && !defined(PACKAGE_BUILDING_FLAVORS) && \
    ${PYTHON_VER} != ${PYTHON_DEFAULT}
IGNORE=	you have python ${PYTHON_DEFAULT} set as the default, and this needs ${PYTHON_VER}
.endif

post-install:
	@${FIND} ${STAGEDIR}${PYTHONPREFIX_SITELIBDIR}/borg/ -name "*.so" \
		-exec ${STRIP_CMD} {} \;

.include <bsd.port.post.mk>
