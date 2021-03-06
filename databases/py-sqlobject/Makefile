# Created by: "Choe, Cheng-Dae" whitekid
# $FreeBSD$

PORTNAME=	sqlobject
PORTVERSION=	1.6.0
CATEGORIES=	databases python
MASTER_SITES=	CHEESESHOP
PKGNAMEPREFIX=	${PYTHON_PKGNAMEPREFIX}
DISTNAME=	SQLObject-${PORTVERSION}

MAINTAINER=	wg@FreeBSD.org
COMMENT=	Python object for manipulation with SQL table row

LICENSE=	LGPL21

BUILD_DEPENDS=	${PYTHON_PKGNAMEPREFIX}formencode>=1.2.2:www/py-formencode
RUN_DEPENDS:=	${BUILD_DEPENDS}

NO_ARCH=	yes
USES=		python
USE_PYTHON=	autoplist distutils

# currently MaxDB(SAP), ADODB are not supported
OPTIONS_DEFINE=	MYSQL PGSQL SQLITE MSSQL SYBASE
SYBASE_DESC=	sybase support

PORTDOCS=	*

MYSQL_RUN_DEPENDS=	${PYTHON_PKGNAMEPREFIX}MySQLdb>=0:databases/py-MySQLdb
PGSQL_RUN_DEPENDS=	${PYTHON_PKGNAMEPREFIX}psycopg2>=0:databases/py-psycopg2
SQLITE_RUN_DEPENDS=	${PYTHON_PKGNAMEPREFIX}sqlite3>=0:databases/py-sqlite3
MSSQL_RUN_DEPENDS=	${PYTHON_PKGNAMEPREFIX}pymssql>=0:databases/py-pymssql
SYBASE_RUN_DEPENDS=	${PYTHON_PKGNAMEPREFIX}sybase>=0.37:databases/py-sybase

post-install-DOCS-on:
	${MKDIR} ${STAGEDIR}${DOCSDIR}
	${INSTALL_MAN} ${WRKSRC}/README.txt ${STAGEDIR}${DOCSDIR}
	(cd ${WRKSRC}/docs && ${COPYTREE_SHARE} . ${STAGEDIR}${DOCSDIR})

.include <bsd.port.mk>
