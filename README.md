# Importancia de las expresiones regulares en la limpieza de datos

Creo que muchos analistas y científicos de datos no dimensionan la importancia que tienen las expresiones regulares. No solo
para identificar patrones y crear condiciones basadas en ellos (como en el código que hice algunos años [Clasificación de correos y teléfonos usando Python](https://github.com/Cuadernin/class_tel_email/tree/main)) sino que simplifican muchísimo el trabajo arduo
de limpieza de datos.

Estás están presentes en un gran número de lenguajes de programación como _Python, R, SQL, Javascript_, entre otros, debido a que es una sintaxis universal para identificar carácteres en una cadena de texto.

A continuación presento dos casos de uso donde el saber expresiones regulares simplifica muchísimo el trabajo:

## Ejemplo 1 (Python):
Supongamos que tenemos los siguientes datos:
```py
CP = [5010,5060,5280,7500,5601,8905]
Direccion = ['Calle Allende 7 Queretaro 7500', 'Boulevard 7 8905 Zacatenco', 'Ignacio 8 Zacatecas', '5060Franciso I Madero 87',
            'Emeterio Arenas 5800 9 Toluca']
df = pd.DataFrame({'ID':[i for i in range(len(Direccion))],"Direccion":Direccion})
```

| ID      | Direccion |
| ----------- | ----------- |
| 0      | Calle Allende 7 Queretaro 7500       |
| 1   | Boulevard 7 8905 Zacatenco        |
| 2     | Ignacio 8 Zacatecas       |
| 3   | 5060Franciso I Madero 87        |
| 4      | Emeterio Arenas 5800 9 Toluca       |

Y se nos pide crear una nueva columna que contenga el CP de cada domicilio que siga lo siguiente:
- Si la dirección no tiene CP, la celda debe quedar vacía
- Si la dirección tiene un CP ajeno a los CP existentes (lista CP), la celda debe quedar vacía

| ID      | Direccion | CP  |
| ----------- | ----------- | ----------- |
| 0      | Calle Allende 7 Queretaro 7500       | 7500
| 1   | Boulevard 7 8905 Zacatenco        | 8905
| 2     | Ignacio 8 Zacatecas       | NaN
| 3   | 5060Franciso I Madero 87        |  5060
| 4      | Emeterio Arenas 5800 9 Toluca       | NaN


> :bulb: **Solución.** Hay muchas soluciones posibles, pero usando expresiones regulares queda de la sig. manera:
``` py
df['CP'] = df.Direccion.str.extract(r"(\d{4})").astype(str)
df.loc[~df.CP.astype(float).isin(CP),'CP'] = np.nan
```


## Ejemplo 2 (R):
Tenemos el siguiente dataset con muchas frases:

| Sent      
| ----------- | 
| The birch canoe slid on the smooth planks.       | 
| Glue the sheet to the dark blue background.   | 
| It's easy to tell the depth of a well.     | 
| These days a chicken leg is a rare dish.   | 
| Rice is often served in round bowls.      | 
| The juice of lemons makes fine punch. |
| The box was thrown beside the parked truck. |

Y deseamos conocer la distribución del total de **palabras** que existen 

> :bulb: **Solución.** Usando expresiones regulares queda de la sig. manera:
 
``` R
head(sents,7)

( sents_count <- sents %>% mutate(Sent = str_to_lower(str_remove_all(Sent,"[:punct:]"))) %>% 
    mutate(pals = str_count(Sent,"\\s+")+1) %>% count(pals) %>% arrange(desc(n)) )
```
  | pals |     n |
  | ----------- |  ----------- | 
| 8  | 245 |
|       7 |  188 |
|       9 |  150 |
|       6 |   56 |
|      10  |  54 |
|       11 |   15 |
|        5 |   10 |
|     12 |    2 |

En el último ejemplo abuse de la sintaxis de R usando [POSIX Character Classes](https://www.gastonsanchez.com/r4strings/character-sets.html) pero podría usarse la expresión [^\w\s]. No obstante, el punto es ver como el 
código tanto en Python como R es más limpio, legible y mucho más rápido en ejecución. 

**Cuanto más sepas usar expresiones regulares más fácil y exhaustiva será la limpieza de datos 🥰.**

