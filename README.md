# Proje 19: Human-Aware Service Robotics with Social Navigation

**Konu Numarası:** 19
**Proje Alanı:** Human-Robot Interaction & Social Navigation
**Grup:** G03

**Grup Üyeleri:** 
* Evin Yılmaz (032490093)
* Meltem Can (032490033)
* Rojin Topuz (032390078)
* Enes Altunbaş (032490096)
---

## 02. Amaç, Problem Tanımı ve Proje Kapsamı
**Amaç:** İnsanların bulunduğu dinamik kapalı alanlarda (kampüs, hastane vb.) görev yapan bir servis robotunun, hedefine ilerlerken insanlarla sadece fiziksel olarak çarpışmaktan kaçınması değil, aynı zamanda "sosyal mesafe" ve "konfor" kurallarına (Proxemics) uygun, sosyal olarak kabul edilebilir bir rotada hareket etmesini sağlamaktır.

**Problem Tanımı:** Standart otonom navigasyon sistemleri, insanları statik veya öngörülemez silindir engeller olarak görür ve geometrik olarak en kısa yolu seçer. Ancak bu durum, robotun insanların kişisel alanını ihlal etmesine, ani hareketlerle rahatsızlık yaratmasına veya grup halindeki insanların arasından geçmeye çalışarak görev akışını bozmasına neden olur.

**Kapsam:** ROS Noetic ve Gazebo ortamında, bir mobil robotun insanları algılaması (kamera/LIDAR füzyonu), kişisel alanlarını bir maliyet (cost) olarak haritaya işlemesi ve bu sosyal maliyetlere uygun şekilde yol planlaması yapması hedeflenmektedir.

---

## 03. Kullanılacak Yöntemler ve Algoritmalar
* **İnsan Algılama ve Takibi:** Görüntü işleme tabanlı nesne tespiti (YOLO) ile 2D/3D pozisyon tahmini.
* **Sosyal Navigasyon Modeli:** Proxemics teorisine dayalı "Social Costmap Layer" (Sosyal Maliyet Haritası Katmanı). İnsanların çevresinde Gauss dağılımı ile kişisel alan bölgeleri (intimate, personal, social) oluşturulması.
* **Yol Planlama:** Global planlama için A*/Dijkstra, yerel planlama ve dinamik engellerden kaçınma için TEB (Timed Elastic Band) veya DWA (Dynamic Window Approach) Local Planner.

---

## 04. Yöntem Seçim Gerekçeleri ve Alternatifler
* **Neden Social Costmap?** Reinforcement Learning (RL) tabanlı uçtan uca sosyal navigasyon modelleri yüksek hesaplama gücü ve uzun eğitim süreleri gerektirir. "Social Costmap" eklentisi ise ROS navigasyon yığını (Navigation Stack) ile doğrudan ve deterministik bir şekilde entegre çalıştığı için gerçek zamanlı uygulamalarda daha güvenilir ve donanım dostudur (RTX 3050 ve Ubuntu 20.04 ortamında yüksek performans).
* **Neden YOLO?** Geleneksel LIDAR tabanlı insan bacağı algılama (leg_detector) algoritmaları, kalabalık ve dar ortamlarda yüksek hata payına sahiptir. YOLO tabanlı görsel algılama, insanın pozisyonunu ve yönelimini belirlemede çok daha kararlı sonuçlar verir.

---

## 05. Sistem Mimarisi ve Temel Çalışma Akışı
1. **Algılama Katmanı:** Kamera sensöründen alınan verilerle ortamdaki insanlar tespit edilir ve 3D konumları hesaplanır.
2. **Harita Katmanı:** Tespit edilen insanların konumları etrafına, ROS Costmap 2D üzerinde dinamik sosyal maliyet katmanları (inflation) eklenir. İnsanın hareket yönüne doğru asimetrik bir maliyet haritası oluşturulur.
3. **Planlama Katmanı:** Global planlayıcı, sosyal maliyetlerin yüksek olduğu bölgelerden (insanların kişisel alanlarından) kaçınan genel bir rota çizer.
4. **Kontrol Katmanı:** Yerel planlayıcı (TEB/DWA), robotun anlık hızını ve dönüşlerini insanları rahatsız etmeyecek ivmelerle (düşük jerk) gerçekleştirir.

---

## 06. Geliştirme Araçları / Simülasyon Ortamı
* **İşletim Sistemi:** Ubuntu 20.04 LTS
* **Robotik Çerçeve:** ROS Noetic
* **Simülasyon:** Gazebo 3D Simulator (İnsan hareketliliğini simüle eden eklentilerle birlikte)
* **Programlama Dili:** Python & C++
* **Geliştirme Ortamı:** VS Code, Antigravity IDE
* **Algı ve Derin Öğrenme:** OpenCV, PyTorch, YOLO

---

## 07. Kurulum ve Projeyi Çalıştırma Adımları
*(Bu bölüm proje geliştikçe güncellenecektir)*
1. Repoyu klonlayın: `git clone <repo-url>`
2. Gerekli ROS paketlerini yükleyin: `rosdep install --from-paths src --ignore-src -r -y`
3. Çalışma alanını derleyin: `catkin_make`
4. Ortamı kaynaklandırın: `source devel/setup.bash`
5. Simülasyonu başlatın: `roslaunch social_navigation gazebo_world.launch`

---
##
## 08. Test, Deney, Metrik ve Örnek Çıktılar
Projenin başarısını değerlendirmek için şu metrikler kullanılacaktır:
* **Kişisel Alan İhlali (Personal Space Intrusion):** Robotun insanlara 1.2 metreden daha fazla yaklaştığı durumların sayısı.
* **Görev Tamamlama Süresi (Navigation Time):** Robotun sosyal kurallara uyarken hedef noktaya ulaşmasında yaşanan gecikme payı.
* **Konfor Metriği (Jerk):** Robotun ani hızlanma veya yavaşlama miktarı.
* **Dondurma Modu (Freezing Robot Problem):** Çok kalabalık senaryolarda robotun hareket edemeyip kilitlenme oranı.

*(Örnek RViz çıktıları, maliyet haritası görselleri ve simülasyon test tabloları `results/` klasörüne eklenecektir.)*

---

## 09. Yol Haritası ve İlerleme Günlüğü

| TARİH | YAPILAN ÇALIŞMA | MEVCUT DURUM / SORUN | SONRAKİ ADIM |
| :--- | :--- | :--- | :--- |
| 2026-11-11 | Proje konusu seçimi ve repo kurulumu | İskelet dosya yapısı ve README oluşturuldu. | Gazebo'da basit bir ofis ortamı ve robot modelinin ayağa kaldırılması. |
| YYYY-MM-DD | ... | ... | ... |

---

## 10. Araştırma Kaynakları ve Referanslar
1. Hall, E. T. (1966). *The Hidden Dimension*. (Proxemics teorisi temelleri)
2. Lu, D. V., et al. (2014). *Layered Costmaps for Context-Sensitive Navigation*. (ROS Costmap yapısı)
3. Helbing, D., & Molnar, P. (1995). *Social force model for pedestrian dynamics*.
4. ROS Navigation Stack Documentation: http://wiki.ros.org/navigation
