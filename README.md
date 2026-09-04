# OpenWrt for TP-Link EX520v (TTNET AX3000)

[TR] TP-Link EX520v v1 (TTNET / Türk Telekom AX3000 Wi-Fi 6 Router) için OpenWrt portlama projesi.

[EN] OpenWrt porting project for TP-Link EX520v v1 (TTNET / Türk Telekom AX3000 Wi-Fi 6 Router).

---

> 📢 **Duyuru / Announcement**
> 
> **[TR]:** Portlama süreci tamamlanmış ve tüm testlerden başarıyla geçmiştir. Derlenmiş imaj dosyaları ve aşağıdaki adım-by-adım kurulum rehberi artık bu depoda yayında. Sorularınızı ve geri bildirimlerinizi Techolay forumundaki başlık üzerinden paylaşabilirsiniz.
> 
> **[EN]:** The porting process is complete and fully verified. Pre-compiled images and the step-by-step installation guide below are now available in this repository. For questions, check the Techolay thread linked below.

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
* **Donanım Hızlandırma:** Donanımsal paket yönlendirme (PPE / WED - Wireless Ethernet Dispatcher) aktif. Ayarları **Network → Firewall → General Settings** altındaki "Software Flow Offloading" bölümünden kontrol edebilirsiniz.
* **Ağ ve Çevre Birimleri:** Switch (LAN1-LAN3), WAN, USB 3.0, Sistem LED'leri ve Reset / WPS butonları sorunsuz çalışıyor.
* **Depolama ve Kalıcılık:** NMBM destekli kalıcı overlay yapısı (`UBI rootfs_data`).
* **Özel DTS & Bootloader Güvenliği:** Stok BL2 / FIP / birincil U-Boot yapılarına dokunulmaz. Fabrika RF kalibrasyon verileri doğrudan `misc_ro` üzerinden okunur.

**[EN]**
* **Base:** Built on OpenWrt `SNAPSHOT` trunk.
* **Wi-Fi:** Full 160 MHz bandwidth (`HE160`) with DFS support on 5 GHz. Simultaneous dual-band AP (`2.4 GHz HE40 + 5 GHz HE160`).
* **Hardware Acceleration:** Hardware Flow Offloading (PPE / WED) fully functional. Toggle it under **Network → Firewall → General Settings → Software/Hardware Flow Offloading**.
* **Peripherals & Ports:** Integrated Switch (LAN1-LAN3), WAN routing, USB 3.0 stack, LEDs, and Reset / WPS buttons.
* **Storage & Persistence:** Persistent UBI overlay layout with NMBM bad block management.
* **Safety & Integrity:** Stock BL2 / FIP / Primary U-Boot partition logic remains untouched. Factory calibration is loaded directly from `misc_ro`.

---

## Kurulum Adımları / Installation Guide

**[TR]** Kurulum, cihazın seri (UART) konsoluna erişim gerektirir. Stok bootloader'a hiçbir şekilde dokunulmaz; imaj yalnızca TFTP ile RAM'e çekilip test edilir, ardından SSH üzerinden sysupgrade ile NAND'e yazılır.

**[EN]** Installation requires access to the device's UART serial console. The stock bootloader is never modified; the image is first pulled into RAM over TFTP and booted for testing, then permanently flashed using sysupgrade.

### 1) Gerekli Malzemeler / Requirements

**[TR]**
* USB-TTL (3.3V) seri adaptör (FTDI, CP2102, CH340 vb.)
* 3× erkek uçlu (male-to-female veya male-to-male, port ihtiyacına göre) jumper kablo
* Cihazın kasasını sökmek için Torx/Phillips tornavida
* Ana bilgisayarda `tftp`, `ssh` ve `scp` araçları (Linux/macOS'ta hazır; Windows'ta örn. `tftpd64` + PuTTY/WinSCP)
* Aşağıdaki imaj dosyaları:
  * `openwrt-mediatek-filogic-tplink_ex520v-initramfs-kernel.bin`
  * `openwrt-mediatek-filogic-tplink_ex520v-squashfs-sysupgrade.bin`

**[EN]**
* USB-TTL (3.3V) serial adapter (FTDI, CP2102, CH340, etc.)
* 3× male-ended jumper wires (male-to-female or male-to-male depending on your header)
* Torx/Phillips screwdriver to open the case
* `tftp`, `ssh`, and `scp` tools on your host (built-in on Linux/macOS; on Windows use e.g. `tftpd64` + PuTTY/WinSCP)
* The following image files:
  * `openwrt-mediatek-filogic-tplink_ex520v-initramfs-kernel.bin`
  * `openwrt-mediatek-filogic-tplink_ex520v-squashfs-sysupgrade.bin`

