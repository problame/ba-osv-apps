ifeq ($(OSV_BASE), )
    $(error "expecting OSV_BASE env variable to point to OSv src directory")
endif

TARGET=cs_microbench
LIBFAKEOSVDIR=$(OSV_BASE)/libfakeosv

.PHONY: module
module: $(TARGET).so $(TARGET) usr.manifest $(TARGET).wrapper

CXXFLAGS = -g -fPIC -I$(OSV_BASE)/include

LDFLAGS += -lpthread -lboost_program_options

OBJS += main.o
OBJS += hopper.o
OBJS += cachestress.o
OBJS += server.o
OBJS += ithrash.o
OBJS += icachelatency.o
OBJS += stagel1i.o
# cachestress code only works with O0
cachestress.o: CXXFLAGS += -O0
icachelatency.o: CXXFLAGS += -O0


HEADERS += $(OSV_BASE)/include/osv/stagesched.h
HEADERS += cs_microbench.hh
HEADERS += histogram.hh
HEADERS += work.hh
HEADERS += build.h

$(shell ./genbuild.sh)

$(TARGET).so: $(OBJS)
	$(CXX) -shared -o $@ $(LDFLAGS) $(OBJS)

$(TARGET): $(OBJS) $(TARGET).so $(LIBFAKEOSVDIR)/libfakeosv.so
	$(CXX) -o $@ $(LDFLAGS) -L$(LIBFAKEOSVDIR) -lfakeosv $(OBJS)

.PHONY: usr.manifest
usr.manifest:
	echo '/$(TARGET).so: $${MODULE_DIR}/$(TARGET).so' > $@

$(OBJS): %.o: %.cc $(HEADERS)
	$(CXX) $(CXXFLAGS) -c $<

$(TARGET).wrapper: $(TARGET)
	echo '#!/bin/bash' > $@
	echo '# a helper to execute microbenchmarks using libfakeosv and niced on a Linux system' >> $@
	echo cd \"\$$\(dirname \$$0\)\" >> $@
	echo export LD_LIBRARY_PATH=\$$\(readlink -f "$(LIBFAKEOSVDIR)"\) >> $@
	echo exec nice -n -20 ./cs_microbench \$@ >> $@
	chmod +x $@

.PHONY: run
run_linux: $(TARGET)
	LD_LIBRARY_PATH=$(LIBFAKEOSVDIR) ./$(TARGET)

.PHONY: clean
clean:
	rm -f build.h
	rm -f *.o
	rm -f $(TARGET)
	rm -f $(TARGET).so
