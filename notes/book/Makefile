PANDOC := $(shell command -v pandoc 2> /dev/null)
RUBBER := $(shell command -v rubber 2> /dev/null)
CIBLE := agr-notes.pdf
SRC := $(sort $(wildcard *.md))
SRC := $(filter-out TODO.md, $(SRC))
SRC := $(filter-out 099_todos.md, $(SRC))
BIB := $(wildcard *.bib)
CC := pandoc
LCC := rubber 
TEMPFILE := out.tex 
TMPDIR := build#$(shell mktemp -d)
CFLAGS := \
	--citeproc \
	-V links-as-notes=true \
	--pdf-engine=xelatex \
	--highlight-style=tango \
	-s \
	-t latex \
	-o ${TEMPFILE}
LCFLAGS := \
	--into ${TMPDIR} \
	--pdf \
	--module xelatex 
all: $(CIBLE)


#
# some reminders
# %.md is the first dependance  and $< represent this aka  %.md
# %.pdf is the cible and $@ represent the cible
#
%.pdf: ${SRC} ${BIB}
ifndef PANDOC
	$(error "pandoc is not available, perhaps install it")
endif
ifndef RUBBER
	$(error "rubber is not available, perhaps install it")
endif
	mkdir -p ${TMPDIR}
	$(CC) $(CFLAGS) ${SRC}
	$(LCC) $(LCFLAGS) ${TEMPFILE}
	mv "${TMPDIR}/$(basename ${TEMPFILE}).pdf" $@
	rm -fr ${TEMPFILE} 
	# ${TMPDIR}
