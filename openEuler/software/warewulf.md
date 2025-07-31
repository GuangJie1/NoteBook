I did a simple test with the openEuler 24.03 image and encountered a known issue, which was resolved and everything worked as expected afterward. Below is a summary of the steps and some running logs:

1. Import the image
   
  wwctl image import docker://docker.io/baibgj/warewulf-openeuler:24.03 warewulf-openeuler:24.03

  ```
    Copying blob ed90f2ff4836 skipped: already exists  
    Copying blob a3d2e0939a02 done   | 
    Copying blob ad5c1755b89e done   | 
    Copying blob 73951662bd53 skipped: already exists  
    Copying blob 9f7449bda617 done   | 
    Copying blob 6861bbb204d1 done   | 
    Copying blob bf54bf7b48ab done   | 
    Copying config 8fb9642bdb done   | 
    Writing manifest to image destination
    2025/07/31 09:03:21  info unpack layer: sha256:73951662bd5354fff5cae8023c13ae2d3001e6336659577cdd039d77b7fa4559
    2025/07/31 09:03:23  info unpack layer: sha256:ed90f2ff483601e1c4df18c31b4607520fb174498c6adae9a616e50d050d7c47
    2025/07/31 09:03:23  info unpack layer: sha256:9f7449bda61771b43d2f57389ad7e458768db8b3cdff939be29a608d713f5968
    2025/07/31 09:03:33  info unpack layer: sha256:a3d2e0939a0276f41303599ec65cc9d4b111de9c8ec188816ccac553193ae294
    2025/07/31 09:03:33  info unpack layer: sha256:6861bbb204d1744b2e50cde84da590f867d54251a7ddf86c14ac746399a532c5
    2025/07/31 09:03:33  info unpack layer: sha256:ad5c1755b89eaabae7fe7d51630530b3c7bf34124de3cf4dd7aa93921eac9ce5
  ```

2. While adding the DNF repo configuration file, I noticed that the DNF config file `/etc/resolv.conf` was missing. Seem as [issues: 1906](https://github.com/warewulf/warewulf/issues/1906)

  env LANG=C.UTF-8 wwctl image exec --build=false warewulf-openeuler:24.03 -- /usr/bin/dnf config-manager --add-repo http://obs.openhpc.community:82/OpenHPC3:/3.3:/Factory/openEuler_22.03/

  ```
    Adding repo from: http://obs.openhpc.community:82/OpenHPC3:/3.3:/Factory/openEuler_22.03/
    + LANG=C
    + LC_CTYPE=C
    + export LANG LC_CTYPE
    + dnf clean all
    0 files removed
    + rm -f /etc/machine-id /var/lib/dbus/machine-id
  ```
  I copied the host machine's  **/etc/resolv.conf** file into the rootfs directory, and the repo was added successfully.

3. Upgrade node image 
  env LANG=C.UTF-8 wwctl image exec --build=false warewulf-openeuler:24.03  -- /usr/bin/dnf update -y
  ```
    .....
    Upgraded:
      glib2-2.78.3-9.oe2403sp2.aarch64      libgcc-12.3.1-97.oe2403sp2.aarch64       libgomp-12.3.1-97.oe2403sp2.aarch64      libssh-0.10.5-4.oe2403sp2.aarch64      libstdc++-12.3.1-97.oe2403sp2.aarch64      libxml2-2.11.9-6.oe2403sp2.aarch64        
      pam-1.5.3-9.oe2403sp2.aarch64         python3-3.11.6-13.oe2403sp2.aarch64      readline-8.2-5.oe2403sp2.aarch64         systemd-255-44.oe2403sp2.aarch64       systemd-libs-255-44.oe2403sp2.aarch64      systemd-udev-255-44.oe2403sp2.aarch64     
    
    Complete!
    ......
  ```

4. Rebuild the image
  wwctl image build warewulf-openeuler:24.03

  ```
    Building image: warewulf-openeuler:24.03
    Created image for Image warewulf-openeuler:24.03: /usr/local/var/warewulf/provision/images/warewulf-openeuler:24.03.img
    Compressed image for Image warewulf-openeuler:24.03: /usr/local/var/warewulf/provision/images/warewulf-openeuler:24.03.img.gz
  ```
