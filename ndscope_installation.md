# Install [`ndscope`](https://pypi.org/project/ndscope/) on `op3anu`

> `ndscope` is actively maintained by ligo, refer to this public [repo](https://git.ligo.org/cds/software/ndscope) on GitLab for source code.

## Check `op3anu` system info: `cat /etc/os-release`

`op3anu`(`192.168.1.12`) uses Scientific Linux which is a red hat distribution `RHEL`, so we'll need to use package manager `yum` instead of `apt` or `apt-get` that's on other `opXanu` (debian).

## Upgrade `python` version to 3.8 or higher

One of the dependencies that `ndscope` package uses requires the system `python3` version must be at least 3.8, and `pip3` version at least 12.0.
The original system `python3` version is 3.6.8, and `pip3` is also out of date.

There're several ways to manage different `python` versions on the same host machine, but as far as `ndscope` is concerned, it also needs to use another package manager `conda` to install modules such as `nds2` [1](https://github.com/gwpy/gwpy/issues/1019). And `conda` installation can aslo uses its own virtualenv and upgrade python to new versions.

So we'll install `conda` and use its `base` vitualenv.

```bash
# cd to a folder e.g. ~/Downloads to save the installation file
wget https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh
sh Miniconda3-latest-Linux-x86_64.sh
# check if it's correctly installed
conda --version
# restart bash SHELL
exec $SHELL
```

## Install [`ndscope`](https://pypi.org/project/ndscope/)

```bash
# now in the `base` virtual env created by conda with (base) at the beginning
pip install ndscope
# check if we can start ndscope
ndscope
```

There may be quite a few issues when trying to start ndscope.

### `... pyqtgraph requires one of pyqt5 pyqt6 pyside2 or pyside6 ...`

Error: `Cannot import PyQtGraph because packages PySide, PyQt4, or PyQt5 can't be imported`

By checking the hosts that can run `ndscope`, we can find that `PyQt5` is missing on `op3anu`. Because the system is `Redhat/CentOS`, we use

```bash
yum install qt5-qtbase-devel
# or we can use pip
pip install PyQt5
```

### `... No module named nds2 ...`

Here we have to use `conda` to solve [this issue](https://github.com/gwpy/gwpy/issues/1019):

```bash
# configure the conda-forge channel
conda config --add channels conda-forge
# install nds client
conda install python-nds2-client
```

### `... ImportError: /lib64/libstdc++.so.6: version GLIBCXX_3.4.20 not found ...`

```bash
# first locate the libstdc++
locate libstdc++.so.6
# check if it's a softlink
ls -ahl /lib64/libstdc++.so.6
# check if GLIBCXX_3.4.20 is already in the pointed file
strings /lib64/libstdc++.so.6
# because the libstdc++ in $HOME/miniconda3/lib has already installed GLIBCXX_3.4.20, so we want to change the file to point to the updated file in miniconda, e.g. libstdc++.so.6.0.30
cd /lib64/
ln -sf $HOME/miniconda3/lib/libstdc++.so.6.0.30 libstdc++.so.6
# check if ndscope can work
ndscope
```

### `... undefined symbol: FT_Get_Font_Format ...`

```bash
sudo yum update freetype-devel
```

## References

1. [Install `PyQt5` on different platforms](https://pythonbasics.org/install-pyqt/)
