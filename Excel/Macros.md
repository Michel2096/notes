
Un macro en Excel es una secuencia de instrucciones grabadas o escritas en código que Excel ejecuta automáticamente. La idea es simple: si realizas una sección repetidamente se puede 

	Formatear un reporte
	Copiar datos de una hoja a otra
	Limpiar columnas vacias

Es como programar un archivo Excel para que lo haga una y otra vez. 

### ¿Cómo funciona una macro por dentro?

Las macros en Excel están escritas en **VBA** (Visual Basic for Applications).

	Es un tipo de lenguaje de programacion propio de Microsoft Office. Cuando grabas una macro con la herramienta de grabacion de Excel, lo que realmente pasa es que Excel traduce cada clic y accion que haces VBA automaticamete. 

**Editor de VBA** (se abre con `Alt + F11`).


```
Sub MiPrimeraMacro()
    Range("A1").Value = "Hola Excel"
    Range("A1").Font.Bold = True
End Sub
```


Los macros son especialmente útiles para tareas que son repetitivas, largas o propensas a error humano. Algunos muy comunes en el trabajo diario son:

	Automatizacion de reportes
	Limpieza de datos 
	Mover y consolidar informacion: Hacer resumenes 
	Validaciones y alertas 
	Botones de accion 

![[Pasted image 20260329014906.png]]

#Ejemplo 

Este es un ejemplo de código de Macro.

```
Sub NombreDeLaMacro()
    ' Aquí va el código
    ' Los apóstrofes son comentarios, Excel los ignora

    Dim nombre As String          ' Declarar una variable
    nombre = "Juan"               ' Asignar valor

    If nombre = "Juan" Then       ' Condición
        MsgBox "Hola, Juan"
    End If

    For i = 1 To 10               ' Bucle: repite 10 veces
        Cells(i, 1).Value = i     ' Escribe 1,2,3... en columna A
    Next i

End Sub
```

[[Macros]]