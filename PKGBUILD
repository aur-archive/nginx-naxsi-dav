# Maintainer: Tom Kuther <tom@kuther.net>

_cfgdir=/etc/nginx
_tmpdir=/var/lib/nginx

pkgname=nginx-naxsi-dav
pkgver=1.6.0
_naxsi_ver=0.53-2
pkgrel=1
pkgdesc="lightweight HTTP server and IMAP/POP3 proxy server, with Naxsi WAF and dav-ext-module"
arch=('i686' 'x86_64')
depends=('pcre' 'zlib' 'openssl' 'geoip')
makedepends=('passenger' 'git')
optdepends=('passenger' 'nx_util: Naxsi whitelist and reports generator')
url="http://nginx.org"
license=('custom')
install=install
backup=(${_cfgdir:1}/fastcgi.conf
	${_cfgdir:1}/fastcgi_params
	${_cfgdir:1}/koi-win
	${_cfgdir:1}/koi-utf
	${_cfgdir:1}/mime.types
	${_cfgdir:1}/nginx.conf
	${_cfgdir:1}/scgi_params
	${_cfgdir:1}/uwsgi_params
	${_cfgdir:1}/win-utf
	etc/logrotate.d/nginx)
source=("http://nginx.org/download/nginx-$pkgver.tar.gz"
	"nginx-dav-ext-module::git+https://github.com/arut/nginx-dav-ext-module.git"
        "naxsi-${_naxsi_ver}.tar.gz::https://github.com/nbs-system/naxsi/archive/${_naxsi_ver}.tar.gz"
	service
	logrotate)
md5sums=('8efa354f1c3c2ccf434a50d3fbe82340'
         'SKIP'
         '348b50914a1eedaed09a2509621adf43'
         'ce9a06bcaf66ec4a3c4eb59b636e0dfd'
         '3441ce77cdd1aab6f0ab7e212698a8a7')

provides=('nginx')
conflicts=('nginx')
replaces=('nginx')

build() {
	cd "$srcdir/nginx-${pkgver}"

        ./configure \
        --prefix=$_cfgdir \
        --conf-path=$_cfgdir/nginx.conf \
        --sbin-path=/usr/bin/nginx \
        --pid-path=/var/run/nginx.pid \
        --lock-path=/var/lock/nginx.lock \
        --user=http --group=http \
        --http-log-path=/var/log/nginx/access.log \
        --error-log-path=/var/log/nginx/error.log \
        --http-client-body-temp-path=$_tmpdir/client-body \
        --http-proxy-temp-path=$_tmpdir/proxy \
        --http-fastcgi-temp-path=$_tmpdir/fastcgi \
        --http-scgi-temp-path=$_tmpdir/scgi \
        --http-uwsgi-temp-path=$_tmpdir/uwsgi \
        --with-imap --with-imap_ssl_module \
        --with-ipv6 --with-pcre-jit \
        --with-file-aio \
        --with-http_dav_module \
        --with-http_geoip_module \
        --with-http_gunzip_module \
        --with-http_gzip_static_module \
        --with-http_realip_module \
        --with-http_spdy_module \
        --with-http_ssl_module \
        --with-http_stub_status_module \
        --with-http_mp4_module \
	--add-module=$srcdir/naxsi-$_naxsi_ver/naxsi_src \
        --add-module=/usr/lib/passenger/ext/nginx \
	--add-module=$srcdir/nginx-dav-ext-module \
        #--with-http_addition_module \
        #--with-http_xslt_module \
        #--with-http_image_filter_module \
        #--with-http_sub_module \
        #--with-http_flv_module \
        #--with-http_random_index_module \
        #--with-http_secure_link_module \
        #--with-http_degradation_module \
        #--with-http_perl_module \

	make
}

package() {
	cd "$srcdir/nginx-${pkgver}"
	make DESTDIR="$pkgdir" install

        sed -e 's|\<user\s\+\w\+;|user html;|g' \
	    -e '44s|html|/usr/share/nginx/html|' \
	    -e '54s|html|/usr/share/nginx/html|' \
	    -i "$pkgdir"/etc/nginx/nginx.conf
	rm "$pkgdir"/etc/nginx/*.default

	install -d "$pkgdir"/$_tmpdir
	install -dm700 "$pkgdir"/$_tmpdir/proxy

	chmod 750 "$pkgdir"/var/log/nginx
	chown http:log "$pkgdir"/var/log/nginx

	install -d "$pkgdir"/usr/share/nginx
	mv "$pkgdir"/etc/nginx/html/ "$pkgdir"/usr/share/nginx

	install -Dm644 "$srcdir"/logrotate "$pkgdir"/etc/logrotate.d/nginx
	install -Dm644 "$srcdir"/service "$pkgdir"/usr/lib/systemd/system/nginx.service
	install -D -m644 LICENSE "$pkgdir"/usr/share/licenses/nginx/LICENSE
	rm -rf "$pkgdir"/var/run

	install -Dm644 "$srcdir/naxsi-$_naxsi_ver/naxsi_config/naxsi_core.rules" "$pkgdir/etc/nginx/naxsi_core.rules"
}
