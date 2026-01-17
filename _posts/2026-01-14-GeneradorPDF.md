---
title: GeneradorPDF
date: 2026-01-14 22-57-00 -0400
categories: [Desarollo, Python ]
tags: [post]     # TAG names should always be lowercase
---

# Generador de PDF con Python

Bueno, para este proyecto vi la idea de crear un **generador de PDF** y la verdad me gustó bastante. Es una alternativa para crear PDFs sin tener que ir a Word, Google Docs, etc.  
Suena bien, ¿no? Pero… ¿cómo está creado?

Eso es justamente lo que estaré explicando brevemente en este post, para que tú también puedas recrearlo e incluso mejorarlo.

Lo primero que debemos hacer es abrir nuestro IDE. En este caso usaré **PyCharm**. Luego, debemos importar (e instalar) las librerías que estaremos utilizando:

```python

import streamlit as st
import fpdf as FPDF
import os 

```

Bien, ahora toca instalar las librerías Streamlit y FPDF con el siguiente comando, el cual ejecutaremos en la terminal (y ojo 👀, asegurándonos de tener el entorno virtual activado):

-pip install streamlit fpdf

Ya con las librerías importadas e instaladas, podemos pasar a lo bueno… ¡el código! 🚀
Primero crearemos una clase llamada PDF, la cual heredará de FPDF. En esta clase estaremos creando cuatro funciones: header, footer, page_title y page_body.

En estas funciones definimos los atributos que tendrán los encabezados, pies de página, textos y títulos, como la fuente, el tamaño, los bordes, etc.

![clase PDF](/assets/imagenes/generador_pdf/PDF.png)


Luego de tener esta clase que define los estilos del documento, pasamos a la función encargada de crear el PDF. Aquí asignamos los atributos generales, como el título del documento, el número de páginas, y utilizamos un ciclo para ir generándolas una por una.

![clase crear PDF](/assets/imagenes/generador_pdf/CrearPDF.png)

Para finalizar, creamos la función main(), donde se construye toda la parte visual del proyecto utilizando la librería Streamlit.
Aquí definimos los inputs (entradas de datos) para que el usuario pueda ingresar la información que llevará el PDF.

Iteramos según el número de páginas indicadas, recolectamos los datos y los almacenamos en una tupla llamada pages, la cual luego pasamos como parámetro a la función que crea el PDF. Finalmente, agregamos un formulario que permite generar el archivo, asignarle un nombre y descargarlo.

```python

# ---------- STREAMLIT APP ----------
def main():
    st.title("Generador de PDF por páginas")

    document_title = st.text_input("Título del documento")

    st.header("Páginas del documento")

    pages = []
    page_count = st.number_input("Número de páginas", 1, 10, 1)

    for i in range(page_count):
        st.subheader(f"Página {i + 1}")

        title = st.text_input(f"Título página {i + 1}", key=f"title{i}")
        content = st.text_area(f"Contenido página {i + 1}", key=f"content{i}")

        uploaded_image = st.file_uploader(
            f"Imagen página {i + 1} (opcional)",
            type=["jpg", "png"],
            key=f"image{i}"
        )

        image_size = st.slider(
            f"Tamaño de imagen (%) página {i + 1}",
            20, 100, 50,
            key=f"img_size{i}"
        )

        font = st.selectbox(
            f"Fuente página {i + 1}",
            ["Arial", "Courier", "Times"],
            key=f"font{i}"
        )

        size = st.slider(
            f"Tamaño fuente página {i + 1}",
            8, 24, 12,
            key=f"size{i}"
        )

        image_path = None
        if uploaded_image:
            # Guarda la imagen temporalmente para que FPDF pueda usarla
            image_path = f"page_image_{i}_{uploaded_image.name}"
            with open(image_path, "wb") as f:
                f.write(uploaded_image.getbuffer())

        # Guardamos toda la info de la página en una tupla
        pages.append((title, content, font, size, image_path, image_size))

```

```python

 # ---------- FORMULARIO FINAL ----------
    with st.form("form_pdf"):
        nombre_pdf = st.text_input("Nombre del PDF")
        generar = st.form_submit_button("Generar PDF")

    if generar:
        if not nombre_pdf:
            st.error("Ingrese un nombre para el PDF")
            return

        if not nombre_pdf.lower().endswith(".pdf"):
            nombre_pdf += ".pdf"

        create_pdf(nombre_pdf, document_title, pages)

        with open(nombre_pdf, "rb") as pdf_file:
            st.download_button(
                "Descargar PDF",
                pdf_file,
                file_name=nombre_pdf,
                mime="application/pdf"
            )

        st.success("PDF generado correctamente")

        # Evita archivos basura
        if os.path.exists(nombre_pdf):
            os.remove(nombre_pdf)


if __name__ == "__main__":
    main()
```

### Aprendizajes

Este proyecto me permitió reforzar conceptos importantes como:

- Programación orientada a objetos en Python  
- Uso de librerías externas para la generación de documentos  
- Manejo de archivos e imágenes  
- Creación de interfaces interactivas con Streamlit  

Es un ejemplo práctico de cómo Python puede utilizarse no solo para scripts, sino también para construir herramientas útiles con una experiencia de usuario amigable.

[Ver proyecto](https://pdf-converter8.streamlit.app/)



