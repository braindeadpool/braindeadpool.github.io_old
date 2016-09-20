---
layout: post
title: Building tensor flow!
---

This time I decided to check out [TensorFlow](https://www.tensorflow.org/) - if you haven't heard of it, it's a fairly new machine learning library or rather a newly open-sourced machine learning library by Google with a Python API. I wanted to build it from source since I'm keen to play around the source-code itself or atleast take a shot at understanding it to some extent. If you are using Ubuntu or Mac OS X, the [official build instructions](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/g3doc/get_started/os_setup.md#installing-from-sources) are pretty straightforward.
Since Arch has some basic differences from other linux flavors out there, I decided to document here the steps I took to build `tensorflow` from scratch.

Let's start with the basic dependencies - `bazel, swig, python-numpy, python-wheel`

All but `bazel` can be installed from the official archlinux repositories (with pacman)

    sudo pacman -Sy python-numpy python-wheel swig

However, `bazel` is currently only available as an [AUR package](https://aur.archlinux.org/packages/bazel/). If you want to install the package manually, [look here](https://wiki.archlinux.org/index.php/Arch_User_Repository#Installing_packages). However, there is a nifty tool that can be used to easen this process - `packer` (I personally prefer this). [Here](http://www.ostechnix.com/install-packer-arch-linux-2/) is a good reference on how to go about setting it up.

If you try to install `bazel`, it will probably fail because you may not meet the java runtime requirements. So before installing `baze1` we need to install one more build time dependency (java 8) and configure it. I installed the official `jre8-openjdk` package for this.

    sudo pacman -Sy jre8-openjdk

Once installed, we set it to the default environment.

    archlinux-java set java-8-openjdk/jre/

You can verify it this has been set properly with the status option on the archlinux-java command.

    archlinux-java status

Finally, reboot to let the changes take effect.

Now we are ready to install `bazel`.

    packer -S bazel


Alright, so we got all the dependencies installed. About time, we finally got `tensorflow`. Go ahead and clone it from the github repo.

    git clone https://github.com/tensorflow/tensorflow

Once cloned, `cd` into the tensorflow directory and build it.

    cd tensorflow
    ./configure
    bazel build -c opt //tensorflow/tools/pip_package:build_pip_package
    mkdir _python_build
    cd _python_build
    ln -s ../bazel-bin/tensorflow/tools/pip_package/build_pip_package.runfiles/org_tensorflow/* .
    ln -s ../tensorflow/tools/pip_package/* .
    python setup.py develop

You can find the same build instructions along with more detailed information [here](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/g3doc/get_started/os_setup.md#setting-up-tensorflow-for-development).