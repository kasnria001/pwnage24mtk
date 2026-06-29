# pwnage24mtk
Cert exploit for MTK devices.There is a logic flaw in MTK cert verification process.Similar to CVE-2023-20696.

For mtk devices,preloader verifies bl2_ext/lk/atf and then jumps to it.

These images is signed with ASN.1 certs.

But there is a logic flaw in the ASN.1 parsing process.

For old V5/V6 devices, cert content is identified by bypass_mode 1.

In this mode, any object will be stepped in.

So we can make a fake cert with 0x3(bit string) object.

The fake cert is unmodified. So it can pass the validation.

The real cert is under the fake cert.And We can put the image hash and image hdr hash at the beginning of the real cert.

For new V6 devices, cert content is identified by bypass_mode 0

In this mode, any object will be skipped except 0x30.

So we can put the image hash and image hdr hash at the beginning of the cert.

Then keep other things unmodified.

Then we can modify the image content.

So we gain EL3 control.



# Usage:

python parse-part-img.py [the original image file] --split -o out

Then you can see separate images (such as lk,bl2_ext,lk_main_dtb,aee)

python parse_preloader.py [preloader image]

You can get lk/bl2_ext load address via this script

after patching
For new v6 devices
python sign_mtk_cert.py [single image file] -w
For Old v6 devices/v5 devices
python sign_mtk_cert.py [single image file] -w --legacy

Then insert the new signed image back

python build-part-img.py replace [original image file] --name [the single image name you want to insert back] -- file [the new signed single image]


