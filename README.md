# Guvenli
Güven
import secrets
import string

def generate_security_code(length=6):
    # Büyük harf, küçük harf ve rakam havuzu oluşturuyoruz
    characters = string.ascii_letters + string.digits
    
    # Güvenli bir şekilde rastgele seçim yapıyoruz
    security_code = ''.join(secrets.choice(characters) for _ in range(length))
    
    return security_code

# Kullanım:
print(f"Güvenlik Kodunuz: {generate_security_code(8)}")
// React Native Örneği - Mantıksal Akış
const verifyCode = async (enteredCode) => {
  try {
    const response = await fetch('https://api.seninsiten.com/verify', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ 
        code: enteredCode, 
        userId: currentUser.id 
      }),
    });
    
    const result = await response.json();
    
    if (result.success) {
      // Başarılıysa ana sayfaya yönlendir
      navigation.navigate('Home');
    } else {
      Alert.alert("Hata", "Girdiğiniz kod hatalı veya süresi dolmuş.");
    }
  } catch (error) {
    console.error("Bağlantı hatası:", error);
  }
};
// Her kutu için ayrı bir state ve referans kullanarak 
// kullanıcı bir rakam girdiğinde otomatik sonraki kutuya odaklanır.
const OtpInput = () => {
  return (
    <View style={{ flexDirection: 'row', justifyContent: 'space-between' }}>
      {[0, 1, 2, 3, 4, 5].map((index) => (
        <TextInput
          key={index}
          style={styles.otpBox}
          keyboardType="number-pad"
          maxLength={1}
          onChangeText={(text) => handleNextStep(text, index)}
          ref={(ref) => inputs[index] = ref}
        />
      ))}
    </View>
  );
};
const sendOTP = async (phoneNumber) => {
  const code = Math.floor(100000 + Math.random() * 900000); // 6 haneli kod
  // Veritabanına kaydet (5 dakika süreli)
  await db.saveCode({ phoneNumber, code, expires: Date.now() + 300000 });
  
  // SMS Servisini tetikle
  await smsClient.messages.create({
    body: `Güvenlik kodunuz: ${code}. Kimseyle paylaşmayın.`,
    to: phoneNumber,
    from: '+123456789'
  });
};
import * as LocalAuthentication from 'expo-local-authentication';

const authenticate = async () => {
  const hasHardware = await LocalAuthentication.hasHardwareAsync();
  if (hasHardware) {
    const result = await LocalAuthentication.authenticateAsync({
      promptMessage: 'Verilere erişmek için kimliğinizi doğrulayın',
      fallbackLabel: 'Şifre Gir',
    });
    
    if (result.success) {
      // Hassas verileri Keystore'dan çek ve göster
      showPrivateData();
    }
  }
};
import * as SecureStore from 'expo-secure-store';

// 1. Veriyi Şifreli Sakla
async function saveSensitiveData(key, value) {
  try {
    await SecureStore.setItemAsync(key, value, {
      keychainAccessible: SecureStore.WHEN_PASSCODE_SET_THIS_DEVICE_ONLY,
    });
    console.log("Veri güvenli bir şekilde mühürlendi.");
  } catch (error) {
    console.error("Saklama hatası:", error);
  }
}

// 2. Veriyi Güvenli Bir Şekilde Geri Çağır
async function getSensitiveData(key) {
  let result = await SecureStore.getItemAsync(key);
  if (result) {
    return result;
  } else {
    console.log("Veri bulunamadı veya yetki yok.");
  }
}
import React, { useState } from 'react';
import * as SecureStore from 'expo-secure-store';
import * as LocalAuthentication from 'expo-local-authentication';
import { Alert } from 'react-native';

const SecurityManager = () => {
  const [isVerified, setIsVerified] = useState(false);

  // ADIM 1: OTP DOĞRULAMA VE TOKEN SAKLAMA
  const handleVerifyOTP = async (userEnteredCode) => {
    // Sunucuya gönder (Örnek API çağrısı)
    const response = await fetch('https://api.verilerim.com/verify', {
      method: 'POST',
      body: JSON.stringify({ code: userEnteredCode })
    });
    const data = await response.json();

    if (data.token) {
      // Token'ı donanım seviyesinde mühürle
      await SecureStore.setItemAsync('user_session', data.token);
      setIsVerified(true);
      Alert.alert("Başarılı", "Güvenlik kilidi oluşturuldu.");
    }
  };

  // ADIM 2: BİYOMETRİK ERİŞİM (Her Uygulama Açılışında)
  const accessPrivateData = async () => {
    const biometricAuth = await LocalAuthentication.authenticateAsync({
      promptMessage: 'Verilerinize erişmek için kimlik doğrulayın',
      disableDeviceFallback: false,
    });

    if (biometricAuth.success) {
      // Mühürlü token'ı çöz ve kullan
      const token = await SecureStore.getItemAsync('user_session');
      console.log("Veri Erişimi İzni Verildi. Token:", token);
      // Hassas verileri getir...
    } else {
      Alert.alert("Erişim Engellendi", "Biyometrik doğrulama başarısız.");
    }
  };

  return (
    // UI Bileşenleri buraya gelecek (Inputlar, Butonlar)
  );
};
const rateLimit = require("express-rate-limit");

