# Instagram-URL-reels-scrapper
URL reels scrapper

## ¿Qué hace?
Este script permite extraer las direcciones URL de cualquier perfil de instagram, al final de procesamiento, genera un archivo TXT con todas las URL encontradas

## Tutorial de uso
1. Accede al perfil del usuario en su sección de reels 'https://www.instagram.com/{USER}/reels/'
2. En tu navegador web (Chrome/Edge) presiona F12 y en entra en CONSOLE
3. Copia y pega el siguiente script. Al terminar, el script genera un archivo TXT con todas las URL encontradas

### COPY AND PASTE into de web Console:
```
(async () => {
    const reels = new Set();
    const esperar = ms =>
        new Promise(resolve => setTimeout(resolve, ms));
    function extraerReels() {
        // Buscar directamente cualquier URL de Reel
        // dentro del HTML actual
        const html = document.documentElement.innerHTML;
        const encontrados = html.match(
            /https?:\/\/(?:www\.)?instagram\.com\/reel\/[A-Za-z0-9_-]+/g
        ) || [];
        // También buscar rutas relativas
        const relativos = html.match(
            /\/reel\/[A-Za-z0-9_-]+/g
        ) || [];
        encontrados.forEach(url => {
            reels.add(
                url.replace(
                    /^https?:\/\/(?:www\.)?instagram\.com/,
                    "https://www.instagram.com"
                )
            );
        });
        relativos.forEach(path => {
            reels.add(
                "https://www.instagram.com" +
                path
            );
        });
    }
    console.clear();
    console.log(
        "======================================"
    );
    console.log(
        " INSTAGRAM REELS COLLECTOR"
    );
    console.log(
        "======================================"
    );
    console.log("");
    let anterior = 0;
    let sinNuevos = 0;
    let scroll = 0;
    while (sinNuevos < 10) {
        extraerReels();
        const total = reels.size;
        const nuevos =
            total - anterior;
        if (nuevos === 0) {
            sinNuevos++;
        } else {
            sinNuevos = 0;
        }
        anterior = total;
        scroll++;
        console.log(
            `Scroll ${scroll} | ` +
            `Reels: ${total} | ` +
            `Nuevos: ${nuevos} | ` +
            `Sin nuevos: ${sinNuevos}/10`
        );
        window.scrollTo(
            0,
            document.body.scrollHeight
        );
        await esperar(4000);
    }
    // Última extracción
    extraerReels();
    const resultado = [...reels];
    console.log("");
    console.log(
        "======================================"
    );
    console.log(
        `TOTAL ENCONTRADO: ${resultado.length}`
    );
    console.log(
        "======================================"
    );
    console.log("");
    console.log(
        resultado.join("\n")
    );
    // Crear archivo TXT
    const contenido =
        resultado.join("\r\n");
    const blob =
        new Blob(
            [contenido],
            {
                type:
                    "text/plain;charset=utf-8"
            }
        );
    const blobURL =
        URL.createObjectURL(blob);
    const enlace =
        document.createElement("a");
    enlace.href = blobURL;
    enlace.download =
        "reels.txt";
    document.body.appendChild(
        enlace
    );
    enlace.click();
    enlace.remove();
    URL.revokeObjectURL(
        blobURL
    );
    console.log("");
    console.log(
        "✓ reels.txt descargado"
    );
    })();
```
