.. _cdh_diskimage-builder-label:

Building Images for Cloudera Plugin
===================================

In this document you will find instructions on how to build Ubuntu and CentOS
images with Cloudera Express (now only versions {5.0.0, 5.3.0 5.4.0, 5.5.0,
5.7.x, 5.9.x} are supported).

To simplify the task of building such images we use
`Disk Image Builder <https://github.com/openstack/diskimage-builder>`_.

`Disk Image Builder` builds disk images using elements. An element is a
particular set of code that alters how the image is built, or runs within the
chroot to prepare the image.

Elements for building Cloudera images are stored in
`Sahara extra repository <https://github.com/openstack/sahara-image-elements>`_

.. note::

   Sahara requires images with cloud-init package installed:

   * `For CentOS 6 <http://mirror.centos.org/centos/6/extras/x86_64/Packages/cloud-init-0.7.5-10.el6.centos.2.x86_64.rpm>`_
   * `For CentOS 7 <http://mirror.centos.org/centos/7/extras/x86_64/Packages/cloud-init-0.7.5-10.el7.centos.1.x86_64.rpm>`_
   * `For Ubuntu 14 <http://packages.ubuntu.com/trusty/cloud-init>`_

To create cloudera images follow these steps:

1. Clone repository "https://github.com/openstack/sahara-image-elements" locally.

2. Use tox to build images.

   You can run "tox -e venv -- sahara-image-create" command in
   sahara-image-elements directory to build images. By default this script will
   attempt to create cloud images for all versions of supported plugins and all
   operating systems (subset of Ubuntu, Fedora, and CentOS depending on
   plugin). To only create Cloudera images, you should use the "-p cloudera"
   parameter in the command line. If you want to create the image only for a
   specific operating system, you should use the "-i ubuntu|centos|centos7"
   parameter to assign the operating system (the cloudera plugin only supports
   Ubuntu and Centos). If you want to create the image only for a specific
   Cloudera version, you should use the "-v 5.0|5.3|5.4|5.5|5.7|5.9" parameter
   to assign the version. Note that Centos 7 can only be used with CDH 5.5 and
   higher. Below is an example to create Cloudera images for both Ubuntu and
   CentOS with Cloudera Express 5.5.0 version.

   .. sourcecode:: console

      tox -e venv -- sahara-image-create -p cloudera -v 5.5

   If you want to create only an Ubuntu image, you may use following example
   for that:

   .. sourcecode:: console

      tox -e venv -- sahara-image-create -p cloudera -i ubuntu -v 5.5

   For CDH 5.7 and higher we support minor versions. If you want to build a minor
   version just export DIB_CDH_MINOR_VERSION before sahara-image-create launch, e.g.:

   .. sourcecode:: console

      export DIB_CDH_MINOR_VERSION=5.7.1

   NOTE: If you don't want to use default values, you should explicitly set the
   values of your required parameters.

   The script will create required cloud images using image elements that
   install all the necessary packages and configure them. You will find the
   created images in the parent directory.

.. note::

    Disk Image Builder will generate QCOW2 images, used with the default
    OpenStack Qemu/KVM hypervisors. If your OpenStack uses a different
    hypervisor, the generated image should be converted to an appropriate
    format.

    The VMware Nova backend requires the VMDK image format. You may use
    qemu-img utility to convert a QCOW2 image to VMDK.

    .. sourcecode:: console

        qemu-img convert -O vmdk <original_image>.qcow2 <converted_image>.vmdk


For finer control of diskimage-create.sh see the `official documentation
<https://github.com/openstack/sahara-image-elements/blob/master/diskimage-create/README.rst>`_