### 2) UART Erişimi / UART Access

**[TR]**
Pinout, kartın üzerindeki fotoğrafta aşağıda gösterilmiştir. **RX ve TX sinyallerinin ilgili pinlere ulaşabilmesi için, kart üzerinde birbirinden ayrı duran iki pad'in köprülenmesi gerekir.**

* Görseldeki pinlere erkek header'lı jumper kablonun uçlarını sıkıca oturtup üzerlerine bir sigara paketi veya benzeri hafif bir ağırlık koymak çoğu zaman yeterlidir.
* Cihazı tek seferlik portlayacaksanız bu yöntem yeterlidir. Birden fazla deneme/test yapacaksanız, bağlantının sürekli ve sağlam kalması için pinleri lehimlemeniz tavsiye edilir.

> ⚠️ Sadece **RX**, **TX** ve **GND** hatlarını bağlayın; adaptörün **VCC/3.3V** ucunu cihaza bağlamayın. Ayrıca adaptörünüzün **RX**'ini kartın **TX**'ine, **TX**'ini kartın **RX**'ine bağlayın.

**UART pin bölgesi ve köprülenmesi gereken pad'ler:**

![UART pinout ve köprülenmesi gereken pad bölgesi](https://raw.githubusercontent.com/wmemcmp/ex520v/refs/heads/main/gallery/IMG_0075.png)


**[EN]**
The pinout is shown in the photo below. **The RX and TX signals only reach their pins after bridging two separate pads on the board.** Soldering is not strictly required for this bridge:

* Pressing the male ends of jumper wires firmly onto the pads and weighing them down with something like a cigarette pack is usually enough.
* If you're only porting the device once, that method is sufficient. If you plan on doing multiple test boots, soldering the pins is recommended for a stable, repeatable connection.

> ⚠️ Only connect **RX**, **TX**, and **GND** — do **not** connect the adapter's **VCC/3.3V** line to the board. Also remember to cross the lines: adapter **RX** → board **TX**, adapter **TX** → board **RX**.

**UART pin area and the pads that need bridging:**

*(Replace the placeholder images above with your own high-resolution photos under `docs/images/`; marking the two pads that need bridging directly on the image is recommended.)*

Seri bağlantı ayarları / Serial console settings:

```
Baud rate: 115200
Data bits: 8
Parity:    None
Stop bits: 1
Flow control: None
```

### 3) Bootloader Üzerinden TFTP ile İmajı Belleğe Çekme / Boot the initramfs Image via TFTP

**[TR]** Cihazı açın, bootloader (U-Boot) menüsüne girin ve ana bilgisayarınızda çalışan bir TFTP sunucusundan initramfs imajını RAM'e çekip başlatın.

> Not: Bootloader'ın ve cihazın TFTP beklentisi nedeniyle ana bilgisayarınızın (tftpd sunucusunun çalıştığı PC) sabit IP adresinin bu örnekte 192.168.1.2 olarak ayarlanması önerilir.

**[EN]** Power on the device, interrupt boot to enter the U-Boot prompt, and pull the initramfs image from a TFTP server running on your host, then boot it directly from RAM.

> Note: Because the bootloader expects the host to be on the same subnet, set your host (the PC running the TFTP server) to a static IP of 192.168.1.2 for this example.

```bash
# Örnek U-Boot komutları / Example U-Boot commands
setenv ipaddr 192.168.1.1
setenv serverip 192.168.1.2
tftpboot 0x46000000 openwrt-mediatek-filogic-tplink_ex520v-initramfs-kernel.bin
bootm 0x46000000
```

![Host with static IP 192.168.1.2](https://raw.githubusercontent.com/wmemcmp/ex520v/refs/heads/main/gallery/static.png)

**[TR]** Bu adımda NAND'e hiçbir şey yazılmaz; cihaz yalnızca RAM üzerinden geçici olarak OpenWrt ile açılır. Bootloader ve mevcut stok firmware tamamen dokunulmadan kalır.

**[EN]** Nothing is written to NAND at this stage — the device boots OpenWrt temporarily from RAM only. The bootloader and existing stock firmware remain completely untouched.

### 4) SCP ile İmajı Aktarma ve SSH Üzerinden NAND'e Yazma / Transfer and Flash via SSH

**[TR]** Cihaz initramfs ile açıldıktan sonra, LAN üzerinden erişilebilir olacaktır (varsayılan olarak `192.168.1.1`). Sysupgrade imajını SCP ile `/tmp/` dizinine kopyalayın:

**[EN]** Once the device has booted the initramfs image, it will be reachable over LAN (default `192.168.1.1`). Copy the sysupgrade image to `/tmp/` via SCP:

```bash
scp openwrt-mediatek-filogic-tplink_ex520v-squashfs-sysupgrade.bin root@192.168.1.1:/tmp/
```

**[TR]** Ardından SSH ile cihaza bağlanıp imajı NAND'e yazın:

**[EN]** Then connect over SSH and flash the image to NAND:

```bash
ssh root@192.168.1.1
sysupgrade -n /tmp/openwrt-mediatek-filogic-tplink_ex520v-squashfs-sysupgrade.bin
```

**[TR]** `-n` bayrağı, mevcut ayarları koruma girişiminde bulunmadan temiz bir kurulum yapılmasını sağlar (ilk kurulum için önerilir). Bu komut sysupgrade imajını NAND'e yazar ve bölümlendirmeyi günceller.

**[EN]** The `-n` flag performs a clean install without attempting to preserve existing settings (recommended for the first flash). This command writes the sysupgrade image to NAND and updates partitions.

### 5) Doğrulama / Verification

**[TR]**
* Cihaza `ssh root@192.168.1.1` ile tekrar bağlanabildiğinizi doğrulayın.
* `ubus call system board` komutuyla model ve imaj bilgisini kontrol edin.
* Wi-Fi arayüzlerinin (2.4 GHz ve 5 GHz) LuCI üzerinden **Network → Wireless** sekmesinde göründüğünden emin olun.
* Donanım hızlandırmayı **Network → Firewall → General Settings** altından etkinleştirin/kontrol edin (aşağıdaki HW Offloading görseline bakın).

**[EN]**
* Confirm you can SSH back into the device at `ssh root@192.168.1.1`.
* Check the model and image info with `ubus call system board`.
* Verify both Wi-Fi radios (2.4 GHz and 5 GHz) appear under **Network → Wireless** in LuCI.
* Enable/verify hardware offloading under **Network → Firewall → General Settings → Software/Hardware Flow Offloading** (see the HW Offloading screenshot below).

![Hardware Flow Offloading](https://raw.githubusercontent.com/wmemcmp/ex520v/refs/heads/main/gallery/image.png)

---

## Sorun Giderme / Troubleshooting

**[TR]**
* **TFTP zaman aşımına uğruyor:** Ana bilgisayarınızın IP adresinin bootloader'ın beklediği alt ağda olduğundan (genelde `192.168.1.x`) ve TFTP sunucunuzun/güvenlik duvarınızın 69/UDP portunu engellemediğinden emin olun. Bu rehberdeki örnekler için ana bilgisayarın `192.168.1.2` statik IP'si kullanılmaktadır.
* **Seri konsolda çıktı görünmüyor:** Baud hızının `115200` olduğunu, RX/TX hatlarının çapraz bağlandığını ve GND hattının ortak olduğunu kontrol edin.
* **`sysupgrade` sonrası cihaza erişilemiyor:** Birkaç dakika bekleyin (ilk açılışta overlay/UBI hazırlığı zaman alabilir); yine erişilemiyorsa UART üzerinden log kontrolü yapın.

**[EN]**
* **TFTP times out:** Make sure your host's IP is on the subnet the bootloader expects (usually `192.168.1.x`) and that port 69/UDP isn't blocked by your TFTP server or firewall. This guide uses `192.168.1.2` as the host static IP in examples.
* **No output on the serial console:** Check that the baud rate is `115200`, RX/TX lines are crossed correctly, and GND is shared between the adapter and the board.
* **Device unreachable after `sysupgrade`:** Wait a few minutes (first boot can take longer while UBI/overlay is prepared); if it's still unreachable, check the logs over UART.

---

## Topluluk & Tartışma / Community & Discussion

**[TR]:** Gelişmeleri takip etmek, soru sormak veya geri bildirimde bulunmak için Techolay üzerindeki konuyu ziyaret edebilirsiniz:
**[EN]:** You can follow the discussion or provide feedback on the community thread:

* 🔗 **Techolay Konusu / Forum Thread:** [Techolay - TP-Link EX520v OpenWrt Konusu](https://techolay.net/sosyal/konu/tp-link-ex520v-openwrt-kurulumu.214971/)

---

## Sorumluluk Reddi / Disclaimer

**[TR]:** Cihazınıza üçüncü taraf yazılım yüklemek kendi sorumluluğunuzdadır. İşlemlere başlamadan önce mevcut partisyonların yedeklerini almanız ve garanti kapsamınızın etkilenebileceğini unutmayın.

**[EN]:** Flashing custom firmware is at your own risk. Always maintain full partition backups before proceeding, and be aware that this process may affect your warranty.
