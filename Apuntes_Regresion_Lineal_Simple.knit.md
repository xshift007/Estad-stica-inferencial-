---
title: "Apuntes sobre Regresión Lineal Simple"
author: "Nicolás"
date: "2024-08-24"
output: html_document
---



## Introducción

La regresión lineal simple es un modelo estadístico que permite entender la relación entre dos variables cuantitativas. Se utiliza para predecir el valor de una variable dependiente (Y) en función de una variable independiente (X).

### Modelo de Regresión Lineal Simple

El modelo de regresión lineal simple se define como:

$$ \hat{y} = \beta_0 + \beta_1x $$

Donde: - $\hat{y}$ es la estimación de la variable dependiente. - $\beta_0$ es la intersección o intercepto. - $\beta_1$ es la pendiente de la recta, que indica el cambio en $\hat{y}$ por cada unidad de cambio en $x$. - $x$ es el valor de la variable independiente.

### Ejemplo en R

Para ilustrar cómo funciona la regresión lineal simple, usaremos el conjunto de datos `mtcars`, que contiene características de diferentes modelos de automóviles.

#### Cargando los datos


``` r
# Cargar el conjunto de datos
data(mtcars)
head(mtcars)
```

```
##                    mpg cyl disp  hp drat    wt  qsec vs am gear carb
## Mazda RX4         21.0   6  160 110 3.90 2.620 16.46  0  1    4    4
## Mazda RX4 Wag     21.0   6  160 110 3.90 2.875 17.02  0  1    4    4
## Datsun 710        22.8   4  108  93 3.85 2.320 18.61  1  1    4    1
## Hornet 4 Drive    21.4   6  258 110 3.08 3.215 19.44  1  0    3    1
## Hornet Sportabout 18.7   8  360 175 3.15 3.440 17.02  0  0    3    2
## Valiant           18.1   6  225 105 2.76 3.460 20.22  1  0    3    1
```

#### Gráfico de Dispersión

Primero, visualizaremos la relación entre dos variables: el peso del automóvil (`wt`) y su rendimiento en millas por galón (`mpg`).


``` r
# Gráfico de dispersión
plot(mtcars$wt, mtcars$mpg, 
     xlab = "Peso (1000 libras)", 
     ylab = "Millas por Galón", 
     main = "Relación entre Peso y Rendimiento")
```

<img src="Apuntes_Regresion_Lineal_Simple_files/figure-html/unnamed-chunk-2-1.png" width="672" />

#### Ajuste del Modelo de Regresión Lineal

Ahora ajustaremos un modelo de regresión lineal para predecir `mpg` en función de `wt`.


``` r
# Ajuste del modelo
modelo <- lm(mpg ~ wt, data = mtcars)

# Resumen del modelo
summary(modelo)
```

```
## 
## Call:
## lm(formula = mpg ~ wt, data = mtcars)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -4.5432 -2.3647 -0.1252  1.4096  6.8727 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept)  37.2851     1.8776  19.858  < 2e-16 ***
## wt           -5.3445     0.5591  -9.559 1.29e-10 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 3.046 on 30 degrees of freedom
## Multiple R-squared:  0.7528,	Adjusted R-squared:  0.7446 
## F-statistic: 91.38 on 1 and 30 DF,  p-value: 1.294e-10
```

El resumen del modelo te proporcionará información sobre los coeficientes $\beta_0$ y $\beta_1$, así como sobre la significancia estadística de estos.

#### Gráfico de la Recta de Regresión

Podemos añadir la recta de regresión al gráfico de dispersión para visualizar el ajuste del modelo.


``` r
# Cargar el conjunto de datos
data(mtcars)

# Gráfico de dispersión
plot(mtcars$wt, mtcars$mpg, 
     xlab = "Peso (1000 libras)", 
     ylab = "Millas por Galón", 
     main = "Relación entre Peso y Rendimiento")

# Ajuste del modelo
modelo <- lm(mpg ~ wt, data = mtcars)

# Añadir la recta de regresión al gráfico
abline(modelo, col = "red", lwd = 2)
```

<img src="Apuntes_Regresion_Lineal_Simple_files/figure-html/unnamed-chunk-4-1.png" width="672" />
### Interpretación de los Resultados

-   **Intersección (**$\beta_0$): El valor de `mpg` cuando `wt` es 0. En este contexto, no es interpretable porque un automóvil no puede tener un peso de 0 libras.
-   **Pendiente (**$\beta_1$): Indica que por cada 1000 libras adicionales en el peso del automóvil, el rendimiento disminuye en un valor aproximado a `-5.34` millas por galón, según los resultados del modelo.

### Validación del Modelo

Es importante validar el modelo para asegurarse de que cumple con los supuestos de la regresión lineal:

#### Residuos


Este gráfico permite verificar si los residuos se distribuyen de manera aleatoria, lo cual es un buen indicio de que el modelo es adecuado.

#### Normalidad de los Residuos


``` r
# Prueba de normalidad de Shapiro-Wilk
shapiro.test(modelo$residuals)
```

```
## 
## 	Shapiro-Wilk normality test
## 
## data:  modelo$residuals
## W = 0.94508, p-value = 0.1044
```

Una prueba de Shapiro-Wilk permitirá evaluar si los residuos siguen una distribución normal.

### Ejercicio Propuesto

1.  **Usa la función `predict()`** para estimar el `mpg` de un automóvil con un peso de 3.5 (1000 libras) usando el modelo ajustado.
2.  **Evalúa el modelo con un conjunto de prueba**: Divide el conjunto de datos en entrenamiento y prueba, ajusta el modelo con los datos de entrenamiento y evalúa su precisión con los datos de prueba.


``` r
# Predicción para un automóvil con peso de 3.5 (1000 libras)
nuevo_peso <- data.frame(wt = 3.5)
predict(modelo, nuevo_peso)
```

```
##        1 
## 18.57948
```


``` r
# División de los datos en entrenamiento y prueba
set.seed(123)
sample <- sample.int(n = nrow(mtcars), size = floor(.7*nrow(mtcars)), replace = F)
entrenamiento <- mtcars[sample, ]
prueba <- mtcars[-sample, ]

# Ajuste del modelo con el conjunto de entrenamiento
modelo_entrenamiento <- lm(mpg ~ wt, data = entrenamiento)

# Predicciones en el conjunto de prueba
predicciones <- predict(modelo_entrenamiento, prueba)

# Error Cuadrático Medio (MSE)
mse <- mean((prueba$mpg - predicciones)^2)
mse
```

```
## [1] 4.567618
```

Este apunte cubre los conceptos clave y proporciona un ejemplo completo en R. Puedes ejecutar cada sección para entender mejor cómo funciona la regresión lineal simple.
