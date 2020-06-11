# qemu-ppc64

Emulação com o QEMU 4.2.0 para PowerPC 64 bits.  

Host: Linux Ubuntu 5.4.0-33-generic #37-Ubuntu SMP x86_64 GNU/Linux

Guest:Linux version 4.9.0-12-powerpc64le  (gcc version 6.3.0 20170516 (Debian 6.3.0-18+deb9u1) ) #1 SMP Debian 4.9.210-1 


Baseado [nesse post para ARM](https://translatedcode.wordpress.com/2017/07/24/installing-debian-on-qemus-64-bit-arm-virt-board/)

Compilei o binário da versão 4.2.0 do site oficial do qemu.

## Instruções para emular


### create img:
```
qemu-img create -f qcow2 hda.qcow2 5G
```

### install:
Caso apareça um erro de permissão rode com o sudo.

```
~/qemu/qemu-4.2.0/ppc64-softmmu/qemu-system-ppc64 -M pseries -m 1G \
  -kernel vmlinux1 \
  -initrd initrd1.gz \
  -drive if=none,file=hda.qcow2,format=qcow2,id=hd \
  -device virtio-blk-pci,drive=hd \
  -netdev user,id=mynet \
  -device virtio-net-pci,netdev=mynet \
  -nographic -no-reboot -vga none
  ```

### Extracting kernel: 
Nessa etapa queremos os novos arquivos de kernel e initrd depois da instalação

Precisamos primeiro tornar o conteudo do /boot/ visivel no ubuntu:
```
sudo chmod 644 /boot/vmlinuz* 
```
para extrair o kernel:

(não consegui usar o próximo comando, como estava no site então copiei toda partição de boot e selecionei os arquivos initrd e vmlinuz com os números de versão, pois estes são os arquivos reais)
Você precisa achar esses arquivos certos para o PPC pois eu não conclui a instalação.
~~virt-copy-out -a hda.qcow2 /boot/vmlinuz-4.9.0-12-arm64 /boot/initrd.img-4.9.0-12-arm64 .~~
```
virt-copy-out -a hda.qcow2 /boot/ 
```

### boot:
```
~/qemu/qemu-4.2.0/ppc64-softmmu/qemu-system-ppc64 -M pseries -m 1G \
  -kernel vmlinuz-<alguma coisa> \
  -initrd initrd.img-<alguma coisa> \
  -append 'root=/dev/vda2' \
  -drive if=none,file=hda.qcow2,format=qcow2,id=hd \
  -device virtio-blk-pci,drive=hd \
  -netdev user,id=mynet \
  -device virtio-net-pci,netdev=mynet \
  -nographic -vga none
  ```
