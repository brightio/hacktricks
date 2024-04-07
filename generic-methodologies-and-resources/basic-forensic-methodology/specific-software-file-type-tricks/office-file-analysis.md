# कार्यालय फ़ाइल विश्लेषण

<details>

<summary><strong>htARTE (HackTricks AWS Red Team Expert)</strong> के साथ जीरो से हीरो तक AWS हैकिंग सीखें!</summary>

HackTricks का समर्थन करने के अन्य तरीके:

* यदि आप अपनी कंपनी का विज्ञापन HackTricks में देखना चाहते हैं या HackTricks को PDF में डाउनलोड करना चाहते हैं तो [**सब्सक्रिप्शन प्लान**](https://github.com/sponsors/carlospolop) देखें!
* [**आधिकारिक PEASS & HackTricks स्वैग**](https://peass.creator-spring.com) प्राप्त करें
* हमारे विशेष [**NFTs**](https://opensea.io/collection/the-peass-family) कलेक्शन, **The PEASS Family** की खोज करें
* **जुड़ें** 💬 [**डिस्कॉर्ड समूह**](https://discord.gg/hRep4RUj7f) या [**टेलीग्राम समूह**](https://t.me/peass) से या हमें **ट्विटर** 🐦 [**@hacktricks\_live**](https://twitter.com/hacktricks\_live) **पर फॉलो** करें।
* **अपने हैकिंग ट्रिक्स साझा करें, HackTricks** और **HackTricks Cloud** github repos में PRs सबमिट करके।

</details>

<figure><img src="../../../.gitbook/assets/image (3) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

\
[**Trickest**](https://trickest.com/?utm\_campaign=hacktrics\&utm\_medium=banner\&utm\_source=hacktricks) का उपयोग करें और दुनिया के सबसे उन्नत समुदाय उपकरणों द्वारा संचालित **वर्कफ़्लो** आसानी से बनाएं और स्वचालित करें।\
आज ही पहुंच प्राप्त करें:

{% embed url="https://trickest.com/?utm_campaign=hacktrics&utm_medium=banner&utm_source=hacktricks" %}

अधिक जानकारी के लिए [https://trailofbits.github.io/ctf/forensics/](https://trailofbits.github.io/ctf/forensics/) देखें। यह केवल एक सारांश है:

माइक्रोसॉफ्ट ने कई ऑफिस दस्तावेज़ प्रारूप बनाए हैं, जिनमें दो मुख्य प्रकार के हैं **OLE प्रारूप** (जैसे RTF, DOC, XLS, PPT) और **Office Open XML (OOXML) प्रारूप** (जैसे DOCX, XLSX, PPTX)। इन प्रारूपों में मैक्रो शामिल हो सकते हैं, जिन्हें फिशिंग और मैलवेयर के लिए लक्ष्य बनाया जा सकता है। OOXML फ़ाइलें ज़िप कंटेनर के रूप में संरचित होती हैं, जिससे अनज़िप करके फ़ाइल और फ़ोल्डर व्यवस्था और XML फ़ाइल की सामग्री का पता लगाया जा सकता है।

OOXML फ़ाइल संरचनाओं की खोज के लिए एक दस्तावेज़ को अनज़िप करने के लिए एक कमांड और आउटपुट संरचना दी गई है। इन फ़ाइलों में डेटा छुपाने के तकनीकों का विवरण दस्तावेज़ किया गया है, जिससे CTF चुनौतियों में डेटा छुपाने में जारी नवाचार का संकेत मिलता है।

विश्लेषण के लिए, **oletools** और **OfficeDissector** OLE और OOXML दस्तावेज़ की जांच के लिए व्यापक उपकरण सेट प्रदान करते हैं। ये उपकरण निहित मैक्रो की पहचान और विश्लेषण में मदद करते हैं, जो अक्सर मैलवेयर वितरण के लिए वेक्टर के रूप में काम करते हैं, आम तौर पर और जानकारी डाउनलोड और अतिरिक्त हानिकारक पेलोड्स को निष्पादित करने के लिए। VBA मैक्रो का विश्लेषण माइक्रोसॉफ्ट ऑफिस के बिना लाइब्रे ऑफिस का उपयोग करके किया जा सकता है, जिसमें ब्रेकपॉइंट्स और वॉच वेरिएबल्स के साथ डीबगिंग की अनुमति होती है।

**oletools** की स्थापना और उपयोग सरल है, जिसमें pip के माध्यम से स्थापना के लिए आदेश प्रदान किए गए हैं और दस्तावेज़ से मैक्रो निकालने के लिए। मैक्रो का स्वचालित निष्पादन `AutoOpen`, `AutoExec`, या `Document_Open` जैसे फ़ंक्शन्स द्वारा प्रेरित किया जाता है।
```bash
sudo pip3 install -U oletools
olevba -c /path/to/document #Extract macros
```
<figure><img src="../../../.gitbook/assets/image (3) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

\
[**Trickest**](https://trickest.com/?utm_campaign=hacktrics&utm_medium=banner&utm_source=hacktricks) का उपयोग करें और आसानी से **दुनिया के सबसे उन्नत** समुदाय उपकरणों द्वारा संचालित **कार्यप्रणालियों** को बनाएं और स्वचालित करें।\
आज ही पहुंचें:

{% embed url="https://trickest.com/?utm_campaign=hacktrics&utm_medium=banner&utm_source=hacktricks" %}

<details>

<summary><strong>शून्य से हीरो तक AWS हैकिंग सीखें</strong> <a href="https://training.hacktricks.xyz/courses/arte"><strong>htARTE (HackTricks AWS Red Team Expert)</strong></a><strong>!</strong></summary>

HackTricks का समर्थन करने के अन्य तरीके:

* यदि आप अपनी कंपनी का **विज्ञापन HackTricks में देखना चाहते हैं** या **HackTricks को PDF में डाउनलोड करना चाहते हैं** तो [**सदस्यता योजनाएं देखें**](https://github.com/sponsors/carlospolop)!
* [**आधिकारिक PEASS & HackTricks स्वैग**](https://peass.creator-spring.com) प्राप्त करें
* हमारे विशेष [**NFTs**](https://opensea.io/collection/the-peass-family) कलेक्शन, [**The PEASS Family**](https://opensea.io/collection/the-peass-family) खोजें
* **शामिल हों** 💬 [**Discord समूह**](https://discord.gg/hRep4RUj7f) या [**टेलीग्राम समूह**](https://t.me/peass) या हमें **ट्विटर** 🐦 [**@hacktricks_live**](https://twitter.com/hacktricks_live)** पर फॉलो** करें।
* **हैकिंग ट्रिक्स साझा करें, HackTricks** और [**HackTricks Cloud**](https://github.com/carlospolop/hacktricks-cloud) github repos में PRs सबमिट करके।

</details>