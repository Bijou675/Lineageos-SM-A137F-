# Lineageos-SM-A137F 
#!/bin/bash

# Script para compilar BijouOS basado en LineageOS 21 (Android 14) y AOSP
# para el dispositivo Samsung Galaxy A13 (SM-A137F) - arquitectura 64-bit
# Repositorios reales alojados bajo GitHub usuario Bijou675

# 1. Instalar dependencias
sudo apt update && sudo apt install -y \
  openjdk-17-jdk bc bison build-essential curl flex git gnupg gperf \
  libncurses5-dev libssl-dev libxml2 libxml2-utils lzop pngcrush rsync \
  schedtool squashfs-tools xsltproc zip zlib1g-dev python-is-python3 \
  ccache lib32z1 lib32ncurses6 lib32stdc++6 clang gnutls-bin ca-certificates

# 2. Configurar ccache
export USE_CCACHE=1
ccache -M 50G

# 3. Preparar directorio de trabajo
mkdir -p ~/bijouos && cd ~/bijouos

# 4. Descargar repo tool
mkdir -p ~/bin
curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
chmod a+x ~/bin/repo
export PATH=~/bin:$PATH

# 5. Configurar git para evitar errores de handshake TLS
git config --global http.version HTTP/1.1

echo "Clonando AOSP base y aplicando parches de LineageOS y BijouOS..."

# 6. Inicializar repositorio AOSP base con parches LineageOS 21
repo init -u https://github.com/LineageOS/android.git -b lineage-21.0
repo sync -j4 --fail-fast --force-sync || repo sync -j1 --force-sync

# 7. Clonar repositorios reales de device, vendor y kernel para SM-A137F
mkdir -p device/samsung/a13sm
mkdir -p vendor/samsung/a13sm
mkdir -p kernel/samsung/a13sm

git clone https://github.com/Bijou675/device_samsung_a13sm.git device/samsung/a13sm
git clone https://github.com/Bijou675/vendor_samsung_a13sm.git vendor/samsung/a13sm
git clone https://github.com/Bijou675/kernel_samsung_a13sm.git kernel/samsung/a13sm

# 8. Aplicar parches de BijouOS si existen (simulado)
# patch -p1 < patches/bijouos_core.patch
# patch -p1 < patches/indra_ai.patch

# 9. Configurar entorno de compilación
source build/envsetup.sh
lunch lineage_a13sm-userdebug

# 10. Compilar la ROM
make bacon -j$(nproc)

# El archivo .zip final aparecerá en out/target/product/a13sm

# 11. Script de actualizaciones incrementales (auto-commit & push)
echo '#!/bin/bash' > update_bijouos.sh
echo 'cd ~/bijouos/device/samsung/a13sm && git add . && git commit -m "Update device tree" && git push' >> update_bijouos.sh
echo 'cd ~/bijouos/vendor/samsung/a13sm && git add . && git commit -m "Update vendor" && git push' >> update_bijouos.sh
echo 'cd ~/bijouos/kernel/samsung/a13sm && git add . && git commit -m "Update kernel" && git push' >> update_bijouos.sh
chmod +x update_bijouos.sh

# 12. GUI opcional en modo texto
if command -v whiptail &> /dev/null; then
  whiptail --title "BijouOS Builder" --msgbox "ROM lista para compilar. Usa ./update_bijouos.sh para subir cambios." 10 60
fi
