
## ¿Qué es EC2?

**Amazon EC2 (Elastic Compute Cloud)** es un servicio de cómputo en la nube que proporciona **capacidad de procesamiento escalable**. Permite lanzar servidores virtuales (llamados **instancias**) para ejecutar aplicaciones sin necesidad de gestionar infraestructura física. Con EC2, solo pagas por el tiempo en que tus instancias están en uso, y puedes ajustar la capacidad en función de tus necesidades.

---

## **¿Cómo funciona EC2?**

- Las **instancias EC2** son servidores virtuales que se ejecutan en la infraestructura de AWS.

- Puedes elegir entre diferentes tipos de instancias, optimizados para tareas específicas, como procesamiento general, computación intensiva, o memoria optimizada.

- AWS te permite lanzar instancias bajo diferentes modelos de pago: **On-Demand**, **Reserved Instances**, **Savings Plans** o **Spot Instances**.

  

---

  

## **Tipos de instancias EC2**

  
![[Pasted image 20260324191356.png]]
  

---

  

### **Ejemplos de Tipos de Instancias**

  

1. **General Purpose (t2, t3, t4g)**

   - Balance entre CPU, memoria y almacenamiento.

   - Ideal para servidores web, entornos de pruebas y aplicaciones de baja latencia.

   - **Ejemplo:** `t3.micro` (usado en el nivel gratuito de AWS).

  

2. **Compute Optimized (c5, c6g)**

   - Optimizadas para alto rendimiento de CPU.

   - Usadas para simulaciones científicas, videojuegos y servidores de alta carga.

   - **Ejemplo:** `c5.xlarge`.

  

3. **Memory Optimized (r5, r6g)**

   - Ofrecen más memoria por núcleo que otras familias.

   - Perfectas para bases de datos grandes y análisis en tiempo real.

   - **Ejemplo:** `r5.large`.

  

4. **Storage Optimized (i3, i4g, d2)**

   - Diseñadas para aplicaciones que requieren operaciones rápidas de lectura/escritura.

   - Comúnmente usadas en bases de datos NoSQL o almacenamiento distribuido.

   - **Ejemplo:** `i3.4xlarge`.

  

5. **Accelerated Computing (p4, g5, f1)**

   - Equipadas con GPU o FPGA para cargas de trabajo especializadas.

   - Utilizadas en inteligencia artificial, aprendizaje profundo y renderización.

   - **Ejemplo:** `p4d.24xlarge`.

  

---

  

## **Modelos de Compra**

- **On-Demand:** Paga por hora/segundo sin compromisos a largo plazo.  

- **Reserved Instances:** Ahorros significativos al reservar instancias por 1 o 3 años.  

- **Savings Plans:** Compromiso de gasto para obtener descuentos en EC2 y otros servicios.  

- **Spot Instances:** Adquiere capacidad no utilizada a menor costo, pero las instancias pueden ser interrumpidas.

  

---

  

## **Casos de Uso Comunes**

- **Aplicaciones web escalables** que necesitan adaptarse a la demanda del tráfico.

- **Bases de datos** y almacenamiento distribuido.

- **Análisis intensivo de datos** con procesamiento paralelo.

- **Entrenamiento de modelos de Machine Learning** con instancias aceleradas por GPU.

  

---

  

## **Más Información**

- [Documentación Oficial de EC2](https://docs.aws.amazon.com/ec2)

- [Precios de EC2](https://aws.amazon.com/ec2/pricing/)

- [Tipos de Instancias EC2](https://aws.amazon.com/ec2/instance-types/)

- [Calculadora Ec2](https://calculator.aws/#/addService)

  

-----------------------------------------------------------------------------------------

# Ideas Clave 


1. Se puede ajustar la opción de acuerdo el uso directo del tiempo ¿Qué tanto tiempo se utilizan las redes?
2. Se clonan los discos duros antes de sacar del rack
3. ¿Qué pasa si sobrepasa su capacidad? Desmontar virtualmente dentro de los servidores de aws.
4. La ec2, nos permite ya no usar la parte fisica, si no la renta de un espacio virtual
	1. Solo se paga en demanda o Bajo demanda
	2. On premise/ In House (Este servicio se tiene en casa)
	3. Cuando algo falla se va on Premise 
	4. Dentro de una EC2 hay una apartado de monitoreo, red , cpu, errores, y otros kpi´s (La que siempre importa es la del CPU)
5. Planes de ahorro: Aws se maneja por planes de ahorro
6. Se maneja por regiones, virginia, ohio, central. Mientras mas lejos esta mas latencia 

## Instanciar en EC2

1. Dashboard

![[Pasted image 20260324190805.png]]


2. Lanzar instancia 

EC2 > Instancias 

![[Pasted image 20260324194707.png]]

3. Launch Instance 

![[Pasted image 20260324194731.png]]

4. Colocar nombre y seleccionar los tipos de codigos 

![[Pasted image 20260324194937.png]]

5. Utilizaremos la de Amazon linux


![[Pasted image 20260324195212.png]]

6. Generar llave nueva 
![[Pasted image 20260324195417.png]]


![[Pasted image 20260324195452.png]]


Checar este campo

![[Pasted image 20260324195641.png]]

![[Pasted image 20260324195652.png]]



![[Pasted image 20260324195942.png]]

Lanzar instancia 

![[Pasted image 20260324200014.png]]


![[Pasted image 20260324200038.png]]

Clic en la instancia, luego connect 


![[Pasted image 20260324200129.png]]


Proceso de conexion 


![[Pasted image 20260324200223.png]]


Termino de conexion


![[Pasted image 20260324200255.png]]


Aplicar este codigo 


![[Pasted image 20260324200312.png]]



Delete instance




# Ideas cluster 

[[A doc project]]

