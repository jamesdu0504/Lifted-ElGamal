include ../common.mk
ifeq ($(UNAME_S),Darwin)
  JAVA_INC=-I/System/Library/Frameworks/JavaVM.framework/Versions/Current/Headers/
  LIB_SUF=dylib
else
  JAVA_HOME=$(realpath $(dir $(realpath $(shell which javac)))..)
  JAVA_INC=-I$(JAVA_HOME)/include
  LIB_SUF=so
  CFLAGS+=-z noexecstack
  LIB+=-lrt
endif
CFLAGS+= -shared -fPIC $(JAVA_INC)

PACKAGE_NAME=jp.go.aist.risec.elgamal
PACKAGE_DIR=$(subst .,/,$(PACKAGE_NAME))

TARGET=../bin/libelgamal_if_wrap.$(LIB_SUF)
JAVA_EXE=cd ../bin && LD_LIBRARY_PATH=./:$(LD_LIBRARY_PATH) java -classpath ../java
all: $(TARGET)

elgamal_if_wrap.cxx: elgamal_if.i
	swig -java -package $(PACKAGE_NAME) -outdir $(PACKAGE_DIR) -c++ -Wall elgamal_if.i

$(TARGET): elgamal_if_wrap.cxx
	$(PRE)$(CXX) $< -o $@ $(CFLAGS) $(LDFLAGS)


%.class: %.java
	javac $<

jar:
	jar cvf elgamal.jar jp

test: ElgamalTest.class $(TARGET)
	$(JAVA_EXE) ElgamalTest
	$(JAVA_EXE) ElgamalTest -e NIST_P192
	$(JAVA_EXE) ElgamalTest -e NIST_P256 -h sha256
	$(JAVA_EXE) ElgamalTest -e NIST_P384 -h sha384
	$(JAVA_EXE) ElgamalTest -e NIST_P521 -h sha512

clean:
	rm -rf *.class $(TARGET) $(PACKAGE_DIR)/*.class

