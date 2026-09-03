# OpenWrt for TP-Link EX520v (TTNET AX3000)

[TR] TP-Link EX520v v1 (TTNET / Türk Telekom AX3000 Wi-Fi 6 Router) için OpenWrt portlama projesi.
[EN] OpenWrt porting project for TP-Link EX520v v1 (TTNET / Türk Telekom AX3000 Wi-Fi 6 Router).

---

> 📢 **Duyuru / Announcement**
> 
> **[TR]:** Portlama süreci tamamlanmış ve tüm testlerden başarıyla geçmiştir. Derlenmiş imaj dosyaları, kaynak kodlar ve adım adım kurulum rehberi **bu ayın sonuna doğru** bu depoda ve Techolay konusunda paylaşılacaktır.
> 
> **[EN]:** The porting process is completed and verified. Pre-compiled images, source files, and a step-by-step installation guide **will be published in this repository by the end of this month.**

---

## Donanım Özellikleri / Technical Specifications

| Bileşen / Component | Özellik / Specification |
| :--- | :--- |
| **SoC** | MediaTek MT7981B (Filogic 820) – 2× ARM Cortex-A53 @ 1.3 GHz |
| **RAM** | 512 MiB DDR3 |
| **Flash** | 512 MiB SPI-NAND (Micron, 4K sayfa / 256K silme bloğu, NMBM) |
| **Wi-Fi** | MT7981 + MT7976 (DBDC 2.4 GHz & 5 GHz, 802.11ax / Wi-Fi 6) |
| **Ethernet** | 3× GbE LAN (MT7531 Switch via 2.5G GMAC0) + 1× GbE WAN (GMAC1) |
| **USB** | 1× USB 3.0 |
| **Kalibrasyon / EEPROM** | `misc_ro` partisyonu üzerinde 4 KiB MT7981 EEPROM |

---

## Çalışan Özellikler / Working Features

**[TR]**
* **Taban:** Güncel OpenWrt `SNAPSHOT` trunk mimarisi.
* **Wi-Fi:** 5 GHz üzerinde 160 MHz bant genişliği (`HE160`) ve DFS kanalları tam aktif. Eşzamanlı çift bant AP (`2.4 GHz HE40 + 5 GHz HE160`).
* **Donanım Hızlandırma:** Donanımsal paket yönlendirme (PPE / WED - Wireless Ethernet Dispatcher) aktif.
* **Ağ ve Çevre Birimleri:** Switch (LAN1-LAN3), WAN, USB 3.0, Sistem LED'leri ve Reset / WPS butonları sorunsuz çalışıyor.
* **Depolama ve Kalıcılık:** NMBM destekli kalıcı overlay yapısı (`UBI rootfs_data`).
* **Özel DTS & Bootloader Güvenliği:** Stok BL2 / FIP / birincil U-Boot yapılarına dokunulmaz. Fabrika RF kalibrasyon verileri doğrudan `misc_ro` üzerinden okunur.

**[EN]**
* **Base:** Built on OpenWrt `SNAPSHOT` trunk.
* **Wi-Fi:** Full 160 MHz bandwidth (`HE160`) with DFS support on 5 GHz. Simultaneous dual-band AP (`2.4 GHz HE40 + 5 GHz HE160`).
* **Hardware Acceleration:** Hardware Flow Offloading (PPE / WED) fully functional.
* **Peripherals & Ports:** Integrated Switch (LAN1-LAN3), WAN routing, USB 3.0 stack, LEDs, and Reset / WPS buttons.
* **Storage & Persistence:** Persistent UBI overlay layout with NMBM bad block management.
* **Safety & Integrity:** Stock BL2 / FIP / Primary U-Boot partition logic remains untouched. Factory calibration is loaded directly from `misc_ro`.

---

## Kurulum ve Mimari / Installation Strategy

**[TR]**
* Stok önyükleyici (U-Boot/BL2) korunduğu için cihazın tamamen kullanılmaz hale gelme (brick) riski minimuma indirilmiştir.
* Fabrika kalibrasyon verileri (4 KiB EEPROM) korunur, Wi-Fi sinyal gücü ve orijinal MAC adresleri kaybolmaz.
* İmaj yükleme ve sysupgrade adımları **bu ayın sonuna doğru** detaylandırılacaktır.

**[EN]**
* Stock bootloader partitions are kept safe to minimize brick risks.
* Factory EEPROM (4 KiB) calibration data is parsed directly to retain original RF power and MAC definitions.
* Detailed flashing steps (serial console / sysupgrade procedures) will be released **by the end of this month**.

---

## Topluluk & Tartışma / Community & Discussion

**[TR]:** Gelişmeleri takip etmek, soru sormak veya geri bildirimde bulunmak için Techolay üzerindeki konuyu ziyaret edebilirsiniz:  
**[EN]:** You can follow the discussion or provide feedback on the community thread:

* 🔗 **Techolay Konusu / Forum Thread:** [Techolay - TP-Link EX520v OpenWrt Tartışma Konusu](https://techolay.net)

---

## Sorumluluk Reddi / Disclaimer

**[TR]:** Cihazınıza üçüncü taraf yazılım yüklemek kendi sorumluluğunuzdadır. İşlemlere başlamadan önce mevcut partisyon yediklerinizi almanız önerilir.  
**[EN]:** Flashing custom firmware is at your own risk. Always maintain full partition backups before proceeding.
