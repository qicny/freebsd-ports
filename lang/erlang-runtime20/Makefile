# Created by: ruslan@shevchenko.kiev.ua
# $FreeBSD$

PORTNAME=	erlang
PORTVERSION=	20.1.6
CATEGORIES=	lang parallel java
MASTER_SITES=	http://www.erlang.org/download/:erlangorg		\
		http://erlang.stacken.kth.se/download/:erlangorg	\
		http://www.csd.uu.se/ftp/mirror/erlang/download/:erlangorg \
		http://olgeni.olgeni.com/~olgeni/distfiles/:local	\
		LOCAL/olgeni:local
PKGNAMESUFFIX=	-runtime20
DISTNAME=	otp_src_${ERL_RELEASE}
DISTFILES=	${DISTNAME}${EXTRACT_SUFX}:erlangorg	\
		${ERLANG_MAN}:erlangorg			\
		preloaded-${PORTVERSION}${EXTRACT_SUFX}:local
DIST_SUBDIR=	erlang
EXTRACT_ONLY=	${DISTNAME}${EXTRACT_SUFX}

PATCH_SITES=	http://olgeni.olgeni.com/~olgeni/distfiles/ \
		LOCAL/olgeni
PATCHFILES=	patch-otp-20.1.1 patch-otp-20.1.2 patch-otp-20.1.3 \
		patch-otp-20.1.4 patch-otp-20.1.5 patch-otp-20.1.6

MAINTAINER=	olgeni@FreeBSD.org
COMMENT=	Functional programming language from Ericsson

LICENSE=	APACHE20

BROKEN_sparc64=	Fails to build: error: fp_except undeclared

WRKSRC=		${WRKDIR}/otp_src_${ERL_RELEASE}

