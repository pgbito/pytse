# pytse
Python Wrapper para interactuar con el sitio web de servicios electorales del Tribunal Supremo de Elecciones a través de Web Crawling
**Pytse** (Tribunal Supremo de Elecciones) es un wrapper  que permite interactuar con la pagina web **[Servicios electorales](https://servicioselectorales.tse.go.cr/chc/)** desde Python.

## Documentacion / Ayuda
Podés encontrar ayuda **[aca](https://pytsecr.readthedocs.io/)**, abrir un **[Issue](https://github.com/pgbito/pytsecr/issues/new)** en este repositorio, o contactarme en **[Mi Telegram](https://t.me/pgbito)**
## Ejemplo: Buscar cumpleaños siguientes:
```csv
#input.csv
carlos,alvarado,quesada
rodrigo,chaves,robles
jose maria,figueres,ferrer
```
```python
import csv
from datetime import datetime
from pytse.tse import PyTse

client = PyTse()

with open('input.csv', newline='') as csvfile:
    spamreader = csv.reader(csvfile, delimiter=',', quotechar='|')

    response = client.multiple_find(spamreader) # Formato tuple[dict] (El diccionario posee toda la informacion de la busqueda)

    rows = set(map(lambda q:(q.get("Nombre") + ' ' + q.get('Apellidos'), q.get("Fecha de Nacimiento")), response)) # Formato: (Nombre, Fecha Nacimiento)



    nacimiento_a_cumpleanos = lambda i: (i[0], datetime.strptime(i[1], '%d/%m/%Y').replace(year=2023))

    rows = sorted(tuple(
            map(
                nacimiento_a_cumpleanos, rows
            ) ), key=lambda x: x[1])# Cambia las fechas de nacimiento a cumpleaños y las ordena de la mas cercana a la mas lejana

    now = datetime.now()
    ya_cumplieron = tuple(filter(lambda row: row[1] < now, rows))  # se fija en las personas que ya cumplieron
    cumpliran = tuple(filter(lambda row: row not in ya_cumplieron, rows))


    def diferencia(original_date, now):
        delta1 = datetime(now.year, original_date.month, original_date.day)
        delta2 = datetime(now.year + 1, original_date.month, original_date.day)

        return ((delta1 if delta1 > now else delta2) - now).days


    for nombre, dt in cumpliran:
            c = diferencia(dt, now)
            print(("Dias para el cumpleaños de {name}:" + str(c) + " days").format(name=nombre))


```
## Instalar pytse (beta)

Pytse esta disponible en PyPi 

```console
python3 -m pip intall pytsecr
```

O incluirlo directamente en aplicaciones:

```console
mkdir miapp
cd miapp
git clone https://github.com/pgbito/pytsecr . 
python3 example.py
```


