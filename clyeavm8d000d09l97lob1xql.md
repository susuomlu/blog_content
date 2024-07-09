---
title: "The simplest way to locally distribute Debian packages"
datePublished: Tue Jul 09 2024 11:00:43 GMT+0000 (Coordinated Universal Time)
cuid: clyeavm8d000d09l97lob1xql
slug: the-simplest-way-to-locally-distribute-debian-packages
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/S6OvsSwm5sE/upload/e7c546a3d2d2db179159346634599e46.jpeg
tags: linux, debian, debian-package

---

Sometime, you will need to install some packages on a remote machine without internet access.

Or, you want to distribute your own Debian packages in your environment.

The simplest way to do it is to setup your own local repository.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1720521730143/e9e3fd4a-4cb8-4d31-abad-4509d71c5313.png align="center")

# **Requirements**

* python3
    
* dpkg-scanpackages: `sudo apt-get install dpkg-dev`
    
* gzip: `sudo apt-get install gzip`
    

# **Create your** repository folder **structure**

You can customize it, but I will keep my structure quick and simple:

`mkdir ~/my_repo/debian`

Then go to that directory:

`cd my_repo`

# **Add your .deb files to the debian folder**

`cp my_deb_thing.deb my_deb_thing2.deb my_repo/debian`

In this example, 2 have put 2 packages for kibana and elasticsearch to the `debian` folder.

# **Create Packages.gz file**

You'll need to do this every time you add/update a .deb.

`dpkg-scanpackages debian /dev/null | gzip -9c > debian/Packages.gz`

You'll get an output similar to:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1720520722704/af4a04a4-d749-44f7-b201-f2c5f3cc5105.png align="center")

`dpkg-scanpackages` will create indices for your packages so Debian package manager can read it.

# **Run a webserver to host it**

Any webserver will do, simply used Python.

`python3 -m http.server 9000`

The port can be change accordingly.

# **Configure client machines to point to your debian repository**

Add to client machine `/etc/apt/sources.list`

`deb [trusted=yes]`[`http://your-server-ip:9000`](http://10.10.80.129:9000)`debian/`

Note that the packages will be non authenticated, so if you want to stop having warnings you'll need to add the \[trusted=yes\]

When update, we will see client getting packages:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1720521173532/631bb805-4cb9-4ff1-8cd5-944d34b8d83c.png align="center")

Then client can install package using APT package manager, just like a normal packages.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1720521939337/df799f32-c8ce-4817-8242-36b4f0d403ee.png align="center")

# Conclusion

In this post, I show you a quick and easy way to host your own APT repo.

However, for the sake of simplicity, I didn't use any proper webserver; and I didn't secure the distribution process.

There are more secure method like `apt-mirror` or `rerepo` and add your own GPG key.