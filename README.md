# pwnage24mtk
Cert exploit for MTK devices.There is a logic flaw in MTK cert verification process.Similar to CVE-2023-20696.
May be patched at June 

There are some issues about architecture detect.Need to be fixed when I am free.Or anyone can make a pull request.
This is a vibe-coded version,and i will rewrite them later.But now it is usable.
 
The date we discovered it is about one year ago(however i did not write a poc because of my lazy attitude.The poc was written at <img width="168" height="37" alt="image" src="https://github.com/user-attachments/assets/5bf68bc4-4a42-47ee-83c3-ef7d90b0314e" />

Look at what LittleNine is doing. On his channel, he's spreading false rumors and threatening to dox me, offering rewards to anyone who can find my account. Isn't this a tactic that goes beyond what the law allows? We reserve the right to pursue legal liability.
<img width="872" height="1920" alt="0d720a106a66bd1a82f06177e9b926ec_720" src="https://github.com/user-attachments/assets/20d6ffaa-4667-49b9-8fb3-8fca00b3f342" />

And his channel https://t.me/EnneaChitChat

Littlenine, MlgmXyysd, and Sherrin are not only rumor mongers but also opportunistic thieves. They took files that we inadvertently leaked, then went around claiming they had discovered those findings themselves. To make matters worse, they turned around and accused me of being the thief. Now they’re planning to capitalize on these—or similar—vulnerabilities by offering paid services.

When our recent GBL vulnerability overlapped with theirs, I reached out in good faith to discuss it, hoping to clarify the situation. Instead of a constructive conversation, I was met with baseless accusations and outright slander.

We do have other vulnerability research in the pipeline, but given this entire experience, we’re seriously reconsidering whether to continue publishing them at all.
<img width="1266" height="1207" alt="fc6bcf3894a432caf00ad3dc1bb878be_720" src="https://github.com/user-attachments/assets/6a95b328-3999-4d7f-a73d-3dfdfb23f8e4" />

<img width="841" height="294" alt="da7ff769c085f94c15a499f6892005d5" src="https://github.com/user-attachments/assets/728563fb-3e94-406b-a37e-a4b827dd282c" />

<img width="872" height="1920" alt="64a10fe17f15045f8ff9b3acb5371c49_720" src="https://github.com/user-attachments/assets/a999e602-f59a-4e8f-8dc5-4de172e259d7" />
<img width="872" height="1920" alt="9685bb71a65b19c68a3888e042252692_720" src="https://github.com/user-attachments/assets/fad8b0c7-1642-4b4d-b7f5-d232bd30e375" />
<img width="1216" height="1048" alt="a7467d2bd8e162978401d9b058d6af5e" src="https://github.com/user-attachments/assets/ed6c0843-9992-441d-98b2-6945f1f11710" />
<img width="872" height="1920" alt="6619dfb3e3f6f9e8b20903ae8571bcb6_720" src="https://github.com/user-attachments/assets/ee50f60a-700f-46c2-a5df-06b5b1e727b6" />
<img width="872" height="1920" alt="39059a044dbd254dbe8400eaaee74410_720" src="https://github.com/user-attachments/assets/775bcda0-a28e-4979-af4c-92400d8cff95" />
<img width="872" height="1920" alt="3e622231640c629aa667afbbae592308_720" src="https://github.com/user-attachments/assets/6a3b6439-8556-4353-b201-e77c12b9c21b" />
<img width="872" height="1920" alt="3aac1a9e5066cd89e35ce7e226a15f07_720" src="https://github.com/user-attachments/assets/37f99cf4-f120-4a83-8036-645429ec9d96" />

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

