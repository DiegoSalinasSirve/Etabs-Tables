# Etabs-Tables

#### A traves de la Api de Etabs es posible extraer y modificar practicamente cualquier tabla de este. 

#### En primer lugar es necesario importar las siguientes librerias 
```
import pandas as pd
import io
```

#### La extracción de tablas puede realizarse a traves del método GetTableForDisplayCSVString de la sub clase DatabaseTables, como se muestra en la siguiente función:

```
def get_table(sap_model, table_name):
    # Extraer csv
    csv = \
        sap_model.DatabaseTables.GetTableForDisplayCSVString(table_name,
                                                            "", "All")[2]
        
    # Covertir csv a data frame
    table = pd.read_csv(io.StringIO(csv), sep = ",", na_filter= False)
    
    return table
```

#### La modificación de tablas puede realizarse a traves del método SetTableForDisplayCSVString de la sub clase DatabaseTables. Esta modificación puede o no ser valida. Para verificar esto se retorna una variable ret la cual en su ultimo termino contiene un 0 si la modificación se realizo con exito. La siguiente función muestra como realizar este proceso:

```
def set_table(sap_model, table_name, table):
    # Convertir data frame a csv 
    csv = pd.DataFrame.to_csv(table, sep = ",", index=False)
    
    # Modificar tabla en Etabs
    ret = sap_model.DatabaseTables.SetTableForEditingCSVString(table_name, 0, csv)
    
    return ret[-1]

```

#### La modificación de tablas no incluye la aplicación de estos cambios en Etabs. Para aplicar estos cambios de debe utilizar el método ApplyEditedTables de la sub clase DatabaseTables. Esta aplicación puede o no ser valida. Para verificar esto se retorna una variable ret la cual en su ultimo termino contiene un 0 si la aplicación se realizo con exito. La siguiente función muestra como realizar este proceso:

```
def apply_tables(sap_model):
    # Aplicar cambios en tablas 
    ret = sap_model.DatabaseTables.ApplyEditedTables(True)
    
    return ret[-1]
    
```

#### Es importante destacar que el separador utilizado en el csv exportado por Etabs es "," por lo que se pueden generar problemas en dos ocaciones: 1)El computador tiene definido que identificador de decimales la "," en lugar de ".", 2) Existe algun nombre definido en Etabs que contiene el caracter ",".
