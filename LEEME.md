# Censo de Carga: proyecto de la app

App móvil para hacer censos de carga eléctrica. Funciona sin conexión y guarda todo en el teléfono.

## Qué incluye

- Censo con usuario, contrato opcional (`10000` + 7 dígitos, o `K000` + 9 dígitos), dirección y fecha automática al guardar. Si escribes un contrato, debe tener uno de esos formatos; si lo dejas en blanco, el reporte dice "Sin contrato".
- Biblioteca de equipos con variantes (watts, 110/220 V y factor de potencia propio).
- Cálculo de kWh/día y kVA, y leyenda con breaker principal, cable, neutro, tierra y protecciones.
- Reportes guardados, con PDF real de una o varias páginas, impresión y envío por correo o WhatsApp.

Carpeta `www/` = la app. El resto es el empaquetado para Android (Capacitor).

## 1. Probarla en el computador

```
cd www
python3 -m http.server 8000
```

Abre `http://localhost:8000` en Chrome. Con el modo de dispositivo móvil (F12) se ve como en el teléfono.

## 2. Instalarla en el teléfono sin APK (opcional)

Si subes la carpeta `www/` a cualquier hosting con https, Chrome en Android ofrece "Instalar app" y queda con su icono, a pantalla completa y sin conexión.

## 3. Sacar el APK sin instalar nada (recomendado)

1. Crea un repositorio en GitHub y sube todo este proyecto (incluida la carpeta oculta `.github`).
2. En GitHub entra a **Actions**, elige **Compilar APK** y pulsa **Run workflow**.
3. A los pocos minutos descarga `censo-carga-apk` (contiene `app-debug.apk`).
4. Cópialo al teléfono, ábrelo y permite instalar desde esa fuente.

## 4. Sacar el APK en tu computador

Necesitas Node 20 o superior, Java 21 y Android Studio (trae el SDK).

```
npm install
npx cap add android
npm run assets
npx cap sync android
npm run apk
```

El APK queda en `android/app/build/outputs/apk/debug/app-debug.apk`.
También puedes abrir el proyecto con `npm run open` y usar Build > Build APK en Android Studio.

## 5. Publicar o repartir una versión firmada

El APK de arriba es de depuración: sirve para instalar y repartir, pero no para Google Play.
Para una versión final en Android Studio usa Build > Generate Signed Bundle / APK, crea tu llave (keystore) y **guárdala bien**: sin ella no podrás actualizar la app.

## Cosas para ajustar antes de la versión final

- **Nombre y paquete:** el identificador `com.censocarga.app` y el nombre están en `capacitor.config.json`. Cámbialos antes de crear el proyecto Android (después es más trabajoso).
- **Acometida mínima:** si la carga calculada pide menos de 60 A, la app recomienda breaker de 60 A y el cable que corresponde a ese breaker en la tabla (60 A = #6 AWG cobre 75 °C; neutro igual, tierra #10) y lo indica en pantalla y en el PDF. El breaker mínimo se cambia en la constante `MIN` de `www/index.html`; el cable sale de la tabla `WIRE` (ampacidad a 75 °C, con #12 limitado a 20 A y #10 a 30 A). Confirma estos valores en el texto de la norma vigente.
- **Tablas de cálculo:** los factores de potencia de ejemplo, las tablas de breaker, cable y tierra, y el criterio (breaker al 125 %, todo calculado a 220 V, sin factores de demanda) son de referencia. Hay que contrastarlos con la normativa que uses. Están al inicio del código de `www/index.html` (`BRK`, `WIRE`, `GND`).
- **Datos:** viven en el almacenamiento del teléfono. Si se desinstala la app o se borran sus datos, se pierden. Un respaldo (exportar/importar) es una mejora recomendada.
- **Enviar e imprimir:** en el teléfono abren el menú de compartir de Android con el PDF adjunto; allí eliges Gmail, WhatsApp o imprimir. La app no puede forzar un destinatario ni una aplicación concreta.
- **No probado en un teléfono real:** la app se probó en un navegador con pantalla de móvil. La compilación del APK y el menú de compartir de Android hay que verificarlos en un equipo real.
