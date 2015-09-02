# Raspberry Pi Emulation

- Download the [latest raspbian](https://downloads.raspberrypi.org/raspbian_latest)
- Unzip to this directory
- Convert from RAW to qcow2 format:

   ```
   $ qemu-img convert -O qcow2 rasbian-wheezy.img rasbian-wheezy.qcow2
   ```

- Create swap file

    ```
    $ qemu-img create -f qcow2 project_swap.qcow2 256M
    ```

- First boot:

   ```
   $ qemu-system-arm -cpu arm1176 -m 256 -M versatilepb -no-reboot -serial stdio -append "root=/dev/sda2 panic=1 rootfstype=ext4 rw init=/bin/bash" -kernel kernel-qemu -hda rasbian-wheezy.qcow2
   ```

- Comment out /etc/ld.so.preload

    ```
    pi $ vi /etc/ld.so.preload
    ```

- Create /etc/udev/rules.d/90-qemu.rules

  ```
  pi $ vi /etc/udev/rules.d/90-qemu.rules
  ```

  with

  ```
  KERNEL=="sda", SYMLINK+="mmcblk0"
  KERNEL=="sda?", SYMLINK+="mmcblk0p%n"
  KERNEL=="sda2", SYMLINK+="root"
  ```

- Turn off

  ```
  pi $ sudo shutdown -h now
  ```

- Resize root volume

  ```
  $ qemu-img resize raspbian-wheezy.qcow2 +4G
  ```

- Boot

  ```
  $ qemu-system-arm -cpu arm1176 -m 256 -M versatilepb -no-reboot -serial stdio -append "root=/dev/sda2 panic=1 rootfstype=ext4 rw" -kernel kernel-qemu -hda raspbian-wheezy.qcow2
  ```

- Fix root block device

  ```
  pi $ sudo ln -snf mmcblk0p2 /dev/root
  ```

- Expand Filesystem

  ```
  pi $ sudo raspi-config
  ```

- Turn off

  ```
  pi $ sudo shutdown -h now
  ```

- Create linked clone

  ```
  $ qemu-img create -b raspbian-wheezy.qcow2 -f qcow2 project.qcow2
  ```

- Boot project

  ```
  $ qemu-system-arm -cpu arm1176 -m 256 -M versatilepb -no-reboot -serial stdio -append "root=/dev/sda2 panic=1 rootfstype=ext4 rw" -kernel kernel-qemu -hda project.qcow2 -hdb project_swap.qcow2
  ```

- Create and activate swap

  ```
  pi $ sudo mkswap /dev/hdb
  pi $ sudo swapon /deb/hdb
  ```


## Sources
[http://www.royvanrijn.com/blog/2014/06/raspberry-pi-emulation-on-os-x/](http://www.royvanrijn.com/blog/2014/06/raspberry-pi-emulation-on-os-x/)
[https://github.com/dhruvvyas90/qemu-rpi-kernel/blob/master/kernel-qemu](https://github.com/dhruvvyas90/qemu-rpi-kernel/blob/master/kernel-qemu)
[http://lnx.cx/docs/vdg/html/ch02s04.html](http://lnx.cx/docs/vdg/html/ch02s04.html)
[https://www.raspberrypi.org/forums/viewtopic.php?f=53&t=8649](https://www.raspberrypi.org/forums/viewtopic.php?f=53&t=8649)
