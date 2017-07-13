BootStrap: shub
From: singularityhub/centos:master

%labels
MAINTAINER hpcdevops
WHATAMI comet-ompi

%runscript
	echo "Arguments received: $*"
	exec /usr/bin/python "$@"

%test
	/usr/local/bin/ompi_info --version
	/usr/local/bin/ompi_info --config
	/usr/local/bin/mpirun --allow-run-as-root /usr/local/bin/ring_c

%post
	echo "Installing Development Tools YUM group"
	yum -y groupinstall "Development Tools"

        if [[ ! -x /usr/local/bin/mpicc ]]; then
		echo "Installing latest GNU autoconf into container..."
		mkdir /tmp/gnu
		cd /tmp/gnu
		curl -LO http://ftp.gnu.org/gnu/autoconf/autoconf-latest.tar.gz
		tar -xzf autoconf-latest.tar.gz
		cd $(find . -maxdepth 1 -type d -name "autoconf-*")
		./configure --prefix=/usr/local
		make -j12
		make install

		echo "Updating PATH environment variable for build..."
		export PATH=/usr/local/bin:/usr/local/sbin:/usr/bin:/bin:/usr/sbin:/sbin:/bin:/sbin:/usr/bin:/usr/sbin:/bin:/sbin:/usr/bin:/usr/sbin

		echo "Installing updated GNU automake into container..."
		cd /tmp/gnu
		curl -LO http://ftp.gnu.org/gnu/automake/automake-1.15.tar.gz
		tar -xzf automake-1.15.tar.gz
		cd automake-1.15
		./configure --prefix=/usr/local
		make -j12
		make install

		echo "Installing updated GNU libtool into container..."
		cd /tmp/gnu
		curl -LO http://gnu.mirror.constant.com/libtool/libtool-2.4.6.tar.gz
		tar -xzf libtool-2.4.6.tar.gz
		cd libtool-2.4.6
		./configure --prefix=/usr/local
		make -j12
		make install
		cd /tmp
		rm -rf /tmp/gnu

		echo "Installing OpenMPI into container..."
		mkdir /tmp/git
		cd /tmp/git
		git clone https://github.com/open-mpi/ompi.git
		cd ompi
		./autogen.pl
		./configure --prefix=/usr/local
		make -j12
		make install

		echo "Making all OpenMPI examples..."
		cd examples
		make
		find . -type f -executable -exec cp -v '{}' /usr/local/bin \;
		cd ~
		rm -rf /tmp/git
	fi

	echo "Adding overlay mount targets"
	mkdir -p /oasis
	mkdir -p /projects
	mkdir -p /scratch

	exit 0