ERLANG_LIB=	${PORTNAME}${PORTVERSION:C/\..*//}

SUB_FILES=	pkg-message
SUB_LIST=	ERLANG_LIB=${ERLANG_LIB}

MAKE_JOBS_UNSAFE=yes

OPTIONS_DEFINE=	DIRTY DOCS DTRACE GS HIPE JAVA KQUEUE NATIVE ODBC OPENSSL SCTP SMP THREADS WX

DIRTY_DESC=	Enable Dirty schedulers (experimental)
DTRACE_DESC=	Enable DTrace support
GS_DESC=	Enable GS application (deprecated)
HIPE_DESC=	Build native HiPE compiler
KQUEUE_DESC=	Enable Kernel Poll (kqueue) support
NATIVE_DESC=	Enable native libraries
SCTP_DESC=	Enable SCTP support
SMP_DESC=	Enable SMP support
WX_DESC=	Enable WX application

NATIVE_IMPLIES=	HIPE

OPTIONS_DEFAULT=HIPE KQUEUE OPENSSL SCTP SMP THREADS DTRACE
OPTIONS_EXCLUDE_DragonFly=	HIPE NATIVE SCTP

.if !exists(/usr/sbin/dtrace)
OPTIONS_EXCLUDE+=	DTRACE
.endif

OPTIONS_EXCLUDE_FreeBSD_10=	DTRACE
OPTIONS_EXCLUDE_i386=		DTRACE
# ld(1) fails to link probes: missing __dtrace_erlang___* symbols
OPTIONS_EXCLUDE_aarch64=	DTRACE

ERL_RELEASE=	20.1

USES=		gmake ncurses perl5
USE_PERL5=	build
GNU_CONFIGURE=	yes
LDFLAGS+=	-L${LOCALBASE}/lib

APP_UPGRADE=	erts           9.1 9.1.5 \
		lib/compiler 7.1.2 7.1.3 \
		lib/diameter   2.1 2.1.2 \
		lib/inets    6.4.2 6.4.4 \
		lib/snmp     5.2.7 5.2.8 \
		lib/ssh        4.6 4.6.2

.include <bsd.port.options.mk>

.if ${PORT_OPTIONS:MDOCS}
DISTFILES+=	${ERLANG_DOCS}:erlangorg
.endif

# If you run Erlang and get a message resembling "WARNING: number of
# probes fixed does not match the number of defined probes (54 != 132,
# respectively)" you probably misconfigured DTrace in some way.

.if ${OPSYS} == FreeBSD && ${OSVERSION} > 1100000
CFLAGS+=	-DMAP_NORESERVE=0
.endif

.if ${PORT_OPTIONS:MDTRACE}
CONFIGURE_ARGS+=--with-dynamic-trace=dtrace
CFLAGS+=	-fno-omit-frame-pointer
STRIP=
.else
CONFIGURE_ARGS+=--without-dynamic-trace
.endif

.if ${PORT_OPTIONS:MOPENSSL}
USES+=		ssl
CONFIGURE_ARGS+=--with-ssl=${OPENSSLBASE}
.else
CONFIGURE_ARGS+=--without-ssl
.endif

.if ${PORT_OPTIONS:MJAVA}
USE_JAVA=	yes
CONFIGURE_ARGS+=--with-javac
CONFIGURE_ENV+=	ac_cv_prog_JAVAC="${JAVAC}"
.else
CONFIGURE_ARGS+=--without-javac
.endif

.if ${PORT_OPTIONS:MHIPE}
CONFIGURE_ARGS+=--enable-hipe
.else
CONFIGURE_ARGS+=--disable-hipe
.endif

.if ${PORT_OPTIONS:MNATIVE}
CONFIGURE_ARGS+=--enable-native-libs
.else
CONFIGURE_ARGS+=--disable-native-libs
.endif

.if ${PORT_OPTIONS:MKQUEUE}
CONFIGURE_ARGS+=--enable-kernel-poll
.else
CONFIGURE_ARGS+=--disable-kernel-poll
.endif

.if ${PORT_OPTIONS:MGS}
USES+=		tk:run
.endif

.if ${PORT_OPTIONS:MWX}
USE_WX=		2.8+
WX_COMPS=	wx contrib
CONFIGURE_ARGS+=--with-wx-config=${WX_CONFIG}
USE_GL+=	gl glu
.endif

.if ${PORT_OPTIONS:MODBC}
CONFIGURE_ARGS+=--with-odbc
LIB_DEPENDS+=	libodbc.so:databases/unixODBC
.else
CONFIGURE_ARGS+=--without-odbc
.endif

ERLANG_MAN=	otp_doc_man_${ERL_RELEASE}.tar.gz
ERLANG_DOCS=	otp_doc_html_${ERL_RELEASE}.tar.gz

.if ${PORT_OPTIONS:MTHREADS}
CONFIGURE_ARGS+=--enable-threads
.else
CONFIGURE_ARGS+=--disable-threads
.endif

.if ${PORT_OPTIONS:MSCTP}
CONFIGURE_ARGS+=--enable-sctp
.else
CONFIGURE_ARGS+=--disable-sctp
.endif

# enabling --enable-smp-support crashes the OS when
# net_kernel:start([node_name, shortnames]) invoked repeatedly
# TODO verify if it still applies

.if ${PORT_OPTIONS:MSMP}
CONFIGURE_ARGS+=--enable-smp-support
.else
CONFIGURE_ARGS+=--disable-smp-support
.endif

.if ${PORT_OPTIONS:MDIRTY}
CONFIGURE_ARGS+=--enable-dirty-schedulers
.endif

.if ${ARCH} == i386
MAKE_ARGS+=	ARCH=x86
.endif

.if ${ARCH} == armv6 || ${ARCH} == armv7
MAKE_ARGS+=	ARCH=arm
.endif

# The man-pages are put (in spite of FreeBSD's port convention) in a private
# subdir. This is to avoid cluttering up the man page name space. Also the
# Erlang man pages are more of internal documentation using the man format than
# actual system man pages. (erl.1 and epmd.1 perhaps being the exception).

# Install documentation. (HTML docs need to be in same dir as the
# rest, not in share/doc/erlang as it should, because of relative
# links in the documentation.

pre-configure:
.if ! ${PORT_OPTIONS:MWX}
	echo "disabled by port options" > ${WRKSRC}/lib/wx/SKIP
.endif
	cd ${WRKSRC} && ${TAR} --unlink -xzpf ${DISTDIR}/${DIST_SUBDIR}/preloaded-${PORTVERSION}${EXTRACT_SUFX}

post-configure:
.if ${PORT_OPTIONS:MODBC}
	${RM} ${WRKSRC}/lib/odbc/SKIP
.endif

.if ${PORT_OPTIONS:MNATIVE}
pre-build:
	@cd ${WRKSRC} && ${MAKE_CMD} clean
.endif

post-install:
	for SECTION in 1 3 4 6 7; do \
		${TAR} --unlink -xzpf ${DISTDIR}/${DIST_SUBDIR}/${ERLANG_MAN} \
			-C ${STAGEDIR}${PREFIX}/lib/${ERLANG_LIB} \
			"man/man$${SECTION}/*.$${SECTION}" || ${TRUE}; \
	done
	${RM} -r ${STAGEDIR}${PREFIX}/lib/${ERLANG_LIB}/man/cat?
.if ${PORT_OPTIONS:MDOCS}
	${TAR} --unlink -xzpf ${DISTDIR}/${DIST_SUBDIR}/${ERLANG_DOCS} \
		-C ${STAGEDIR}${PREFIX}/lib/${ERLANG_LIB}
.for app v1 v2 in ${APP_UPGRADE}
	${MV} ${STAGEDIR}${PREFIX}/lib/${ERLANG_LIB}/${app}-${v1}/* \
		${STAGEDIR}${PREFIX}/lib/${ERLANG_LIB}/${app}-${v2}
	${RMDIR} ${STAGEDIR}${PREFIX}/lib/${ERLANG_LIB}/${app}-${v1}
.endfor
	${INSTALL_DATA} ${WRKSRC}/lib/dialyzer/doc/*.txt \
		${STAGEDIR}${PREFIX}/lib/${ERLANG_LIB}/lib/dialyzer-*/doc/
.endif
.if ! ${PORT_OPTIONS:MGS}
	${RM} -r ${STAGEDIR}${PREFIX}/lib/erlang/lib/gs-*
.endif
.if ! ${PORT_OPTIONS:MJAVA}
	${RM} -r ${STAGEDIR}${PREFIX}/lib/erlang/lib/jinterface-*
.endif
.if ! ${PORT_OPTIONS:MODBC}
	${RM} -r ${STAGEDIR}${PREFIX}/lib/erlang/lib/odbc-*
.endif
.if ! ${PORT_OPTIONS:MOPENSSL}
	${RM} -r ${STAGEDIR}${PREFIX}/lib/erlang/lib/crypto-*
	${RM} -r ${STAGEDIR}${PREFIX}/lib/erlang/lib/ssh-*
	${RM} -r ${STAGEDIR}${PREFIX}/lib/erlang/lib/ssl-*
.endif
.if ! ${PORT_OPTIONS:MWX}
	${RM} -r ${STAGEDIR}${PREFIX}/lib/erlang/lib/wx-*
.endif
	${FIND} ${STAGEDIR}${PREFIX}/lib/${ERLANG_LIB}/* -type d -empty -delete

	@cd ${STAGEDIR}${PREFIX}; ${FIND} lib/${ERLANG_LIB}/* -type f -o -type l \
		| ${SORT} \
		>> ${TMPPLIST}

.include <bsd.port.pre.mk>

.if ${PORT_OPTIONS:MOPENSSL} && ${SSL_DEFAULT:Mopenssl-devel}
BROKEN=		Does not build with openssl-devel
.endif

.include <bsd.port.post.mk>
