
# Descargar enlaces TVG

Este repositorio contiene un script en Python para automatizar la extracción de enlaces de la página de transparencia de la CRTVG, específicamente de la sección de contratos y convenios de contenidos audiovisuales.

## Requisitos

1. Tener instalado `Selenium`.
   Para instalar Selenium, usa el siguiente comando:
   ```
   pip install selenium
   ```

2. Tener instalado `ChromeDriver` y asegurarse de que está en la ruta correcta. Puedes descargar `ChromeDriver` desde [aquí](https://sites.google.com/a/chromium.org/chromedriver/downloads).
   Asegúrate de que el archivo ejecutable esté en `/usr/bin/chromedriver`.

## Descripción del Script

Este script recorre 16 páginas de la web de transparencia de CRTVG y extrae los enlaces de los contenidos audiovisuales listados. Los enlaces extraídos se guardan en un archivo de texto llamado `enlaces.txt`.

### Funcionalidad principal

El script realiza lo siguiente:
1. Accede a cada una de las 16 páginas.
2. Extrae los enlaces a los contenidos audiovisuales a partir de los códigos y títulos listados.
3. Almacena los enlaces en un archivo de texto plano.

### Estructura del Código

```python
from selenium import webdriver
from selenium.webdriver.chrome.service import Service
from selenium.webdriver.common.by import By
import time

# Ruta a tu chromedriver
service = Service('/usr/bin/chromedriver')
driver = webdriver.Chrome(service=service)

# Lista de URLs de las páginas con los contratos (todas las 16 páginas)
urls_paginas = [
    "https://crtvg.gal/gl/crtvg/portal-de-transparencia/actividade-corporativa/contratos-e-convenios/contidos-audiovisuais/actividade-contractual-2021?p_p_id=com_liferay_asset_publisher_web_portlet_AssetPublisherPortlet_INSTANCE_ejzt&p_p_lifecycle=0&p_p_state=normal&p_p_mode=view&_com_liferay_asset_publisher_web_portlet_AssetPublisherPortlet_INSTANCE_ejzt_delta=20&p_r_p_resetCur=false&_com_liferay_asset_publisher_web_portlet_AssetPublisherPortlet_INSTANCE_ejzt_cur=1",
    ...
    # Aquí se incluyen todas las 16 URLs proporcionadas
    "https://crtvg.gal/gl/crtvg/portal-de-transparencia/actividade-corporativa/contratos-e-convenios/contidos-audiovisuais/actividade-contractual-2021?p_p_id=com_liferay_asset_publisher_web_portlet_AssetPublisherPortlet_INSTANCE_ejzt&p_p_lifecycle=0&p_p_state=normal&p_p_mode=view&_com_liferay_asset_publisher_web_portlet_AssetPublisherPortlet_INSTANCE_ejzt_cur=16"
]

# Lista para almacenar los enlaces extraídos
enlaces = []

# Función para extraer los enlaces de cada página
def extraer_enlaces(url):
    print(f"Accediendo a: {url}")
    driver.get(url)
    time.sleep(2)  # Espera para asegurarse de que la página cargue completamente

    try:
        # Buscar los códigos y títulos en la tabla
        filas = driver.find_elements(By.CSS_SELECTOR, 'table tr')

        for fila in filas:
            # Extrae el código del título para formar la URL
            columnas = fila.find_elements(By.TAG_NAME, 'td')
            if len(columnas) > 0:
                titulo_codigo = columnas[0].text.strip()
                titulo = columnas[1].text.strip().lower().replace(' ', '-').replace("'", "")
                enlace = f"https://crtvg.gal/gl/web/crtvg/w/{titulo_codigo}-{titulo}"
                enlaces.append(enlace)
                print(f"Encontrado enlace: {enlace}")

    except Exception as e:
        print(f"Error extrayendo enlaces de la página: {e}")

# Recorrer todas las URLs y extraer enlaces
for url in urls_paginas:
    extraer_enlaces(url)

# Guardar los enlaces en un archivo
with open("enlaces.txt", "w") as archivo:
    for enlace in enlaces:
        archivo.write(enlace + "\n")

print("Todos los enlaces han sido extraídos y guardados en enlaces.txt")

# Cerrar el navegador
driver.quit()
```

### Archivos generados

- `enlaces.txt`: Contiene los enlaces extraídos de las páginas de contratos de CRTVG.

### Ejecución

1. Coloca el archivo en una carpeta local.
2. Ejecuta el script con:
   ```
   python3 extraer_enlaces.py
   ```
3. Se generará un archivo `enlaces.txt` con todos los enlaces obtenidos.

## Notas

Este script depende de la estructura actual de la web de CRTVG, por lo que podría requerir modificaciones si la estructura de la página cambia en el futuro.