// Aynı IP'den 15 dakikada en fazla 5 OTP isteği atılabilir
const otpLimiter = rateLimit({
  windowMs: 15 * 60 * 1000, 
  max: 5,
  message: "Çok fazla deneme yaptınız, lütfen bekleyin."
});

app.post("/send-otp", otpLimiter, (req, res) => {
  // 1. Rastgele 6 haneli kod üret
  // 2. SMS API'sine (Twilio/Infobip) gönder
  // 3. Veritabanına hashleyerek kaydet (Şifre gibi saklanmalı!)
});
npx expo install expo-secure-store expo-local-authentication
import React, { useState, useRef } from 'react';
import { StyleSheet, Text, View, TextInput, TouchableOpacity, Alert } from 'react-native';
import * as SecureStore from 'expo-secure-store';
import * as LocalAuthentication from 'expo-local-authentication';

export default function SecurityApp() {
  const [otp, setOtp] = useState(['', '', '', '', '', '']);
  const [isVerified, setIsVerified] = useState(false);
  const inputRefs = useRef([]);

  // --- 1. OTP MANTIĞI: Kutular arası otomatik geçiş ---
  const handleOtpChange = (value, index) => {
    const newOtp = [...otp];
    newOtp[index] = value;
    setOtp(newOtp);

    if (value && index < 5) {
      inputRefs.current[index + 1].focus();
    }
  };

  // --- 2. DOĞRULAMA VE MÜHÜRLEME ---
  const verifyAndLock = async () => {
    const fullCode = otp.join('');
    
    // Simüle edilmiş API kontrolü (Gerçekte sunucuya gider)
    if (fullCode === "123456") { 
      // Sunucudan gelen Token'ı donanım seviyesinde mühürle
      await SecureStore.setItemAsync('user_session_token', 'SECRET_JWT_TOKEN_123');
      setIsVerified(true);
      Alert.alert("Başarılı!", "Kod doğrulandı ve oturumunuz mühürlendi.");
    } else {
      Alert.alert("Hata", "Girdiğiniz kod yanlış.");
    }
  };

  // --- 3. BİYOMETRİK ERİŞİM (Veri Kilidini Açma) ---
  const unlockWithBiometrics = async () => {
    const hasHardware = await LocalAuthentication.hasHardwareAsync();
    if (!hasHardware) return Alert.alert("Hata", "Cihazda biyometrik sensör yok.");

    const result = await LocalAuthentication.authenticateAsync({
      promptMessage: 'Hassas verilere erişim izni verin',
      fallbackLabel: 'Şifre kullan',
    });

    if (result.success) {
      const token = await SecureStore.getItemAsync('user_session_token');
      Alert.alert("Erişim Onaylandı", `Mühürlü Token Çözüldü: ${token}`);
    }
  };

  return (
    <View style={styles.container}>
      <Text style={styles.title}>{isVerified ? "Güvenli Bölge" : "Güvenlik Kodunu Girin"}</Text>
      
      {!isVerified ? (
        <View>
          <View style={styles.otpContainer}>
            {otp.map((digit, index) => (
              <TextInput
                key={index}
                ref={(el) => (inputRefs.current[index] = el)}
                style={styles.otpBox}
                keyboardType="number-pad"
                maxLength={1}
                onChangeText={(v) => handleOtpChange(v, index)}
                value={digit}
              />
            ))}
          </View>
          <TouchableOpacity style={styles.button} onPress={verifyAndLock}>
            <Text style={styles.buttonText}>KODU DOĞRULA</Text>
          </TouchableOpacity>
        </View>
      ) : (
        <TouchableOpacity style={[styles.button, {backgroundColor: '#2ecc71'}]} onPress={unlockWithBiometrics}>
          <Text style={styles.buttonText}>BİYOMETRİK İLE VERİLERİ AÇ</Text>
        </TouchableOpacity>
      )}
    </View>
  );
}

