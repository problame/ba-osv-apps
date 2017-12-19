TARGET=cs_microbench

.PHONY: module
module: $(TARGET).so $(TARGET) usr.manifest

CXXFLAGS = -g -fPIC -I../../include

LDFLAGS += -lpthread -lboost_program_options

OBJS += main.o
OBJS += hopper.o
OBJS += cachestress.o
# cachestress code only works with O0
cachestress.o: CXXFLAGS += -O0


HEADERS += ../../include/osv/stagesched.h
# HEADERS += build.h not included on purpose because it is re-generated every time
HEADERS += cs_microbench.hh
HEADERS += histogram.hh

$(TARGET).so: $(OBJS)
	$(CXX) -shared -o $@ $(LDFLAGS) $(OBJS)

$(TARGET): $(OBJS) $(TARGET).so libfakestd/libfakestd.so
	$(CXX) -o $@ $(LDFLAGS) -L./libfakestd -lfakestd $(OBJS)

.PHONY: libfakestd/libfakestd.so
libfakestd/libfakestd.so:
	make -C libfakestd libfakestd.so

.PHONY: usr.manifest
usr.manifest:
	echo '/$(TARGET).so: $${MODULE_DIR}/$(TARGET).so' > $@

$(OBJS): %.o: %.cc $(HEADERS)
	$(CXX) $(CXXFLAGS) -c $<

.PHONY: build.h
build.h:
	echo -n > $@
	echo "#define SCM_VERSION \"$$(git describe --tags --dirty)\"" >> $@

.PHONY: run
run_linux: $(TARGET)
	LD_LIBRARY_PATH=libfakestd ./$(TARGET)

.PHONY: clean
clean:
	make -C libfakestd clean
	rm -f *.o
	rm -f $(TARGET)
	rm -f $(TARGET).so