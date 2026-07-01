# pwnage24mtk
Cert exploit for MTK devices.There is a logic flaw in MTK cert verification process.Similar to CVE-2023-20696.
May be patched at June 

There are some issues about architecture detect.Need to be fixed when I am free.Or anyone can make a pull request.
This is a vibe-coded version,and i will rewrite them later.But now it is usable.
 


# Disclaimer
This project is only used for researching.
Make sure you get authorized before you use it.
Not allowed for illegal use.
It is not allowed to use this exploit to provide paid services.


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

You need to patch BL2_EXT to remove the vefification of lk or atf.

And then patch LK to remove the verification of lk_main_dtb(otherwise it will crash)

And you need to patch LK to remove all bootloader locks including set_lock_state to avoid locking back or some crashes.



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

# Example:

<img width="1916" height="850" alt="4c8558aaea5e8d939ba978d6b10be14d" src="https://github.com/user-attachments/assets/eff7645a-9da2-4dfc-8f29-3ec9a727e208" />
<img width="1665" height="462" alt="17fe099c27560ec4b0dd0ab1540e1442" src="https://github.com/user-attachments/assets/c673c332-848f-436e-a024-1676982ceced" />
<img width="1002" height="971" alt="b56f0026951f2c58612b315b44423437" src="https://github.com/user-attachments/assets/577449d1-384b-4a47-8509-aebb70bab04e" />
<img width="1325" height="233" alt="image" src="https://github.com/user-attachments/assets/68871910-a551-4a53-b64f-b0d0e2312ff3" />

