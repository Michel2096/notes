

Excel admite una gran variedad de formatos en archivo, cada uno diseñado para un proposito espescifico. A grandes rasgos se dividen en tres familias: formatos nativos de Excel, formatos de texto plano, y formatos de intercambio/compatibilidad.

## Formatos nativos de Excel


	.XLSX

**Libro de EXCEL (OPEN XML)**: Es el formato estándar desde Excel 2007. 
Internamente es un archivo ZIP que contiene archivos XML. Admite múltiples hojas, formulas, gráficos, imágenes, tablas dinámicas, formatos condicionales y casi todas las funciones modernas de Excel. No admite macros VBA por razones de seguridad.

	.XLSM

**Libro habilitado para macros**: Idéntico al .XLSX pero con soporte para macros escritas en VBA (Visual Basic for Applications). Cunado guardas n archivo con macros en formato .XLSX. Excel advierte que se perderan; debes usar .xlsm para conservarlas. 

	.XLSB

**Libro binario de Excel**: Almacena los datos en formato binario en lugar de XML. Es mucho más rápido de abrir, guardar y procesar, especialmente con archivos muy grandes (más de 100 MB). Admite macros. Su desventaja es que no es tan fácil de leer con otras herramientas fuera del ecosistema Microsoft.

	.XLS

**Libro Excel 97-2003 (Formato legado)**: El formato antiguo de Excel, anterior a la era Open XML. Tiene un límite de 65,536 filas y 256 columnas, contra el millón de filas y 16,384 columnas del `.xlsx`. Se sigue utilizando para compatibilidad con sistemas muy antiguos, pero ya no es recomendable como formato de trabajo.


	.XLTX


**Plantilla de Excel**: Una plantilla sin macros. Al abrirlo, Excel crea un libro nuevo basado en esa plantilla sin modificar el archivo original. Útil para reportes recurrentes con diseño predefinido.

	.XLTM

**Plantilla habilitada para macros**: Igual que .xltx pero admite macros de VBA en la plantilla


	.xlam

**Complemento en Excel** Archivo especial que se encarga como un complemento add-in para agregar funciones personalizados o herramientas al meni Excel.


![[Pasted image 20260329020432.png]]

---

# Restantes 

**`.csv` — Valores separados por comas (Comma-Separated Values)** Quizás el formato más universal en el mundo de los datos. Cada fila del archivo corresponde a una fila en la hoja, y los valores se separan por comas (o punto y coma, dependiendo de la configuración regional). Solo guarda una hoja, no tiene fórmulas ni formatos visuales. Es el estándar para importar y exportar datos entre Excel, bases de datos, Python, R, sistemas ERP y prácticamente cualquier software que maneje tablas.

```
Nombre,Edad
Juan,20
Ana,22
```

**`.txt` — Texto delimitado por tabulaciones** Similar al CSV pero usa tabulaciones como separador en lugar de comas. Era más común antes de que el CSV se estandarizara. Útil para sistemas que interpretan las comas como parte del texto (por ejemplo, descripciones de productos que contienen comas).

**`.prn` — Texto con espacios (Formatted Text)** Un formato aún más antiguo donde las columnas se separan por espacios para simular alineación visual. Prácticamente en desuso, pero Excel aún lo admite para compatibilidad con sistemas mainframe antiguos.

---

### Formatos de intercambio y compatibilidad

**`.ods` — OpenDocument Spreadsheet** El formato estándar abierto ISO para hojas de cálculo, usado nativamente por LibreOffice Calc y Google Sheets. Excel puede abrir y guardar en `.ods`, aunque con algunas pérdidas de compatibilidad en funciones avanzadas. Es la opción correcta cuando necesitas que el archivo funcione perfectamente fuera del ecosistema Microsoft.

**`.pdf` — Portable Document Format** Excel puede exportar cualquier hoja a PDF directamente desde "Guardar como" o "Exportar". El PDF congela el aspecto visual del archivo pero ya no es editable como hoja de cálculo. Es el formato ideal para reportes finales, estados de cuenta o cualquier documento que deba verse igual en cualquier dispositivo.

**`.xml` — XML Spreadsheet 2003** Un formato XML que existía antes del Open XML moderno (`.xlsx`). Muy pocas veces se necesita hoy en día, pero sigue estando disponible para compatibilidad con sistemas que consumen XML con esa estructura específica.




[[Excel]]