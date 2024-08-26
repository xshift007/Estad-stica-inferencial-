
# Resumen de Métodos Robustosos y Ejemplo Complejo en R

Este documento resume los principales conceptos de métodos robustos para el análisis estadístico y proporciona un ejemplo complejo en R que ilustra su aplicación práctica.

## Métodos Robustosos

Los métodos robustos se utilizan cuando los datos no cumplen con las condiciones necesarias para los procedimientos estadísticos tradicionales. Estos métodos son menos sensibles a valores atípicos, distribuciones asimétricas, o tamaños de muestra pequeños.

### **Alternativas Robustas a la Media**

1. **Media Truncada**:
   - Se calcula eliminando un porcentaje específico de los valores en ambos extremos del conjunto de datos.
   - Ejemplo en R:
     ```r
     x <- c(5, 20, 37, 38, 40, 43, 43, 45, 87, 91)
     media_truncada <- mean(x, trim = 0.2)
     ```

2. **Media Winsorizada**:
   - En lugar de descartar valores extremos, estos se reemplazan por los valores más cercanos que no serían descartados.
   - Ejemplo en R:
     ```r
     library(WRS2)
     media_winsorizada <- winmean(x, tr = 0.2)
     ```

### **Prueba de Yuen para dos Muestras Independientes**

La prueba de Yuen es una alternativa a la prueba t de Student cuando las varianzas son diferentes o los tamaños de las muestras son muy dispares.

- **Fórmula del estadístico**:
  \[
  T_y = rac{\overline{X}_{t1} - \overline{X}_{t2}}{\sqrt{d_1 + d_2}}
  \]
  donde \( \overline{X}_{t1} \) y \( \overline{X}_{t2} \) son las medias truncadas de cada muestra.

- **Ejemplo en R**:
  ```r
  library(WRS2)
  prueba <- yuen(tiempo ~ algoritmo, data = datos, tr = 0.2)
  ```

### **Prueba de Yuen para dos Muestras Pareadas**

Este método compara las medias truncadas de dos muestras apareadas, similar a la prueba de Yuen para muestras independientes, pero adaptado a muestras dependientes.

- **Fórmula del estadístico**:
  \[
  T_y = rac{\overline{X}_{t1} - \overline{X}_{t2}}{\sqrt{d_1 + d_2 - 2d_{12}}}
  \]

- **Ejemplo en R**:
  ```r
  prueba <- yuend(x = X, y = Y, tr = 0.2)
  ```

### **Comparaciones de una Vía para Múltiples Grupos Independientes**

Para ANOVA de una vía con muestras independientes cuando no se cumple la homocedasticidad o hay tamaños muestrales diferentes.

- **Ejemplo en R**:
  ```r
  medias_truncadas <- t1way(tiempo ~ algoritmo, data = datos, tr = 0.2)
  ```

### **Comparaciones de una Vía para Múltiples Grupos Correlacionados**

Para ANOVA de una vía con muestras correlacionadas que no cumplen con la condición de esfericidad.

- **Ejemplo en R**:
  ```r
  prueba <- rmanova(y = datos$tiempo, groups = datos$algoritmo, blocks = datos$instancia, tr = 0.2)
  ```

## Ejemplo Complejo en R

Este ejemplo combina varios de los métodos robustos descritos anteriormente. Suponemos que estamos comparando el rendimiento de tres algoritmos diferentes (A, B, y C) en términos de tiempo de ejecución (en milisegundos) en diferentes instancias de un problema. Estos datos presentan algunos valores atípicos y no cumplen con la suposición de normalidad y homocedasticidad.

### **Paso 1: Generación de Datos**

Vamos a simular los tiempos de ejecución para los tres algoritmos, incluyendo algunos valores atípicos.

```r
set.seed(123)

# Generación de datos con valores atípicos
A <- c(rnorm(30, mean = 25, sd = 2), 100, 105)  # Algoritmo A con outliers
B <- c(rnorm(30, mean = 26, sd = 2), 110, 115)  # Algoritmo B con outliers
C <- c(rnorm(30, mean = 27, sd = 2), 120, 125)  # Algoritmo C con outliers

# Crear data frame
tiempo <- c(A, B, C)
algoritmo <- rep(c("A", "B", "C"), each = 32)
datos <- data.frame(tiempo, algoritmo)
```

### **Paso 2: Visualización Inicial**

Es útil visualizar los datos antes de aplicar los métodos robustos.

```r
library(ggplot2)

# Boxplot para visualizar los valores atípicos
ggplot(datos, aes(x = algoritmo, y = tiempo)) +
  geom_boxplot() +
  theme_minimal() +
  labs(title = "Boxplot de Tiempos de Ejecución por Algoritmo",
       y = "Tiempo (ms)", x = "Algoritmo")
```

### **Paso 3: Comparación de los Algoritmos Usando Métodos Robustosos**

Usaremos el procedimiento `t1way()` para realizar un ANOVA robusto con medias truncadas y `lincon()` para el análisis post-hoc.

```r
library(WRS2)

# ANOVA robusto usando medias truncadas
gamma <- 0.2  # Nivel de poda
anova_robusta <- t1way(tiempo ~ algoritmo, data = datos, tr = gamma)

# Resultado del ANOVA robusto
print(anova_robusta)

# Procedimiento post-hoc
post_hoc <- lincon(tiempo ~ algoritmo, data = datos, tr = gamma)
print(post_hoc)
```

### **Paso 4: Comparación de Algoritmos Usando Bootstrap**

Para aumentar la robustez, usaremos `t1waybt()` que incorpora bootstrapping y `mcppb20()` para el análisis post-hoc.

```r
# ANOVA robusto con bootstrapping
set.seed(123)
bootstrap_anova <- t1waybt(tiempo ~ algoritmo, data = datos, tr = gamma, nboot = 1000)

# Resultado del ANOVA con bootstrapping
print(bootstrap_anova)

# Procedimiento post-hoc con bootstrapping
post_hoc_boot <- mcppb20(tiempo ~ algoritmo, data = datos, tr = gamma, nboot = 1000)
print(post_hoc_boot)
```

### **Paso 5: Interpretación de Resultados**

Al final del análisis, deberíamos interpretar los resultados obtenidos de las pruebas ANOVA robustas y los análisis post-hoc. Estos resultados nos indicarán si hay diferencias significativas en los tiempos de ejecución promedio entre los algoritmos, y si es así, entre qué pares de algoritmos estas diferencias son significativas.

### **Conclusión**

Este ejemplo ilustra cómo manejar datos problemáticos que no cumplen con los supuestos clásicos de normalidad y homocedasticidad usando métodos robustos. Al final, el uso de la ANOVA robusta y las pruebas post-hoc nos permite hacer comparaciones válidas y confiables entre los algoritmos, incluso en presencia de valores atípicos y otras irregularidades en los datos.

Este enfoque es especialmente útil en situaciones del mundo real, donde los datos pueden estar contaminados o no cumplir con las suposiciones ideales de los métodos estadísticos tradicionales.
