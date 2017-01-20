---
layout: post
title:  "Making your digital camera work on FreeBSD"
date:   2017-01-20 21:28:00 +1100
categories: freebsd camera howto
---

I have an old [Canon 400D DSLR camera](https://www.cnet.com/au/products/canon-eos-400d/review/) camera, a solid entry-level DLSR that I bought ages ago. I have never considered replacing it as it works perfectly and shoots good photos (even thought my iPhone camera probably has a higher resolution). After a holiday in Europe, I had several gigabytes of photos stored on the MMC memory card. Instead of importing the files on my laptop as I would normally do, I thought it was easier to simply plug the camera straight into my file server at home, via one of the USB ports.

Now my challenge was how to do this in FreeBSD. There is no built-in tool for doing it; however, a Google search reveals that it is really easy using the FuseFS stack (userland file system) with the ``gphotofs'' driver. I did as follows:

Install the [fusefs-gphotofs](http://www.freshports.org/graphics/fusefs-gphotofs) module. This allows us to mount the camera as a device in user land:

{% highlight shell %}
# sudo pkg install fusefs-gphotofs
# sudo kldload fuse
{% endhighlight %}

Tell gphotofs to mount any devices it recognises into /mnt. ls reveals that it found my camera: 
{% highlight shell %}
% gphotofs /mnt
% ls /mnt
store_00010001
{% endhighlight %}

Mount it:
{% highlight shell %}
% mount -t fusefs
/dev/fuse on /mnt (fusefs, local, synchronous)
{% endhighlight %}

If you want to access the camera without having to load the FuseFS kernel module every time, add fuse_load=YES to /boot/loader.conf.