.PHONY: module

BASEDIR :=${CURDIR}
BUILDDIR:="$(BASEDIR)/build"
ROOTDIR:="$(BASEDIR)/install"
SRCDIR:="$(BASEDIR)/src"
SRCTARBALLURL:="https://downloads.mysql.com/archives/get/file/mysql-5.6.38.tar.gz"
SRCTARBALL:=mysql-5.6.38.tar.gz
CFLAGS="-fPIC"
CXXFLAGS="-fPIC -fpermissive"

module: usr.manifest

usr.manifest: install
	printf "\
/etc/**: $(ROOTDIR)/etc/**\n\
/usr/share/**: $(ROOTDIR)/usr/share/**\n\
/usr/lib/**: $(ROOTDIR)/usr/lib/**\n\
/usr/data/**: $(ROOTDIR)/usr/data/**\n\
/usr/bin/mysqld: $(BUILDDIR)/sql/mysqld\n\
" > usr.manifest

.PHONY: install reinstall
install: install.depend
reinstall:
	rm -f install.depend
	$(MAKE) install

install.depend: build.depend
	rm -rf $(ROOTDIR)
	mkdir -p $(ROOTDIR)/etc # includes install dir
	$(MAKE) -C $(BUILDDIR) install DESTDIR=$(ROOTDIR)
	cp $(BASEDIR)/my.cnf $(ROOTDIR)/etc/
	./configure_install.bash $(BASEDIR) $(ROOTDIR) $(PRESEED)
	touch install.depend


.PHONY: build
build: build.depend

$(SRCTARBALL):
	test -f $@ || wget -O $@ $(SRCTARBALLURL)

src.depend: $(SRCTARBALL)
	rm -rf $(SRCDIR)
	mkdir -p $(SRCDIR)
	tar -C "$(SRCDIR)" -x --strip-components=1 -f "$<"
	touch $@

build.depend: src.depend
	mkdir -p $(BUILDDIR)
	cd $(BUILDDIR); \
	cmake \
		-DCMAKE_INSTALL_PREFIX:PATH=/usr \
		-DCMAKE_CXX_FLAGS='-fpermissive -fPIC' \
		-DCMAKE_C_FLAGS='-fPIC' \
		-DWITH_UNIT_TESTS=OFF \
		-DWITH_NDBCLUSTER=OFF \
		-DWITH_NDBCLUSTER_STORAGE_ENGINE=OFF \
		-DWITH_PROTOBUF=OFF \
		-DWITH_EMBEDDED_SERVER=OFF \
		-DWITH_EMBEDDED_SHARED_LIBRARY=OFF \
		-DWITH_MYSQLD_LDFLAGS="-pie" \
		$(SRCDIR)
	$(MAKE) -C $(BUILDDIR)
	touch build.depend
	
.PHONY: clean
clean:
	rm -rf build install usr.manifest *.depend

.PHONY: cleanall
cleanall: clean

