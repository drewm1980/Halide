include ../support/Makefile.inc

all: $(BIN)/test

$(BIN)/halide_blur.generator: halide_blur_generator.cpp $(GENERATOR_DEPS)
	@mkdir -p $(@D)
	$(CXX) $(CXXFLAGS) -fno-rtti $(filter-out %.h,$^) -o $@ $(LDFLAGS) $(HALIDE_SYSTEM_LIBS)

$(BIN)/%/halide_blur.a: $(BIN)/halide_blur.generator
	@mkdir -p $(@D)
	$^ -g halide_blur -o $(BIN)/$* target=$(HL_TARGET)

# g++ on OS X might actually be system clang without openmp
CXX_VERSION=$(shell $(CXX) --version)
ifeq (,$(findstring clang,$(CXX_VERSION)))
OPENMP_FLAGS=-fopenmp
else
OPENMP_FLAGS=
endif

# -O2 is faster than -O3 for this app (O3 unrolls too much)
$(BIN)/%/test: $(BIN)/%/halide_blur.a test.cpp
	@mkdir -p $(@D)
	$(CXX-$*) $(CXXFLAGS-$*) $(OPENMP_FLAGS) -Wall -O2 -I$(BIN)/$* test.cpp $(BIN)/$*/halide_blur.a -o $@ $(LDFLAGS-$*)

clean:
	rm -rf $(BIN)

test: $(BIN)/host/test
	$(BIN)/host/test
