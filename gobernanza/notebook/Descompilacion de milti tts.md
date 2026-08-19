Tienes `jadx` instalado (CLI, sin GUI).

## Requisito previo: `JAVA_HOME`

`jadx` es un script que necesita Java. En tu Termux no está exportado, así que hay que definirlo antes de cada uso (o añadirlo a `.zshrc`):

```bash
export JAVA_HOME=/data/data/com.termux/files/usr/lib/jvm/java-21-openjdk
```

Para que sea permanente:

```bash
echo 'export JAVA_HOME=/data/data/com.termux/files/usr/lib/jvm/java-21-openjdk' >> ~/.zshrc
```

## Comandos de descompilación

Tu APK está en `~/.bildung/multi-tts/multi-tts_1.8.0.apk`. Todos los comandos van desde el directorio del proyecto.

### 1. Descompilación completa (código + recursos)

```bash
cd ~/.bildung/multi-tts
jadx -d tmp/apk/out multi-tts_1.8.0.apk
```

**Qué hace:** descompila **todo** — bytecode a Java (`.java` en `sources/`) y recursos (manifiest, layouts, XML en `resources/`). Es lo que hiciste antes con `out/`.

### 2. Descompilación con código "malo" visible (la que me interesa)

```bash
cd ~/.bildung/multi-tts
jadx -d tmp/apk/outbad --show-bad-code multi-tts_1.8.0.apk
```

**Qué hace:** igual que la anterior, pero **`--show-bad-code`** hace que jadx **no oculte** los métodos que no pudo descompilar limpiamente (en vez de dejarlos vacíos, muestra el bytecode/marcadores). Útil para la clase `A6/d.java` ofuscada, porque así ves el `switch` de rutas aunque esté parcialmente roto.

### 3. Solo el código, sin recursos (más rápido, menos output)

```bash
cd ~/.bildung/multi-tts
jadx -d tmp/apk/src-only --no-res --show-bad-code multi-tts_1.8.0.apk
```

**Qué hace:** `--no-res` **no descompila recursos** (manifiest/XML), solo el código Java. Genera mucho menos output y es más rápido. Es la que recomiendo para el análisis de clases, ya que los recursos no te interesan para el switch de rutas.

### 4. Solo los recursos, sin código (si quieres el manifiest/strings)

```bash
cd ~/.bildung/multi-tts
jadx -d tmp/apk/res-only --no-src multi-tts_1.8.0.apk
```

**Qué hace:** `--no-src` **no descompila código**, solo extrae recursos. Útil para ver `AndroidManifest.xml` (permisos, servicios declarados) y `strings.xml`.

### 5. Una sola clase (lo más rápido, para el switch de rutas)

Si solo te interesa `A6/d.java` (el servidor), descompila **una sola clase**:

```bash
cd ~/.bildung/multi-tts
jadx --single-class A6.d --show-bad-code --single-class-output tmp/apk/A6d.java multi-tts_1.8.0.apk
```

**Qué hace:** `--single-class A6.d` descompila **solo esa clase** y la vuelca en `tmp/apk/A6d.java` (o un directorio si se indica `--single-class-output` a un dir). Es lo más eficiente para el objetivo actual.

### 6. Ajustar hilos (opcional, rendimiento)

```bash
jadx -d tmp/apk/outbad --show-bad-code -j 8 multi-tts_1.8.0.apk
```

**Qué hace:** `-j 8` usa 8 hilos (default 4). En un móvil con 8 núcleos acelera la descompilación. Solo afecta velocidad, no resultado.

### 7. (Opcional) Exportar como proyecto Gradle

```bash
jadx -d tmp/apk/gradle -e --show-bad-code multi-tts_1.8.0.apk
```

**Qué hace:** `-e` exporta como **proyecto Gradle** recompilable, no solo fuentes sueltas. Útil si quisieras recompilar/modificar la APK. Para tu objetivo (entender el servidor) **no hace falta**.

---

## Ejemplo

Para documentar el `switch (path.hashCode())` de las rutas del servidor, lo mínimo y más rápido es:

```bash
cd ~/.bildung/multi-tts
export JAVA_HOME=/data/data/com.termux/files/usr/lib/jvm/java-21-openjdk
jadx --single-class A6.d --show-bad-code --single-class-output tmp/apk/A6d.java multi-tts_1.8.0.apk
```

Esto te genera `tmp/apk/A6d.java` con la clase del servidor, sin descompilar los 4300 archivos de nuevo.
