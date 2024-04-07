# Stego Tricks

<details>

<summary><strong>जानें AWS हैकिंग को शून्य से हीरो तक</strong> <a href="https://training.hacktricks.xyz/courses/arte"><strong>htARTE (HackTricks AWS Red Team Expert)</strong></a><strong> के साथ!</strong></summary>

दूसरे तरीके HackTricks का समर्थन करने के लिए:

* यदि आप अपनी **कंपनी का विज्ञापन HackTricks में देखना चाहते हैं** या **HackTricks को PDF में डाउनलोड करना चाहते हैं** तो [**सब्सक्रिप्शन प्लान्स देखें**](https://github.com/sponsors/carlospolop)!
* [**आधिकारिक PEASS & HackTricks स्वैग**](https://peass.creator-spring.com) प्राप्त करें
* हमारे विशेष [**NFTs**](https://opensea.io/collection/the-peass-family) कलेक्शन, [**The PEASS Family**](https://opensea.io/collection/the-peass-family) खोजें
* **शामिल हों** 💬 [**Discord समूह**](https://discord.gg/hRep4RUj7f) या [**टेलीग्राम समूह**](https://t.me/peass) या हमें **ट्विटर** 🐦 [**@carlospolopm**](https://twitter.com/hacktricks\_live)** पर फॉलो** करें।
* **अपने हैकिंग ट्रिक्स साझा करें, PRs सबमिट करके** [**HackTricks**](https://github.com/carlospolop/hacktricks) और [**HackTricks Cloud**](https://github.com/carlospolop/hacktricks-cloud) github repos में।

</details>

**Try Hard Security Group**

<figure><img src="../.gitbook/assets/telegram-cloud-document-1-5159108904864449420.jpg" alt=""><figcaption></figcaption></figure>

{% embed url="https://discord.gg/tryhardsecurity" %}

***

## **फ़ाइलों से डेटा निकालना**

### **Binwalk**

एक टूल जो एम्बेडेड छिपे फ़ाइलों और डेटा के लिए बाइनरी फ़ाइलों की खोज के लिए है। यह `apt` के माध्यम से स्थापित किया जाता है और इसका स्रोत [GitHub](https://github.com/ReFirmLabs/binwalk) पर उपलब्ध है।
```bash
binwalk file # Displays the embedded data
binwalk -e file # Extracts the data
binwalk --dd ".*" file # Extracts all data
```
### **प्रमुख**

यह फ़ाइलों को उनके हेडर और फ़ुटर के आधार पर पुनः प्राप्त करता है, png छवियों के लिए उपयोगी। `apt` के माध्यम से स्थापित किया जाता है और इसका स्रोत [GitHub](https://github.com/korczis/foremost) पर उपलब्ध है।
```bash
foremost -i file # Extracts data
```
### **Exiftool**

फ़ाइल मेटाडेटा देखने में मदद करता है, यहाँ उपलब्ध [है](https://www.sno.phy.queensu.ca/\~phil/exiftool/).
```bash
exiftool file # Shows the metadata
```
### **Exiv2**

एक्सिफटूल के समान, मेटाडेटा देखने के लिए। `apt` के माध्यम से इंस्टॉल किया जा सकता है, [GitHub](https://github.com/Exiv2/exiv2) पर स्रोत है, और एक [आधिकारिक वेबसाइट](http://www.exiv2.org/) है।
```bash
exiv2 file # Shows the metadata
```
### **फ़ाइल**

यह पता लगाएं कि आपके पास किस प्रकार की फ़ाइल है।

### **स्ट्रिंग्स**

फ़ाइलों से पठनीय स्ट्रिंग्स निकालें, विभिन्न इन्कोडिंग सेटिंग का उपयोग करके आउटपुट को फ़िल्टर करने के लिए।
```bash
strings -n 6 file # Extracts strings with a minimum length of 6
strings -n 6 file | head -n 20 # First 20 strings
strings -n 6 file | tail -n 20 # Last 20 strings
strings -e s -n 6 file # 7bit strings
strings -e S -n 6 file # 8bit strings
strings -e l -n 6 file # 16bit strings (little-endian)
strings -e b -n 6 file # 16bit strings (big-endian)
strings -e L -n 6 file # 32bit strings (little-endian)
strings -e B -n 6 file # 32bit strings (big-endian)
```
### **तुलना (cmp)**

एक संशोधित फ़ाइल को उसके मूल संस्करण के साथ तुलना करने के लिए उपयोगी है जो ऑनलाइन मिलता है।
```bash
cmp original.jpg stego.jpg -b -l
```
## **पाठ में छिपी जानकारी निकालना**

### **अंतरिक्ष में छिपी जानकारी**

दिखाई न देने वाले जगहों में अदृश्य वर्णमालाएं जानकारी छुपा सकती है। इस डेटा को निकालने के लिए, [https://www.irongeek.com/i.php?page=security/unicode-steganography-homoglyph-encoder](https://www.irongeek.com/i.php?page=security/unicode-steganography-homoglyph-encoder) पर जाएं।

## **छवियों से डेटा निकालना**

### **ग्राफिकमैजिक के साथ छवि विवरणों की पहचान**

[ग्राफिकमैजिक](https://imagemagick.org/script/download.php) छवि फ़ाइल के प्रकार निर्धारित करने और संभावित क्षति की पहचान करने के लिए सेवित करता है। निम्नलिखित कमांड को निष्पादित करें एक छवि की जांच करने के लिए:
```bash
./magick identify -verbose stego.jpg
```
एक क्षतिग्रस्त छवि पर मरम्मत करने का प्रयास करने के लिए, मेटाडेटा टिप्पणी जोड़ना मददगार साबित हो सकता है:
```bash
./magick mogrify -set comment 'Extraneous bytes removed' stego.jpg
```
### **डेटा छुपाने के लिए Steghide**

Steghide `JPEG, BMP, WAV, और AU` फ़ाइलों में डेटा छुपाने की सुविधा प्रदान करता है, जिसमें एन्क्रिप्टेड डेटा को संबोधित और निकाला जा सकता है। स्थापना `apt` का उपयोग करके सीधी है, और इसका [स्रोत कोड GitHub पर उपलब्ध है](https://github.com/StefanoDeVuono/steghide)।

**कमांड:**

* `steghide info file` यह प्रकट करता है कि क्या एक फ़ाइल में छुपा डेटा है।
* `steghide extract -sf file [--passphrase password]` छुपा हुआ डेटा निकालता है, पासवर्ड वैकल्पिक है।

वेब-आधारित निकालने के लिए, [इस वेबसाइट पर जाएं](https://futureboy.us/stegano/decinput.html)।

**Stegcracker के साथ ब्रूटफ़ोर्स हमला:**

* Steghide पर पासवर्ड क्रैकिंग का प्रयास करने के लिए, निम्नलिखित रूप में [stegcracker](https://github.com/Paradoxis/StegCracker.git) का उपयोग करें:
```bash
stegcracker <file> [<wordlist>]
```
### **zsteg के लिए PNG और BMP फ़ाइलें**

zsteg PNG और BMP फ़ाइलों में छिपी डेटा का पता लगाने में विशेषज्ञ है। स्थापना `gem install zsteg` के माध्यम से की जाती है, जिसका [GitHub पर स्रोत](https://github.com/zed-0xff/zsteg) है।

**कमांड:**

* `zsteg -a फ़ाइल` फ़ाइल पर सभी पहचान विधियों को लागू करता है।
* `zsteg -E फ़ाइल` डेटा निकालने के लिए एक payload निर्दिष्ट करता है।

### **StegoVeritas और Stegsolve**

**stegoVeritas** मेटाडेटा की जांच करता है, छवि परिवर्तन करता है, और अन्य सुविधाओं के बीच LSB brute forcing को लागू करता है। सभी विकल्पों की पूरी सूची के लिए `stegoveritas.py -h` का उपयोग करें और सभी जांचों को क्रियान्वित करने के लिए `stegoveritas.py stego.jpg` का उपयोग करें।

**Stegsolve** विभिन्न रंग फ़िल्टर लागू करता है ताकि छवियों में छिपी पाठ या संदेशों को प्रकट कर सकें। यह [GitHub पर](https://github.com/eugenekolo/sec-tools/tree/master/stego/stegsolve/stegsolve) उपलब्ध है।

### **छिपी सामग्री के लिए FFT**

फास्ट फ़ूरियर परिवर्तन (FFT) तकनीक छवियों में छिपी सामग्री को प्रकट कर सकती है। उपयोगी संसाधन शामिल हैं:

* [EPFL डेमो](http://bigwww.epfl.ch/demo/ip/demos/FFT/)
* [Ejectamenta](https://www.ejectamenta.com/Fourifier-fullscreen/)
* [GitHub पर FFTStegPic](https://github.com/0xcomposure/FFTStegPic)

### **ऑडियो और छवि फ़ाइलों के लिए Stegpy**

Stegpy छवि और ऑडियो फ़ाइलों में जानकारी को समाहित करने की अनुमति देता है, PNG, BMP, GIF, WebP, और WAV जैसे प्रारूपों का समर्थन करता है। यह [GitHub पर](https://github.com/dhsdshdhk/stegpy) उपलब्ध है।

### **PNG फ़ाइल विश्लेषण के लिए Pngcheck**

PNG फ़ाइलों का विश्लेषण करने या उनकी प्रामाणिकता की पुष्टि करने के लिए, इस्तेमाल करें:
```bash
apt-get install pngcheck
pngcheck stego.png
```
### **छवि विश्लेषण के लिए अतिरिक्त उपकरण**

और अध्ययन के लिए, निम्नलिखित पर जाएं:

* [Magic Eye Solver](http://magiceye.ecksdee.co.uk/)
* [Image Error Level Analysis](https://29a.ch/sandbox/2012/imageerrorlevelanalysis/)
* [Outguess](https://github.com/resurrecting-open-source-projects/outguess)
* [OpenStego](https://www.openstego.com/)
* [DIIT](https://diit.sourceforge.net/)

## **ऑडियो से डेटा निकालना**

**ऑडियो स्टेगनोग्राफी** ध्वनि फ़ाइलों में जानकारी छुपाने के लिए एक अद्वितीय तकनीक प्रदान करती है। छुपी हुई सामग्री को समाहित करने या पुनः प्राप्त करने के लिए विभिन्न उपकरणों का उपयोग किया जाता है।

### **Steghide (JPEG, BMP, WAV, AU)**

Steghide एक बहुमुखी उपकरण है जो डेटा को JPEG, BMP, WAV, और AU फ़ाइलों में छुपाने के लिए डिज़ाइन किया गया है। विस्तृत निर्देश [stego tricks documentation](stego-tricks.md#steghide) में उपलब्ध हैं।

### **Stegpy (PNG, BMP, GIF, WebP, WAV)**

यह उपकरण PNG, BMP, GIF, WebP, और WAV जैसे विभिन्न प्रारूपों के साथ संगत है। अधिक जानकारी के लिए, [Stegpy's section](stego-tricks.md#stegpy-png-bmp-gif-webp-wav) का संदर्भ दें।

### **ffmpeg**

ffmpeg ऑडियो फ़ाइलों की अखंडता का मूल्यांकन करने के लिए महत्वपूर्ण है, विस्तृत जानकारी को हाइलाइट करने और किसी भी अंतर्विरोध को पहचानने के लिए।
```bash
ffmpeg -v info -i stego.mp3 -f null -
```
### **WavSteg (WAV)**

WavSteg WAV files में डेटा छुपाने और निकालने में उत्कृष्ट है जो कम महत्वपूर्ण बिट रणनीति का उपयोग करता है। यह [GitHub](https://github.com/ragibson/Steganography#WavSteg) पर उपलब्ध है। आज्ञाएँ शामिल हैं:
```bash
python3 WavSteg.py -r -b 1 -s soundfile -o outputfile

python3 WavSteg.py -r -b 2 -s soundfile -o outputfile
```
### **डीपसाउंड**

डीपसाउंड AES-256 का उपयोग करके ध्वनि फ़ाइलों में जानकारी का एन्क्रिप्शन और पहचान करने की अनुमति देता है। इसे [आधिकारिक पृष्ठ](http://jpinsoft.net/deepsound/download.aspx) से डाउनलोड किया जा सकता है।

### **सॉनिक विज़ुअलाइज़र**

ऑडियो फ़ाइलों की दृश्यात्मक और विश्लेषणात्मक जांच के लिए एक अमूल्य उपकरण, सॉनिक विज़ुअलाइज़र अन्य साधनों द्वारा अनुसंधान न किए जा सकने वाले छुपे तत्वों को उजागर कर सकता है। अधिक जानकारी के लिए [आधिकारिक वेबसाइट](https://www.sonicvisualiser.org/) पर जाएं।

### **डीटीएमएफ़ टोन्स - डायल टोन्स**

ऑडियो फ़ाइलों में डीटीएमएफ़ टोन्स का पता लगाना ऑनलाइन उपकरणों के माध्यम से संभव है, जैसे कि [इस DTMF डिटेक्टर](https://unframework.github.io/dtmf-detect/) और [डायलएबीसी](http://dialabc.com/sound/detect/index.html)।

## **अन्य तकनीकें**

### **बाइनरी लेंथ एसक्यूआरटी - क्यूआर कोड**

पूर्ण संख्या में वर्ग करने वाला बाइनरी डेटा एक क्यूआर कोड को प्रतिनिधित कर सकता है। इसे जांचने के लिए इस स्निपेट का उपयोग करें:
```python
import math
math.sqrt(2500) #50
```
### **ब्रेल अनुवाद**

ब्रेल का अनुवाद करने के लिए, [ब्रानाह ब्रेल अनुवादक](https://www.branah.com/braille-translator) एक उत्कृष्ट स्रोत है।

## **संदर्भ**

* [**https://0xrick.github.io/lists/stego/**](https://0xrick.github.io/lists/stego/)
* [**https://github.com/DominicBreuker/stego-toolkit**](https://github.com/DominicBreuker/stego-toolkit)

**ट्राई हार्ड सिक्योरिटी ग्रुप**

<figure><img src="../.gitbook/assets/telegram-cloud-document-1-5159108904864449420.jpg" alt=""><figcaption></figcaption></figure>

{% embed url="https://discord.gg/tryhardsecurity" %}

<details>

<summary><strong>जानें AWS हैकिंग को शून्य से हीरो तक</strong> <a href="https://training.hacktricks.xyz/courses/arte"><strong>htARTE (HackTricks AWS Red Team Expert)</strong></a><strong>!</strong></summary>

HackTricks का समर्थन करने के अन्य तरीके:

* यदि आप अपनी **कंपनी का विज्ञापन HackTricks में देखना चाहते हैं** या **HackTricks को PDF में डाउनलोड करना चाहते हैं** तो [**सब्सक्रिप्शन प्लान्स देखें**](https://github.com/sponsors/carlospolop)!
* [**आधिकारिक PEASS और HackTricks स्वैग**](https://peass.creator-spring.com) प्राप्त करें
* हमारे विशेष [**NFTs**](https://opensea.io/collection/the-peass-family) कलेक्शन, [**The PEASS Family**](https://opensea.io/collection/the-peass-family) खोजें
* **शामिल हों** 💬 [**डिस्कॉर्ड ग्रुप**](https://discord.gg/hRep4RUj7f) या [**टेलीग्राम ग्रुप**](https://t.me/peass) या हमें **ट्विटर** 🐦 [**@carlospolopm**](https://twitter.com/hacktricks\_live)** पर फॉलो** करें।
* **हैकिंग ट्रिक्स साझा करें, HackTricks और HackTricks Cloud** github रेपो में PR जमा करके।

</details>