const styles = StyleSheet.create({
  container: { flex: 1, justifyContent: 'center', alignItems: 'center', backgroundColor: '#f5f6fa' },
  title: { fontSize: 22, fontWeight: 'bold', marginBottom: 30 },
  otpContainer: { flexDirection: 'row', justifyContent: 'space-between', width: '80%' },
  otpBox: { width: 45, height: 50, borderBottomWidth: 2, borderColor: '#3498db', fontSize: 24, textAlign: 'center' },
  button: { marginTop: 40, backgroundColor: '#3498db', padding: 15, borderRadius: 10, width: 250, alignItems: 'center' },
  buttonText: { color: 'white', fontWeight: 'bold' }
});
const paragraphs = document.querySelectorAll("p");
// paragraphs[0] is the first <p> element
// paragraphs[1] is the second <p> element, etc.
alert(paragraphs[0].nodeName);<html lang="en">
  <head>
    <title>My Document</title>
  </head>
  <body>
    <h1>Header</h1>
    <p>Paragraph</p>
  </body>
</html># Python DOM example
import xml.dom.minidom as m
doc = m.parse(r"C:\Projects\Py\chap1.xml")
doc.nodeName # DOM property of document object
p_list = doc.getElementsByTagName("para")<body onload="console.log('Welcome to my home page!');">
  …
</body><html lang="en">
  <head> </head>
  <body>
    <script>
      // create a couple of elements in an otherwise empty HTML page
      const heading = document.createElement("h1");
      const headingText = document.createTextNode("Big Head!");
      heading.appendChild(headingText);
      document.body.appendChild(heading);
    </script>
  </body>
</html>const table = document.getElementById("table");
const tableAttrs = table.attributes; // Node/Element interface
for (const attr of tableAttrs) {
  // HTMLTableElement interface: border attribute
  if (attr.nodeName.toLowerCase() === "border") {
    table.border = "1";
  }
}
// HTMLTableElement interface: summary attribute
table.summary = "note: increased border";<div class="container">
  <textarea class="story"></textarea>
  <button id="set-text" type="button">Set text content</button>
  <button id="clear-text" type="button">Clear text content</button>
</div>secure-auth-system/
├── backend-api/           # Node.js & Express Sunucusu
│   ├── src/
│   │   ├── controllers/   # OTP gönderme ve doğrulama mantığı
│   │   ├── middleware/    # Rate Limiting (Hız Sınırı)
│   │   └── routes/        # API uç noktaları
│   ├── package.json
│   └── .env               # SMS API anahtarları (Twilio vb.)
├── mobile-app/            # React Native (Expo) Uygulaması
│   ├── src/
│   │   ├── components/    # OtpInput, BiometricButton
│   │   ├── services/      # SecureStore ve API çağrıları
│   │   └── screens/       # Login, SecureHome
│   ├── App.js
│   └── app.json
├── scripts/               # Yardımcı Araçlar
│   └── otp_generator.py   # Python tabanlı güvenli OTP modülü
└── README.md              # Proje dökümantasyonu
# 🛡️ Secure Mobile Auth System

Bu proje; **Python**, **Node.js** ve **React Native** kullanarak uçtan uca güvenli bir OTP (Tek Kullanımlık Şifre) ve Biyometrik doğrulama sistemi sunar.

## ✨ Özellikler
- **Güvenli OTP:** Python `secrets` modülü ile kriptografik rastgele kod üretimi.
- **Hız Sınırı:** Node.js tabanlı kaba kuvvet (Brute-force) saldırı koruması.
- **Donanım Kilidi:** iOS Keychain ve Android Keystore ile `SecureStore` entegrasyonu.
- **Biyometrik Onay:** FaceID ve Parmak İzi ile veri mühürleme.

## 🚀 Kurulum

### 1. Backend (Sunucu)
```bash
cd backend-api
npm install
npm start
cd mobile-app
npx expo install
npx expo start
---

## 3. GitHub'a Yükleme Adımları (Git Komutları)

Bilgisayarında bir klasör aç ve şu komutları sırasıyla çalıştır:

1. **Repoyu Başlat:** `git init`
2. **Dosyaları Ekle:** `git add .`
3. **İlk Commit:** `git commit -m "Initial commit: Secure OTP and Biometric System"`
4. **GitHub'a Gönder:**
   ```bash
   git remote add origin https://github.com/kullaniciadi/secure-auth-system.git
   git branch -M main
   git push -u origin main# 1. Gerekli paketleri hızlıca kur (Eksik varsa tamamlar)
npm install 

