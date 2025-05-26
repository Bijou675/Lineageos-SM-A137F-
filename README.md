# 📱 LineageOS para Samsung Galaxy A13 (SM-A137F)

Este repositorio contiene los enlaces y scripts necesarios para compilar una ROM personalizada basada en **LineageOS 21 (Android 14)** para el **Samsung Galaxy A13 (modelo SM-A137F, codename: `a13q`)**.

---

## 📂 Estructura de repositorios

Este proyecto utiliza tres subrepositorios principales:

- 📦 Árbol de dispositivo: [`device_samsung_a13q`](https://github.com/Bijou675/device_samsung_a13q)
- 🧩 Vendor blobs: [`vendor_samsung_a13q`](https://github.com/Bijou675/vendor_samsung_a13q)
- 🧠 Kernel fuente: [`kernel_samsung_a13q`](https://github.com/Bijou675/kernel_samsung_a13q)

---

## 🚀 Instrucciones de compilación rápida

```bash
# Clona el repo principal
git clone https://github.com/Bijou675/LineageOS-SM-A137F.git
cd LineageOS-SM-A137F

# Descarga todos los repos necesarios
bash ./init.sh

# Inicia el entorno de compilación
source build/envsetup.sh
lunch lineage_a13q-userdebug

# Compila la ROM
make bacon -j$(nproc)

