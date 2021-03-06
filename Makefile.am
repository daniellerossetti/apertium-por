###############################################################################
## Makefile for apertium-por
###############################################################################

RELEASE=0.1
VERSION=0.1.0
LANG1=por
BASENAME=apertium-$(LANG1)

VAR1=#deixar en blanc
VAR2=_bra

TARGETS_COMMON =				\
	$(LANG1)$(VAR1).automorf.bin		\
	$(LANG1)$(VAR1).automorf.att.gz		\
	$(LANG1)$(VAR1).autogen.bin		\
	$(LANG1)$(VAR2).autogen.bin		\
	$(LANG1)$(VAR1).autogen.att.gz		\
	$(LANG1)$(VAR1).autopgen.bin		\
	$(LANG1)$(VAR2).autopgen.bin		\
	$(LANG1)$(VAR1).rlx.bin

# Use this goal for creating .deps, otherwise make -j2 will give problems:
@ap_include@

###############################################################################
## Portuguese transducer
###############################################################################

.deps/$(BASENAME).$(LANG1).pt.dix: $(BASENAME).$(LANG1).dix .deps/.d
	apertium-validate-dictionary $<
	python genvdix.py pt $< >$@

.deps/$(BASENAME).$(LANG1).br.dix: $(BASENAME).$(LANG1).dix .deps/.d
	apertium-validate-dictionary $<
	python genvdix.py br $< >$@

$(LANG1)$(VAR1).autogen.bin: .deps/$(BASENAME).$(LANG1).pt.dix
	apertium-validate-dictionary $<
	lt-comp -v pt rl $< $@ $(BASENAME).$(LANG1).acx

$(LANG1)$(VAR2).autogen.bin: .deps/$(BASENAME).$(LANG1).br.dix 
	apertium-validate-dictionary $<
	lt-comp -v br rl $< $@ $(BASENAME).$(LANG1).acx


$(LANG1)$(VAR1).automorf.bin: $(BASENAME).$(LANG1).dix
	apertium-validate-dictionary $<
	lt-comp lr $< $@ $(BASENAME).$(LANG1).acx

$(LANG1)$(VAR1).autogen.att.gz: $(LANG1).autogen.bin
	lt-print $< | gzip -9 -c -n > $@

$(LANG1)$(VAR1).automorf.att.gz: $(LANG1).automorf.bin
	lt-print $< | gzip -9 -c -n > $@

$(LANG1)$(VAR1).autopgen.bin: $(BASENAME).post-$(LANG1).dix
	lt-comp -a pt lr $< $@

$(LANG1)$(VAR2).autopgen.bin: $(BASENAME).post-$(LANG1).dix
	lt-comp -a br lr $< $@

###############################################################################
## Disambiguation rules
###############################################################################

$(LANG1).rlx.bin: $(BASENAME).$(LANG1).rlx $(CGCOMP)
	$(CGCOMP) $< $@

###############################################################################
## Distribution
###############################################################################
EXTRA_DIST=$(BASENAME).$(LANG1).dix		\
		$(BASENAME).$(LANG1).acx	\
		$(BASENAME).$(LANG1).rlx	\
		$(BASENAME).post-$(LANG1).dix	\
		$(LANG1).prob			\
		modes.xml

###############################################################################
## Installation stuff
###############################################################################
#
#   apertium_por_dir: This is where the compiled binaries go
#   apertium_por_srcdir: This is where the source files go

apertium_pordir=$(prefix)/share/apertium/$(BASENAME)/
apertium_por_srcdir=$(prefix)/share/apertium/$(BASENAME)/

apertium_por_DATA=$(TARGETS_COMMON) $(LANG1).prob

pkgconfigdir = $(prefix)/share/pkgconfig
pkgconfig_DATA = $(BASENAME).pc

# Modes are not installed since they're all just "debug modes". All
# modes are created by this goal.
modes/$(LANG1)-morph.mode: modes.xml
	apertium-validate-modes modes.xml
	apertium-gen-modes modes.xml

noinst_DATA=modes/$(LANG1)-morph.mode

install-data-local: install-modes
	test -d $(DESTDIR)$(apertium_por_srcdir) || mkdir -p $(DESTDIR)$(apertium_por_srcdir)
	$(INSTALL_DATA) $(BASENAME).$(LANG1).dix $(DESTDIR)$(apertium_por_srcdir)
	$(INSTALL_DATA) $(BASENAME).$(LANG1).rlx $(DESTDIR)$(apertium_por_srcdir)
	$(INSTALL_DATA) $(BASENAME).post-$(LANG1).dix $(DESTDIR)$(apertium_por_srcdir)

CLEANFILES = $(TARGETS_COMMON)
clean-local:
	-rm -rf .deps modes

###############################################################################
## Test
###############################################################################

test: all
	echo "TODO test sentence" | apertium -d . por-morph | tee .test-mt
	@echo "TODO test analysis results" > .test-ref
	@diff .test-ref .test-mt
	@rm .test-ref .test-mt

