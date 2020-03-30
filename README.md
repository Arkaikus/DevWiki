# Tutorial TensorFlow
Ejercicios para aprender tensorflow con python

## Instalación

```bash
sudo apt update
sudo apt install python3-dev python3-pip
sudo pip3 install -U virtualenv
virtualenv --system-site-packages -p python3 ./venv
source ./venv/bin/activate
#------------------------------------
# (venv) $ pip install --upgrade pip
# (venv) $ pip install --upgrade tensorflow jypterlab
# (venv) $ jupyter lab
# Ingresar a la url: http://localhost:8888/?token=XXXXXXXXXXXXXXXXXXXXX
```

## Introducción TensorFlow

Desde un notebook incluir la libreria

```python
import tensorflow as tf
```

La computación está descrita en terminos de flujo de datos en la estrucctura de un grafo dirigido, el uso directo de esta estructura en la v2 está obsoleto, sin embargo se pueden definir con el tf.Graph

Para un entorno más imperativo se introdujo la "ejecución ansiosa" (eager execution) la cual evalua las operaciones sin construir grafos: las operaciones retornan valores concretos en vez de construir un grafo computacional para ejecutar después

Sin embargo la principal estructura es la siguiente

- **Nodos:** se encargan de realizar la computación y tienen 0 o mas salidas, un nodo se representa mediante **Operaciones:** Una operación es una "computación abstracta" la cual toma tensores de entrada y produce tensores de salida, (ej: tf.add o el operador +, tf.matmult o el operador *)

- **Tensores:** son los datos que se mueven entre nodos son conocidos como tensores, los cuales son arreglos n-dimensionales de numeros reales (un tensor de dimensión 0 es un escalar, un tensor de dimensión 1 es un arreglo, uno de dimensión 2 es una matriz, etc)
   - **Tipos de dato en un tensor (dtype):** float32, int32, string y otros.
   - **Shape**: Representa la dimensión de la información  

TensorFlow ofrece distintos modulos para representar modelos matemáticos, redes neuronales, etc. Además ofrece compatibilidad con numpy

## Operaciones Básicas

Sumemos 2 vectores de enteros:

```python
a = tf.constant([2.5, 2])
b = tf.constant([3, 6], dtype=tf.float32)
total = tf.add(a, b)
```

> **tf.constant:** crea un tensor de constantes a partir de un array

TensorFlow en la v2 no es necesario crear una sesión la cual se encargue de ejecutar el grafo como en la v1 debiddo a que las operaciones se hacen al llamarse, es decir

```python
total = tf.add(a, b)
```
`total` tiene el resultado de la operación y no es necesario llamar más metodos, si imprimimos total obtenemos:

```python
print(total)
> tf.Tensor([5.5 8. ], shape=(2,), dtype=float32)
# Si se quiere ver como un valor de numpy podemos hacer
print(total.numpy())
> [5.5 8. ]
# de la misma forma podemos acceder a valores, si hacemos total[0] esto nos da
print(total[0])
> tf.Tensor(5.5, shape=(), dtype=float32)
# si queremos saber el valor de python podemos convertir un tensor usando int,list,etc
```

Las variables en cambio permiten alterar el valor que contienen y esto se puede realizar mediante el metodo `.assign(val)`

```python
x = tf.Variable(0, dtype=tf.float32)
print(x.numpy())
x.assign(3)
print(x.numpy())
```

## Resolviendo Sistemas de Ecuaciones

Esto se realiza mediante el modulo **linalg** el cual permite operaciones de algebra lineal, y ofrece un [solver](https://www.tensorflow.org/api_docs/python/tf/linalg/solve) para facilitar la solución de sistemas de ecuaciones, *se requiere un minimo de 2 ecuaciones*

```python
#AX = B

#Ecuaciones

# -1*x_1 + 3*x_2 = 7
# 3*x_1 + 2*x_2 = 1

A = tf.constant([
    [-1, 3],
    [3, 2]
], dtype=tf.float32)

B = tf.constant([
    [7], [1]
], dtype=tf.float32)

result = tf.linalg.solve(
    A, B, adjoint=False, name=None
)

print(result.numpy())

```

Un sistema de ecuaciones también se podria resolver computando la inversa de A y realizando la multiplicacion con B, sin embargo no siempre se puede encontrar la inversa y el proceso es costoso; mientras tanto el solver es más rápido

```python
X = tf.matmul(tf.linalg.inv(A),B)
```

## Regresión Lineal con NumPy

Es un modelo matematico para aproximar la dependencia lineal muchos valores Xi y su respectivo valor en Y, es decir, buscamos una ecuación lineal que relacione los puntos (Xi,F(Xi)) minimizando el error de aproximación para que la recta se ajuste lo más que se pueda de igual forma a los puntos dispersos y asi poder realizar predicciones futuras.

Para realizar esto debemos hacer lo siguiente:
- Calcular el error cuadrado por medio de minimos cuadrados
- Generar la ecuación lineal con la pendiente y corte-y encontrados

Ejemplo:

numpy nos ofrece los siguientes metodos  
> - **unique(arr):** recibe un arreglo de numeros y lo retorna ordenado en orden ascendente sin repreticiones  
> - **polyfit(x,y,degree):** efectua el metodo de minimos cuadrados tomando x,y y un grado de polinomio (en nuestro caso 1) y retorna un arreglo de coeficientes  
> - **poly1d(arr):** recibe un arreglo de coeficientes y retorna una ecuación lineal donde al menos uno de los coeficientes no se multiplica por la variable dependiente, es decir:  
> > ```python
> > np.poly1d([1])     # equivale a '1'
> > np.poly1d([1,0])   # equivale a '1x + 0'
> > np.poly1d([1,1])   # equivale a '1x + 1'
> > np.poly1d([1,1,1]) # equivale a '1x + 1x + 1'
> > ```  
> > 
Esto es conveniente dado que poly1d retorna la pendiente y corte-y de la forma que necesitamos

Codigo de Ejemplo:
```python
import matplotlib.pyplot as plt
import numpy as np

x = [1, 2, 2.5, 3, 4]
y = [1, 4, 7, 9, 15]
plt.plot(x, y, 'ro')
plt.axis([0, 6, 0, 20])
unique_x = np.unique(x)
plt.plot(unique_x, np.poly1d(np.polyfit(x, y, 1))(unique_x))
plt.show()
```

### Referencias

- [TensorFlow Overview](https://www.tensorflow.org/api_docs/python/tf_overview)
- [TensorFlow Guide](https://www.tensorflow.org/guide)
- [NumPy Doc](https://docs.scipy.org/doc/numpy/index.html)
- [Introduction to the Python Deep Learning Library TensorFlow](https://machinelearningmastery.com/introduction-python-deep-learning-library-tensorflow/) (TensorFlow v1)
- [From Solving Equations to Deep Learning: A TensorFlow Python Tutorial](https://www.toptal.com/machine-learning/tensorflow-python-tutorial) (TensorFlow v1)
- [TensorFlow Linear with Estimators](https://github.com/tensorflow/docs/blob/master/site/en/tutorials/estimator/linear.ipynb)

### Para Revisar
- [Small Steps to TensorFlow r0.12](https://dataplatform.cloud.ibm.com/exchange/public/entry/view/0cdd9df0783b706f0c4b7e5a3a613803)
- [Use TensorFlow to predict hand-written digits](https://dataplatform.cloud.ibm.com/exchange/public/entry/view/b5eac6e919cd6fbcc5824de04a00ec65)