# Tutorial: Uso de Oracle Cloud Infrastructure (OCI) con servicios de IA GENERATIVA Y SELECTAI



Este tutorial te guiará a través de los pasos necesarios para trabajar con perfiles y modelos en una base de datos autónoma utilizando el SQL Web de OCI. Asegúrate de seguir cada paso cuidadosamente para completar con éxito las tareas.

## Prerrequisitos

Antes de comenzar, asegúrate de tener acceso a:
- Una **Base de Datos Autónoma** en OCI.
- Acceso al usuario `admin` <- **_IMPORTANTE_**
- El **SQL Web** habilitado `admin` para ejecutar consultas.

---

## Paso 1: Deshabilitar y habilitar el principal de recursos

Para comenzar, primero necesitamos deshabilitar y luego habilitar el **principal de recursos** para el usuario `ADMIN`. Esto nos asegurará que el entorno esté preparado para los siguientes comandos.

```sql
BEGIN
  dbms_cloud_admin.disable_resource_principal(username  => 'ADMIN');
END;
/
```

Ahora habilitamos el **resource_principal**:

```sql
BEGIN
  dbms_cloud_admin.enable_resource_principal(username  => 'ADMIN');
END;
/
```

---

# Paso 2: Crear un perfil de IA utilizando **_`Llama 3`_**

A continuación, crearemos un perfil de IA llamado `ociai_llama`. Este perfil utilizará el modelo **meta.llama-3-70b-instruct** para ejecutar tareas de inteligencia artificial sobre los datos de la base de datos.

Primero, eliminamos el perfil si ya existe:

```sql
BEGIN
    -- Elimina el perfil si ya existe
    DBMS_CLOUD_AI.drop_profile(
        profile_name => 'ociai_llama',
        force => true
    );     
END;
/
```

Luego, creamos el perfil con el modelo `meta.llama-3-70b-instruct` llamando las tablas requeridas para el lab:

```sql
BEGIN
    DBMS_CLOUD_AI.create_profile (                                              
        profile_name => 'ociai_llama',
        attributes   => 
        '{"provider": "oci",
            "credential_name": "OCI$RESOURCE_PRINCIPAL",
            "object_list": [
                {"owner": "SH", "name": "customers"},
                {"owner": "SH", "name": "sales"},
                {"owner": "SH", "name": "products"},
                {"owner": "SH", "name": "countries"}
            ],
            "model": "meta.llama-3-70b-instruct"
            }');
END;
/
```

Después de crear el perfil, necesitamos configurarlo para usarlo en nuestras consultas:

```sql
BEGIN
    DBMS_CLOUD_AI.SET_PROFILE(
        profile_name => 'ociai_llama'
    );
END;
/
```
---

# Paso 3: Generar consultas con el perfil de IA

Ahora que hemos configurado el perfil, podemos utilizarlo para generar respuestas y SQL basados en lenguaje natural.

## Consulta 1: ¿Cuántos clientes en San Francisco están casados?
Para obtener una respuesta narrativa:

```sql
SELECT DBMS_CLOUD_AI.GENERATE(
    prompt => '¿Cúantos clientes en San Francisco están casados?',
    profile_name => 'ociai_llama',
    action => 'narrate') AS chat
FROM dual;
```

Para generar la consulta SQL correspondiente:

```sql
SELECT DBMS_CLOUD_AI.GENERATE(
    prompt => '¿Cúantos clientes en San Francisco están casados?',
    profile_name => 'ociai_llama',
    action => 'showsql') AS query
FROM dual;
```

## Consulta 2: ¿Cuál es el mejor producto?

```sql
SELECT DBMS_CLOUD_AI.GENERATE(
    prompt => '¿Cuál es el mejor producto?, responde en español',
    profile_name => 'ociai_llama',
    action => 'narrate')
FROM dual;
```

## Consulta 3: ¿Qué es Oracle Autonomous Database? 

Es posible crear texto LLM usando la interfaz de Oracle SQL.

```sql
SELECT DBMS_CLOUD_AI.GENERATE(
    prompt       => 'what is oracle autonomous database, responde en español',
    profile_name => 'ociai_llama',
    action       => 'chat')
FROM dual;
```



---

## ¡Gracias por seguir este tutorial!

Espero que este tutorial te haya sido útil y te permita sacar el máximo provecho de las capacidades de Oracle Cloud Infrastructure en tus proyectos. Si tienes alguna duda o sugerencia, no dudes en contactarme. Estoy siempre dispuesto a apoyar en el proceso de transformación digital y adopción de tecnologías innovadoras.

**[Jorge Alexander Galán Méndez](https://www.linkedin.com/in/jorge-alexander-galan-mendez/)**  
**Ingeniero Electrónico especializado en Gerencia Empresarial**  
**Maestría en Inteligencia Artificial**  

¡Éxito en tu camino hacia la innovación tecnológica!

---



