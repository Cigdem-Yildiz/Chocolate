## Kullanılan Araçlar
* **Model:** Google Gemini, Claude AI

## Konuşmalar
**1. Soru(Google Gemini):** html canvas olulturdum. javascript kullnaıyorum. canvas ın bir yerinde resim var. o resmi merkezi etrafında döndürmek istiyorum. saat gibi düşün merkez noktasından devamlı dönsün istiyorum nasıl yaparım bunu
<br/>**1. Cevap:** 
```text
<!DOCTYPE html>
<html lang="tr">
<head>
    <meta charset="UTF-8">
    <title>Canvas Dönen Resim</title>
    <style>
        canvas {
            border: 1px solid #ccc;
            background-color: #f9f9f9;
        }
    </style>
</head>
<body>

<canvas id="myCanvas" width="500" height="500"></canvas>

<script>
    const canvas = document.getElementById('myCanvas');
    const ctx = canvas.getContext('2d');

    // Döndürmek istediğin resmi oluştur
    const img = new Image();
    img.src = 'https://picsum.photos/100'; // Buraya kendi resminin yolunu koymalısın

    // Resmin özellikleri
    const imgWidth = 100;
    const imgHeight = 100;
    
    // Resmin canvas üzerindeki konumu (X ve Y)
    const posX = 200; 
    const posY = 200;

    let angle = 0; // Başlangıç açısı

    // Resim yüklendiğinde animasyonu başlat
    img.onload = function() {
        animate();
    };

    function animate() {
        // 1. Her kareden önce canvas'ı temizle (yoksa resimler üst üste biner)
        ctx.clearRect(0, 0, canvas.width, canvas.height);

        // 2. Canvas'ın güncel durumunu kaydet
        ctx.save();

        // 3. Orijini (0,0 noktasını) resmin merkezine taşı
        // Resmin sol üst köşesi (posX, posY) olduğu için, merkezi bunun yarısı kadar ileridedir.
        ctx.translate(posX + (imgWidth / 2), posY + (imgHeight / 2));

        // 4. Canvas'ı döndür
        ctx.rotate(angle);

        // 5. Resmi çiz! 
        // DİKKAT: Orijin artık resmin tam ortasında. Bu yüzden resmi, genişliğinin ve 
        // yüksekliğinin yarısı kadar geriden başlatarak çiziyoruz (-50, -50 gibi).
        ctx.drawImage(img, -imgWidth / 2, -imgHeight / 2, imgWidth, imgHeight);

        // 6. Canvas'ı eski durumuna (orijin 0,0) geri getir
        ctx.restore();

        // 7. Açıyı artır (Dönüş hızını buradan ayarlayabilirsin)
        angle += 0.05; // Açı radyan cinsindendir.

        // Sürekli döngü için requestAnimationFrame kullan
        requestAnimationFrame(animate);
    }
</script>

</body>
</html>

```

**2. Soru(Google Gemini):** Bitiş nesnesinin merkez noktasından büyüyüp küçülmesini istiyorum nasıl yaparım
<br/>**2. Cevap:** 
```text
const canvas = document.getElementById('myCanvas');
const ctx = canvas.getContext('2d');

const img = new Image();
img.src = 'resminizin-yolu.jpg'; 

let time = 0;

function animate() {
    // 1. Her karede canvas'ı temizle
    ctx.clearRect(0, 0, canvas.width, canvas.height);

    const imgX = 100; // Resmin X başlangıç noktası
    const imgY = 100; // Resmin Y başlangıç noktası
    const imgWidth = 200; 
    const imgHeight = 200;

    // Zamanla değişen bir ölçek değeri hesapla
    // Math.sin() -1 ile 1 arası değer üretir. Bunu kullanarak 1x ile 1.5x arası gidip gelen bir değer elde ediyoruz.
    const scale = 1 + Math.abs(Math.sin(time)) * 0.5;

    // 2. Mevcut canvas durumunu kaydet (diğer çizimleri etkilememek için)
    ctx.save();

    // 3. Orijini resmin tam merkezine taşı
    ctx.translate(imgX + imgWidth / 2, imgY + imgHeight / 2);

    // 4. Büyütme/Küçültme işlemini uygula
    ctx.scale(scale, scale);

    // 5. Resmi çiz (Orijin merkezde olduğu için -genişlik/2 ve -yükseklik/2 pozisyonundan başlıyoruz)
    ctx.drawImage(img, -imgWidth / 2, -imgHeight / 2, imgWidth, imgHeight);

    // 6. Canvas durumunu geri yükle (orijini ve scale'i eski haline getir)
    ctx.restore();

    // Zamanı ilerlet
    time += 0.05;

    // Animasyonu devam ettir
    requestAnimationFrame(animate);
}

// Resim yüklendikten sonra animasyonu başlat
img.onload = () => {
    animate();
};

```
**3. Soru(Claude AI):** Hızlanma sorununu çözemedim. Karakter ve turner lar zaman geçtikçe hızlanıyor ne yapabilirim.
<br/>**3. Cevap:**
1. Delta time yok (ana sebep)
requestAnimationFrame ekranın yenileme hızına göre çalışır — 60Hz'de 60fps, 144Hz monitörde 144fps. Tüm hareket hesapları (player.vx, gravity, angle, time) her kare için sabit değer ekliyor, bu yüzden yüksek Hz'li ekranlarda çok daha hızlı çalışıyor.
2. resizeCanvas() içinde draw() çağrılıyor
Her pencere boyutu değiştiğinde draw() tetikleniyor, bu da angle ve time değişkenlerini ekstra artırıyor. Turner'lar bundan etkileniyor.

```text
let lastTime = null;
function gameLoop(timestamp){
  if(lastTime === null) lastTime = timestamp;
  const delta = Math.min((timestamp - lastTime) / (1000/60), 3);
  lastTime = timestamp;
  if(game_part === 1) update1(delta);
  else if(game_part === 2) update2(delta);
  draw(delta);
  requestAnimationFrame(gameLoop);
}

function resizeCanvas() {
  const padding = 0.9;
  const scaleX = (window.innerWidth * padding) / BASE_WIDTH;
  const scaleY = (window.innerHeight * padding) / BASE_HEIGHT;
  scale = Math.min(scaleX, scaleY);
  canvas.width = BASE_WIDTH * scale;
  canvas.height = BASE_HEIGHT * scale;
  // draw() çağırma — gameLoop halleder
}
```