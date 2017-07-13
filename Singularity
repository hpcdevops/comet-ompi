BootStrap:docker
From:centos:latest

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
		echo "Installing OpenMPI into container..."
		mkdir /tmp/git
		cd /tmp/git
		git clone https://github.com/open-mpi/ompi.git
		cd ompi
		./autogen.pl
		./configure --prefix=/usr/local
		make
		make install

		echo "Updating PATH environment variable for build..."
		export PATH=/usr/local/bin:/usr/local/sbin:/usr/bin:/bin:/usr/sbin:/sbin:/bin:/sbin:/usr/bin:/usr/sbin:/bin:/sbin:/usr/bin:/usr/sbin

		echo "Making all OpenMPI examples..."
		cd examples
		make
		find . -type f -executable -exec cp -v '{}' /usr/local/bin \;
		cd /
		rm -rf /tmp/git
	fi

	echo "Adding overlay mount targets"
	mkdir -p /oasis
	mkdir -p /projects
	mkdir -p /scratch

	exit 0
