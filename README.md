# Hackaton Deep Learning

Departamento de Ciencias de la Computación
Universidad de Chile 
Noviembre, 2022

## Integrantes
* Jorge Ortiz Fuentes
* Camila Labarca

En esta actividad intentaremos resolver un problema de clasificación multimodal. En un problema de clasificación multimodal, cada pieza de información viene en diferentes representaciones (imágenes, texto, audios, etc) y la idea es determinar cómo usar esos datos para un problema de clasificación. 

## El problema
El problema consiste en entrenar un modelo que clasifique instancias del dataset CUB de la mejor manera posible. Algunas preguntas que guiaron nuestro desarrollo son:

* ¿Qué performance se obtiene solamente clasificando las imágenes?
* ¿Qué performance se obtiene solamente clasificando los textos?
* ¿Qué performance se obtiene si combino modelos de textos e imágenes?

## Experimentos

Primero, se leyeron los datos y se cargaron en dos dataframes de pandas. Véase el archivo "1_Read data.ipynb". 

En segundo lugar, se probaron cuatro clasificadores de imágenes con distintos hiperpámetros. En la siguiente tabla se enumeran los experimentos realizados:

| **Models**                                        	| **Preprocess**                	| **Epochs** 	| **Loss function** 	| **Test Accuracy** 	|
|---------------------------------------------------	|-------------------------------	|------------	|-------------------	|-------------------	|
| Finetuning microsoft/swin-tiny-patch4-window7-224 	| Resized                       	|        100 	| Cross-Entropy     	|              0.74 	|
| google/vit-large-patch32-384                      	| Resized                       	|        100 	| Cross-Entropy     	|              0.79 	|
| google/vit-large-patch32-224-in21k                	| Resized and data augmentation 	|        100 	| Cross-Entropy     	|              0.79 	|
| google/vit-large-patch32-384                      	| Resized and data augmentation 	|         30 	| Cross-Entropy     	|              0.79 	|

Se desprende que VIT large obtiene el mejor performance. Se probaron distintas maneras de utilizar VIT, sin embargo, los resultados solo tuvieron diferencias en milésimas. 

En tercer lugar, se probaron distintas arquitecturas para clasificar los textos. En la siguiente tabla se enumeran los experimentos realizados:

| **Models**                              	| **Preprocess** 	| **Epochs** 	| **Loss function** 	| **Test Accuracy** 	|
|-----------------------------------------	|----------------	|------------	|-------------------	|-------------------	|
| sentence-transformers/all-mpnet-base-v2 	| lower          	|        100 	| Cross-Entropy     	|              0.56 	|
| bert-base-uncased                       	| lower          	|        100 	| Cross-Entropy     	|              0.56 	|
| roberta-base                            	| lower          	|        100 	| Cross-Entropy     	|              0.56 	|

En cuarto lugar, se combinaron modelos de textos e imágenes. Para ello se utilizaron los mejores modelos de textos e imágenes, se extrajo la última capa de vectores antes de la predicción de las redes, se concatenaron estos output y se entrenaron MLP multimodales. Los modelos MLP se entrenaron usando GridSearch hasta encontrar la mejor arquitectura para obtener el máximo de accuracy. Tales resultados performaron mejor que los experimentos anteriores, tal como se muestra en la siguiente tabla:

| **Models**        	| **Epochs** 	| **Arquitectura**                                                                                                        	| **Test Accuracy** 	|
|-------------------	|------------	|-------------------------------------------------------------------------------------------------------------------------	|-------------------	|
| swin+sbert        	|         20 	| {'activation': 'tanh', 'alpha': 0.05, 'hidden_layer_sizes': (1000, 500), 'learning_rate': 'adaptive', 'solver': 'adam'} 	|              0.81 	|
| roberta+bit large 	|         20 	| {'activation': 'tanh', 'alpha': 0.05, 'hidden_layer_sizes': (1000, 500), 'learning_rate': 'adaptive', 'solver': 'adam'} 	|              0.86 	|

## Discusión

A partir de los experimentos se observa que las arquitecturas combinadas de texto e imágenes permiten obtener los mejores resultados para resolver el problema.

Los modelos de predicción de imágenes obtienen resultados superiores a un 70% de accuracy. Por lo tanto, se desprende que las etiquetas pueden ser predichas principalmente mediante las imágenes. En específico, la arquitectura que funcionó mejor fue VIT. Sin embargo, no hubo diferencias si se aplicaban técnicas de data augmentation o si se finetuneaban modelos con más parámetros.

Los modelos de predicción de textos obtuvieron resultados peores que los modelos de imágenes. Se utilizaron 3 arquitecturas basadas en Transformers y todas obtuvieron resultados relatiavmente iguales, cercanos a 54% de Accuracy.

Aunque la predicción de textos no performó bien por si misma, si sirvió como complemento para mejorar la accuracy de los modelos de imágenes. El mejor resultado de los modelos multimodales fue de 86% de Accuracy utilizando una red MLP con dos capas ocultas de tamaño 1000 y 500 respectivamente. 

## Conclusión

En conclusión, se desprende que combinar vectores multimodales de modelos finetuneados basados en Transformers permite obtener mejores resultados que el entrenameinto de modelos separados. Esto se puede explicar debido a que la concatenación de los vectores permite aunar distintos tipos de conocimientos que le permiten a la MLP tomar decisiones basadas en información multimodal.











