OCAMLC=ocamlc
OCAMLOPT=ocamlopt
OCAMLDEP=ocamldep

DESTDIR=`$(OCAMLC) -where`/ocamlmpi
MPIINCDIR=/home/pad/packages/Linux/include
MPILIBDIR=/home/pad/packages/Linux/lib
#/usr/local/lib/mpich/lib/LINUX/ch_p4

CC=gcc
CFLAGS=-I`$(OCAMLC) -where` -I$(MPIINCDIR) -O -g -Wall

COBJS=init.o comm.o msgs.o collcomm.o groups.o utils.o
OBJS=mpi.cmo

all: libcamlmpi.a mpi.cma mpi.cmxa

install:
	cp mpi.mli mpi.cmi mpi.cma mpi.cmxa mpi.a libcamlmpi.a $(DESTDIR)

libcamlmpi.a: $(COBJS)
	rm -f $@
	ar rc $@ $(COBJS)

mpi.cma: $(OBJS)
	$(OCAMLC) -a -o mpi.cma -custom $(OBJS) -cclib -lcamlmpi -ccopt -L$(MPILIBDIR) -cclib -lmpich

mpi.cmxa: $(OBJS:.cmo=.cmx)
	$(OCAMLOPT) -a -o mpi.cmxa $(OBJS:.cmo=.cmx) -cclib -lcamlmpi -ccopt -L$(MPILIBDIR) -cclib -lmpich

.SUFFIXES: .ml .mli .cmo .cmi .cmx

.ml.cmo:
	$(OCAMLC) -c $<
.mli.cmi:
	$(OCAMLC) -c $<
.ml.cmx:
	$(OCAMLOPT) -c $<

testmpi: test.ml mpi.cma libcamlmpi.a
	ocamlc -o testmpi unix.cma mpi.cma test.ml -ccopt -L.

clean::
	rm -f testmpi

test: testmpi
	mpirun -np 5 ./testmpi

test_mandel: test_mandel.ml mpi.cmxa libcamlmpi.a
	ocamlopt -o test_mandel graphics.cmxa mpi.cmxa test_mandel.ml -ccopt -L.

test_pad: test_pad.ml mpi.cma
	ocamlc -o test_pad -I ../commons bigarray.cma str.cma unix.cma  ../commons/commons.cma mpi.cma test_pad.ml -ccopt -L.

test2:
	mpirun -np 3 ./test_pad arg1 arg2

clean::
	rm -f test_mandel

clean::
	rm -f *.cm* *.o *.a
depend:
	$(OCAMLDEP) *.ml > .depend
	gcc -MM $(CFLAGS) *.c >> .depend

include .depend

