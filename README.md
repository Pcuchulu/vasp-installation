# vasp-installation
# first step ( installing fortran and c compilers)
I suggest instaling intel parallel studio\
in order to install it on linux OS first you need installation files :)\
Intel_Parallel_Studio_XE_2019 (recommended/)

sudo apt-get -y install build-essential emacs dkms synaptic g++ g++-multilib gfortran ssh openssh-server\
sudo apt-get install libgtk2.0\
sudo apt update\
sudo apt upgrade\
sudo ./install_GUI.sh\
after installation check if installed (which mpirun (must be /opt/intel/impi/2019.5.281/intel64/bin/mpirun))\

# seccond step ( installing fftw lib)
sudo mkdir /mirror\
sudo chown user:user /mirror/\
wget https://www.fftw.org/fftw-3.3.10.tar.gz\
cd fftw-3.3.10/\
tar xvf fftw-3.3.10.tar.gz\
./configure --prefix=/mirror/fftw3 BLAS_LIBS="$MKL" LAPACK_LIBS="$MKL" FFT_LIBS="-L$FFTW_LIB -lfftw3f" INCLUDEFFTW="$FFTW_INC"\
make all -j\
make install\

```
echo $IFORT_COMPILER_LIB$
```

# third step
vim (or just any shell or non-shell based editor) to edit bash\
add below lines to bash\

```
################################################# for inel2019
#############################################for intel

PATH="/opt/intel/bin:$PATH"
export PATH

PATH="/opt/intel/mkl/lib/intel64:$PATH"
export PATH
LD_LIBRARY_PATH="/opt/intel/mkl/lib/intel64:$LD_LIBRARY_PATH"
export LD_LIBRARY_PATH
PATH="/opt/intel/impi/2019.5.281/intel64/bin:$PATH"
export PATH
LD_LIBRARY_PATH="/opt/intel/mkl/lib/intel64:$LD_LIBRARY_PATH"
export LD_LIBRARY_PATH
PATH="/opt/intel/impi/2019.5.281/intel64/include:$PATH"
export PATH
PATH="/opt/intel/impi/2019.5.281/intel64/modulefiles:$PATH"
export PATH
LD_LIBRARY_PATH="/opt/intel/impi/2019.5.281/intel64/lib:$LD_LIBRARY_PATH"
export LD_LIBRARY_PATH

LD_LIBRARY_PATH="/opt/intel/impi/2019.5.281/intel64/lib/release:$LD_LIBRARY_PATH"
export LD_LIBRARY_PATH
LD_LIBRARY_PATH="/mirror/spglib/lib:$LD_LIBRARY_PATH"
export LD_LIBRARY_PATH

LD_LIBRARY_PATH="/opt/intel/impi/2019.5.281/intel64/libfabric/lib:$LD_LIBRARY_PATH"
export LD_LIBRARY_PATH

LD_LIBRARY_PATH="/opt/intel/impi/2019.5.281/intel64/libfabric/lib/prov:$LD_LIBRARY_PATH"
export LD_LIBRARY_PATH
PATH="/opt/intel/impi/2019.5.281/intel64/libfabric/bin:$PATH"
export PATH
LD_LIBRARY_PATH="/opt/intel/lib/intel64:$LD_LIBRARY_PATH"
export LD_LIBRARY_PATH
LD_LIBRARY_PATH="/opt/intel/mkl/lib/intel64_lin:$LD_LIBRARY_PATH"
export LD_LIBRARY_PATH
################################################## end intel
```

# forth step
in vasp (5.4.1) folder you see this ::\\❣\
![image](https://github.com/Pcuchulu/vasp-installation/assets/100542104/4d1eca4c-749b-41cc-a807-135dc7ea484b)\

copy below text in makefile.include\

```
# Precompiler options
CPP_OPTIONS= -DHOST=\"LinuxIFC\"\
-DMPI -DMPI_BLOCK=8000 \
-Duse_collective \
-DscaLAPACK \
-DCACHE_SIZE=4000 \
-Davoidalloc \
-Duse_bse_te \
-Dtbdyn \
-Duse_shmem

CPP = fpp -f_com=no -free -w0 $*$(FUFFIX) $*$(SUFFIX) $(CPP_OPTIONS)

FC = mpiifort
FCL = mpiifort -mkl=sequential -lstdc++

FREE = -free -names lowercase

FFLAGS = -assume byterecl -w
OFLAG = -O2
OFLAG_IN = $(OFLAG)
DEBUG = -O0

BLACS = -lmkl_blacs_intelmpi_lp64
SCALAPACK = /opt/intel/mkl/lib/intel64/libmkl_scalapack_lp64.a $(BLACS)

OBJECTS = fftmpiw.o fftmpi_map.o fft3dlib.o fftw3d.o /mirror/fftw3/lib/libfftw3.a

INCS =-I/mirror/fftw3/include

LLIBS = $(SCALAPACK) $(LAPACK) $(BLAS)


OBJECTS_O1 += fftw3d.o fftmpi.o fftmpiw.o
OBJECTS_O2 += fft3dlib.o

# For what used to be vasp.5.lib
CPP_LIB = $(CPP)
FC_LIB = $(FC)
CC_LIB = icc
CFLAGS_LIB = -O
FFLAGS_LIB = -O1
FREE_LIB = $(FREE)

OBJECTS_LIB= linpack_double.o getshmem.o

# For the parser library
CXX_PARS = icpc

LIBS += parser
LLIBS += -Lparser -lparser -lstdc++

# Normally no need to change this
SRCDIR = ../../src
BINDIR = ../../bin

#================================================
# GPU Stuff

CPP_GPU = -DCUDA_GPU -DRPROMU_CPROJ_OVERLAP -DUSE_PINNED_MEMORY -DCUFFT_MIN=28 -UscaLAPACK

OBJECTS_GPU = fftmpiw.o fftmpi_map.o fft3dlib.o fftw3d_gpu.o fftmpiw_gpu.o

CC = icc
CXX = icpc
CFLAGS = -fPIC -DADD_ -Wall -openmp -DMAGMA_WITH_MKL -DMAGMA_SETAFFINITY -DGPUSHMEM=300 -DHAVE_CUBLAS

CUDA_ROOT ?= /usr/local/cuda/
NVCC := $(CUDA_ROOT)/bin/nvcc -ccbin=icc
CUDA_LIB := -L$(CUDA_ROOT)/lib64 -lnvToolsExt -lcudart -lcuda -lcufft -lcublas

GENCODE_ARCH := -gencode=arch=compute_30,code=\"sm_30,compute_30\" \
-gencode=arch=compute_35,code=\"sm_35,compute_35\" \
-gencode=arch=compute_60,code=\"sm_60,compute_60\"

MPI_INC = $(I_MPI_ROOT)/include64/
```

# fifth step

    make std gam ncl

in order to make cd to the folder including makefile.include
 then add\
 export PATH="/home/p/Vienna Ab initio Simulation Package (VASP) 5.4.4 Source Code 2018-03-06/vasp.5.4.4/bin:$PATH"\

enjoy using 😍😍\
mpirun -np 2 vasp_std\

done🙄🙄
