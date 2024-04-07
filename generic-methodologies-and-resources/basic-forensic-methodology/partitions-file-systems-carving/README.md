# Partitions/File Systems/Carving

<details>

<summary><strong>htARTE (HackTricks AWS Red Team Expert)</strong> के साथ जीरो से हीरो तक AWS हैकिंग सीखें!</summary>

HackTricks का समर्थन करने के अन्य तरीके:

* अगर आप अपनी कंपनी का विज्ञापन **HackTricks** में देखना चाहते हैं या **HackTricks** को **PDF** में डाउनलोड करना चाहते हैं तो [**सब्सक्रिप्शन प्लान**](https://github.com/sponsors/carlospolop) देखें!
* [**आधिकारिक PEASS & HackTricks स्वैग**](https://peass.creator-spring.com) प्राप्त करें
* हमारे विशेष [**NFTs**](https://opensea.io/collection/the-peass-family) संग्रह **The PEASS Family** की खोज करें
* **जुड़ें** 💬 [**डिस्कॉर्ड समूह**](https://discord.gg/hRep4RUj7f) या [**टेलीग्राम समूह**](https://t.me/peass) से या हमें **ट्विटर** 🐦 [**@hacktricks\_live**](https://twitter.com/hacktricks\_live)\*\* पर फॉलो\*\* करें।
* **हैकिंग ट्रिक्स साझा करें और PRs सबमिट करें** [**HackTricks**](https://github.com/carlospolop/hacktricks) और [**HackTricks Cloud**](https://github.com/carlospolop/hacktricks-cloud) github repos में।

</details>

## विभाजन

एक हार्ड ड्राइव या एक **SSD डिस्क विभिन्न विभाजन** शामिल कर सकता है जिसका उद्देश्य डेटा को भौतिक रूप से अलग करना है।\
डिस्क की **न्यूनतम** इकाई सेक्टर है (सामान्य रूप से 512B से बना होता है)। इसलिए, प्रत्येक विभाजन का आकार उस आकार की गुणाकारी होनी चाहिए।

### MBR (मास्टर बूट रिकॉर्ड)

यह **डिस्क के पहले सेक्टर में आवंटित किया गया है 446B के बूट कोड के बाद**। इस सेक्टर का महत्वपूर्ण है क्योंकि यह PC को बताता है कि कौन सा और कहां से एक विभाजन माउंट किया जाना चाहिए।\
इसमें **4 विभाजन** तक की अनुमति है (अधिकतम **केवल 1** एक्टिव/**बूटेबल** हो सकता है)। हालांकि, अगर आपको अधिक विभाजन चाहिए तो आप **विस्तारित विभाजन** का उपयोग कर सकते हैं। इस पहले सेक्टर का अंतिम बाइट बूट रिकॉर्ड सिग्नेचर **0x55AA** है। केवल एक विभाजन को एक्टिव मार्क किया जा सकता है।\
MBR **अधिकतम 2.2TB** की अनुमति देता है।

![](<../../../.gitbook/assets/image (489).png>)

![](<../../../.gitbook/assets/image (490).png>)

MBR के **बाइट 440 से 443** तक आप **Windows Disk Signature** (अगर Windows का उपयोग किया गया है) पा सकते हैं। हार्ड डिस्क का तार्किक ड्राइव पत्र Windows Disk Signature पर निर्भर करता है। इस सिग्नेचर को बदलने से Windows को बूट करने से रोका जा सकता है (उपकरण: [**Active Disk Editor**](https://www.disk-editor.org/index.html)**)**।

![](<../../../.gitbook/assets/image (493).png>)

**स्वरूप**

| ऑफसेट       | लंबाई      | आइटम               |
| ----------- | ---------- | ------------------ |
| 0 (0x00)    | 446(0x1BE) | बूट कोड            |
| 446 (0x1BE) | 16 (0x10)  | पहला विभाजन        |
| 462 (0x1CE) | 16 (0x10)  | दूसरा विभाजन       |
| 478 (0x1DE) | 16 (0x10)  | तीसरा विभाजन       |
| 494 (0x1EE) | 16 (0x10)  | चौथा विभाजन        |
| 510 (0x1FE) | 2 (0x2)    | सिग्नेचर 0x55 0xAA |

**विभाजन रिकॉर्ड स्वरूप**

| ऑफसेट     | लंबाई    | आइटम                                                     |
| --------- | -------- | -------------------------------------------------------- |
| 0 (0x00)  | 1 (0x01) | एक्टिव फ्लैग (0x80 = बूटेबल)                             |
| 1 (0x01)  | 1 (0x01) | प्रारंभ हेड                                              |
| 2 (0x02)  | 1 (0x01) | प्रारंभ सेक्टर (बिट्स 0-5); सिलेंडर के ऊपरी बिट्स (6- 7) |
| 3 (0x03)  | 1 (0x01) | प्रारंभ सिलेंडर का निचला 8 बिट्स                         |
| 4 (0x04)  | 1 (0x01) | विभाजन प्रकार कोड (0x83 = लिनक्स)                        |
| 5 (0x05)  | 1 (0x01) | अंत हेड                                                  |
| 6 (0x06)  | 1 (0x01) | अंत सेक्टर (बिट्स 0-5); सिलेंडर के ऊपरी बिट्स (6- 7)     |
| 7 (0x07)  | 1 (0x01) | अंत सिलेंडर का निचला 8 बिट्स                             |
| 8 (0x08)  | 4 (0x04) | विभाजन से पहले के सेक्टर (लिटिल इंडियन)                  |
| 12 (0x0C) | 4 (0x04) | विभाजन में सेक्टर                                        |

Linux में MBR को माउंट करने के लिए पहले आपको स्टार्ट ऑफसेट प्राप्त करना होगा (आप `fdisk` और `p` कमांड का उपयोग कर सकते हैं)

![](https://github.com/carlospolop/hacktricks/blob/in/.gitbook/assets/image%20\(413\)%20\(3\)%20\(3\)%20\(3\)%20\(2\)%20\(1\)%20\(1\)%20\(1\)%20\(1\)%20\(1\)%20\(1\)%20\(1\)%20\(1\)%20\(1\)%20\(1\)%20\(1\)%20\(1\)%20\(1\)%20\(1\)%20\(1\)%20\(1\)%20\(1\)%20\(1\)%20\(1\)%20\(1\)%20\(1\)%20\(1\)%20\(1\)%20\(1\)%20\(1\)%20\(1\)%20\(1\)%20\(1\)%20\(1\)%20\(1\)%20\(1\)%20\(1\)%20\(1\)%20\(1\)%20\(1\)%20\(1\)%20\(1\)%20\(1\)%20\(1\)%20\(1\)%20\(1\)%20\(1\)%20\(12\).png)

और फिर निम्नलिखित कोड का उपयोग करें

```bash
#Mount MBR in Linux
mount -o ro,loop,offset=<Bytes>
#63x512 = 32256Bytes
mount -o ro,loop,offset=32256,noatime /path/to/image.dd /media/part/
```

**LBA (Logical block addressing)**

**लॉजिकल ब्लॉक पता** (**LBA**) एक सामान्य योजना है जो कंप्यूटर स्टोरेज उपकरणों पर डेटा ब्लॉक की स्थान सूचित करने के लिए उपयोग की जाती है, जनरली सेकेंडरी स्टोरेज सिस्टम्स जैसे हार्ड डिस्क ड्राइव्स। LBA एक विशेष रूप से सरल रैखिक पता योजना है; **ब्लॉक को एक पूर्णांक सूचक** द्वारा ढूंढा जाता है, पहला ब्लॉक LBA 0 होता है, दूसरा LBA 1 होता है, और ऐसा होता जाता है।

### GPT (GUID Partition Table)

GUID Partition Table, जिसे GPT के रूप में जाना जाता है, MBR (Master Boot Record) की तुलना में उन्नत क्षमताओं के लिए पसंद की जाती है। विभाजनों के लिए **वैश्विक अद्वितीय पहचानकर्ता** के लिए पहचानी जाने वाली GPT कई तरीकों में उभरती है:

* **स्थान और आकार**: GPT और MBR दोनों **सेक्टर 0** से शुरू होते हैं। हालांकि, GPT **64 बिट** पर काम करती है, जो MBR के 32 बिट के विपरीत है।
* **विभाजन सीमाएं**: GPT विंडोज सिस्टम्स पर **128 विभाजन** का समर्थन करती है और **9.4ZB** तक के डेटा को समर्थन करती है।
* **विभाजन नाम**: 36 यूनिकोड वर्णों के साथ विभाजनों को नाम देने की क्षमता प्रदान करती है।

**डेटा सहनशीलता और पुनर्प्राप्ति**:

* **पुनरावृत्ति**: MBR की तुलना में, GPT विभाजन और बूट डेटा को एक ही स्थान पर सीमित नहीं करती है। यह डेटा डिस्क पर फैलाती है, जिससे डेटा सत्यापन और सहनशीलता में सुधार होता है।
* **साइक्लिक रीडंडेंसी चेक (CRC)**: GPT डेटा सत्यापन के लिए CRC का उपयोग करती है। यह डेटा कोरप्शन के लिए सक्रिय रूप से मॉनिटर करती है, और जब इसे पता चलता है, GPT दूसरे डिस्क स्थान से कोरप्ट डेटा को पुनर्प्राप्त करने का प्रयास करती है।

**संरक्षक MBR (LBA0)**:

* GPT पुराने MBR परिसर में एक संरक्षक MBR के माध्यम से पिछली संगतता को बनाए रखती है। यह सुनिश्चित करने के लिए डिज़ाइन किया गया है कि पुराने MBR आधारित यूटिलिटी गलती से GPT डिस्क को ओवरराइट करने से बचें, इसलिए GPT-स्वरूपित डिस्क पर डेटा सत्यापन की सुरक्षा करता है।

![https://upload.wikimedia.org/wikipedia/commons/thumb/0/07/GUID\_Partition\_Table\_Scheme.svg/800px-GUID\_Partition\_Table\_Scheme.svg.png](<../../../.gitbook/assets/image (491).png>)

**हाइब्रिड MBR (LBA 0 + GPT)**

[Wikipedia से](https://en.wikipedia.org/wiki/GUID\_Partition\_Table)

जो ऑपरेटिंग सिस्टम GPT-आधारित बूट का समर्थन करते हैं बायोस के माध्यम से, पहला सेक्टर बूटलोडर कोड को स्टोर करने के लिए भी उपयोग किया जा सकता है, लेकिन **संशोधित** करके **GPT विभाजनों** को पहचानने के लिए। MBR में बूटलोडर 512 बाइट का सेक्टर आकार न माने।

**विभाजन सारणी हेडर (LBA 1)**

[Wikipedia से](https://en.wikipedia.org/wiki/GUID\_Partition\_Table)

विभाजन सारणी हेडर डिस्क पर उपयोग के योग्य ब्लॉक को परिभाषित करता है। यह विभाजन सारणी का हिस्सा बनाने वाले विभाजन प्रविष्टियों की संख्या और आकार को भी परिभाषित करता है (टेबल में ऑफसेट 80 और 84)।

| ऑफसेट     | लंबाई   | सामग्री                                                                                                                                                                      |
| --------- | ------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 0 (0x00)  | 8 बाइट  | हस्ताक्षर ("EFI PART", 45h 46h 49h 20h 50h 41h 52h 54h या 0x5452415020494645ULL[ ](https://en.wikipedia.org/wiki/GUID\_Partition\_Table#cite\_note-8)लिटिल-एंडियन मशीनों पर) |
| 8 (0x08)  | 4 बाइट  | संशोधन 1.0 (00h 00h 01h 00h) यूईएफआई 2.8 के लिए                                                                                                                              |
| 12 (0x0C) | 4 बाइट  | हेडर आकार लिटिल एंडियन में (बाइट में, सामान्यत: 5Ch 00h 00h 00h या 92 बाइट)                                                                                                  |
| 16 (0x10) | 4 बाइट  | [CRC32](https://en.wikipedia.org/wiki/CRC32) हेडर का (ऑफसेट +0 से हेडर आकार तक) हेडर में लिटिल एंडियन में, जिसमें इस क्षेत्र को गणना के दौरान शून्य किया जाता है             |
| 20 (0x14) | 4 बाइट  | आरक्षित; शून्य होना चाहिए                                                                                                                                                    |
| 24 (0x18) | 8 बाइट  | वर्तमान LBA (इस हेडर कॉपी की स्थान)                                                                                                                                          |
| 32 (0x20) | 8 बाइट  | बैकअप LBA (दूसरे हेडर की कॉपी की स्थान)                                                                                                                                      |
| 40 (0x28) | 8 बाइट  | विभाजनों के लिए पहला उपयोगी LBA (प्राथमिक विभाजन सारणी का अंतिम LBA + 1)                                                                                                     |
| 48 (0x30) | 8 बाइट  | अंतिम उपयोगी LBA (सेकेंडरी विभाजन सारणी का पहला LBA − 1)                                                                                                                     |
| 56 (0x38) | 16 बाइट | डिस्क GUID मिक्स्ड एंडियन में                                                                                                                                                |
| 72 (0x48) | 8 बाइट  | पार्टीशन प्रविष्टियों के एक एरे की प्रारंभिक LBA (हमेशा 2 प्राथमिक कॉपी में)                                                                                                 |
| 80 (0x50) | 4 बाइट  | एरे में पार्टीशन प्रविष्टियों की संख्या                                                                                                                                      |
| 84 (0x54) | 4 बाइट  | एकल पार्टीशन प्रविष्टि का आकार (सामान्यत: 80h या 128)                                                                                                                        |
| 88 (0x58) | 4 बाइट  | पार्टीशन प्रविष्टियों के एरे का CRC32 लिटिल एंडियन में                                                                                                                       |
| 92 (0x5C) | \*      | आरक्षित; बाकी ब्लॉक के लिए शून्य होना चाहिए (512 बाइट के सेक्टर आकार के लिए 420 बाइट; लेकिन अधिक बड़े सेक्टर आकार के साथ अधिक हो सकता है)                                    |

**पार्टीशन प्रविष्टियाँ (LBA 2–33)**

| GUID पार्टीशन प्रविष्टि प्रारूप |         |                                                                                                                    |
| ------------------------------- | ------- | ------------------------------------------------------------------------------------------------------------------ |
| ऑफसेट                           | लंबाई   | सामग्री                                                                                                            |
| 0 (0x00)                        | 16 बाइट | [विभाजन प्रकार GUID](https://en.wikipedia.org/wiki/GUID\_Partition\_Table#Partition\_type\_GUIDs) (मिक्स्ड एंडियन) |
| 16 (0x10)                       | 16 बाइट | अद्वितीय विभाजन GUID (मिक्स्ड एंडियन)                                                                              |
| 32 (0x20)                       | 8 बाइट  | पहला LBA ([लिटिल एंडियन](https://en.wikipedia.org/wiki/Little\_endian))                                            |
| 40 (0x28)                       | 8 बाइट  | अंतिम LBA (समावेशी, सामान्यत: विषम)                                                                                |
| 48 (0x30)                       | 8 बाइट  | विशेषता ध्वज (उदाहरण के लिए बिट 60 पढ़ने योग्यता की निशानी है)                                                     |
| 56 (0x38)                       | 72 बाइट | विभाजन नाम (36 [UTF-16](https://en.wikipedia.org/wiki/UTF-16)LE कोड इकाइयों)                                       |

**विभाजन प्रकार**

![](<../../../.gitbook/assets/image (492).png>)

अधिक विभाजन प्रकार [https://en.wikipedia.org/wiki/GUID\_Partition\_Table](https://en.wikipedia.org/wiki/GUID\_Partition\_Table) में

### जांच

[**ArsenalImageMounter**](https://arsenalrecon.com/downloads/) के साथ फोरेंसिक्स इमेज को माउंट करने के बाद, आप [**Active Disk Editor**](https://www.disk-editor.org/index.html) विंडोज टूल का उपयोग करके पहले सेक्टर की जांच कर सकते हैं। निम्नलिखित छवि में एक **MBR** को **सेक्टर 0** पर पहचाना गया थ

## फ़ाइल-सिस्टम

### Windows फ़ाइल-सिस्टम सूची

* **FAT12/16**: MSDOS, WIN95/98/NT/200
* **FAT32**: 95/2000/XP/2003/VISTA/7/8/10
* **ExFAT**: 2008/2012/2016/VISTA/7/8/10
* **NTFS**: XP/2003/2008/2012/VISTA/7/8/10
* **ReFS**: 2012/2016

### FAT

**FAT (File Allocation Table)** फ़ाइल सिस्टम अपने मूल घटक के आसपास डिज़ाइन किया गया है, जो वॉल्यूम की शुरुआत पर स्थित फ़ाइल आवंटन टेबल के चारों ओर है। यह सिस्टम डेटा की सुरक्षा को दो नकलें बनाकर रखता है, जिससे यदि एक कोरप्ट हो जाए तो भी डेटा अखंड रहता है। टेबल, साथ ही रूट फ़ोल्डर, एक **निश्चित स्थान** पर होना चाहिए, जो सिस्टम की स्टार्टअप प्रक्रिया के लिए महत्वपूर्ण है।

फ़ाइल सिस्टम की मूल भंडारण इकाई एक **क्लस्टर, सामान्यत: 512B** होती है, जिसमें कई सेक्टर होते हैं। FAT कई संस्करणों के माध्यम से विकसित हुआ है:

* **FAT12**, 12-बिट क्लस्टर पतों का समर्थन करता है और 4078 क्लस्टर (UNIX के साथ 4084) तक का संचालन करता है।
* **FAT16**, 16-बिट पतों के साथ उन्नत होकर, इसलिए 65,517 क्लस्टर तक का समर्थन करता है।
* **FAT32**, 32-बिट पतों के साथ और भी उन्नत होकर, जो एक शानदार 268,435,456 क्लस्टर प्रति वॉल्यूम की अनुमति देता है।

FAT संस्करणों के लिए एक महत्वपूर्ण सीमा है **4GB अधिकतम फ़ाइल आकार**, जो फ़ाइल आकार भंडारण के लिए उपयोग किए गए 32-बिट फ़ील्ड द्वारा लागू किया गया है।

मूल निर्देशिका के महत्वपूर्ण घटक, विशेष रूप से FAT12 और FAT16 के लिए, निम्नलिखित हैं:

* **फ़ाइल/फ़ोल्डर नाम** (अधिकतम 8 वर्ण)
* **गुण**
* **निर्माण, संशोधन और अंतिम पहुंच तिथियाँ**
* **FAT टेबल पता** (फ़ाइल के प्रारंभ क्लस्टर को दर्शाता है)
* **फ़ाइल आकार**

### EXT

**Ext2** वह सबसे सामान्य फ़ाइल सिस्टम है जो **जर्नलिंग नहीं** करता है (**वे विभाजन जो बहुत बदलते नहीं हैं**) जैसे बूट विभाजन के लिए। **Ext3/4** **जर्नलिंग** हैं और सामान्यत: **शेष विभाजनों** के लिए उपयोग किए जाते हैं।