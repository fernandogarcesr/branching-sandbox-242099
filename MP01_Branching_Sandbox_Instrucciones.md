# Micropráctica individual — Branching Sandbox

**DevOps 2026 · Unidad 1 · Semana 02**

## Propósito

Experimentar de forma controlada cómo crece la divergencia entre ramas, provocar un **merge conflict real**, diagnosticarlo y resolverlo usando evidencia de Git.

La práctica sigue:

**Predicción → Acción → Evidencia → Interpretación → Decisión**

> Esta práctica se realiza en un repositorio ** —sandbox** — independiente. No uses todavía el repositorio de OrderFlow para provocar conflictos deliberadamente.

---

## Datos del estudiante

* Fernando Garces Rodriguez
* 242099
* Equipo Rojo
* 02/09/2026
* https://github.com/fernandogarcesr/branching-sandbox-242099.git

---

# Parte 0 — Crear el repositorio sandbox

Crea un repositorio nuevo, por ejemplo:

`branching-sandbox-tu-matricula`

Puedes crearlo primero en GitHub o trabajar localmente y agregar el remoto después.

En una carpeta vacía:

```bash
git init
git branch -M main
```

Crea un archivo llamado `config.txt` con este contenido:

```text
APP\_NAME=OrderFlow
TIMEOUT=30
LOG\_LEVEL=INFO
```

Después:

```bash
git add config.txt
git commit -m "chore: create baseline configuration"
```

Si ya creaste el repositorio remoto en GitHub:

```bash
git remote add origin <URL\_DE\_TU\_REPOSITORIO>
git push -u origin main
```

### Evidencia inicial

Registra:

* **Hash corto del commit baseline:** fe502ea
* **Resultado de `git log --oneline`:**

```fe502ea (HEAD -> main, origin/main, origin/HEAD) chore: create baseline configuration
0749ea9 Initial commit

```

---

# Parte 1 — Predicción

Antes de modificar nada, responde:

### 1.1

¿Qué esperas que ocurra si una rama modifica archivos distintos a `main`?

**Respuesta:**
no generaria ningun conflicto, ya que son arhivos distintos al integrarlos con un merge se guntan ambos.
---

### 1.2

¿Qué esperas que ocurra si dos ramas modifican **la misma línea del mismo archivo** de manera diferente?

**Respuesta:**
pues se guardarian normalmente en su rama por separado pero al intentar hacer merge como hay dos valores
distintos en la misma linea, tendrias que elegir manualmente que se queda.
---

### 1.3

¿Cuál crees que será más costoso de integrar: una rama con pocos cambios o una rama que permaneció separada mientras `main` siguió cambiando? ¿Por qué?

**Respuesta:**
serua mas costoso la rama que estuvo separada mientras main hiba cambiando por que despues de un tiempo
con tantas modificaciones en el main al intentar hacer merge con tu rama abandonada va estar demasiado
diferente o cosas que tu tienes que fueron cambiadas por completo y eso generaria muchos conflictos mientras que si al integrar una rama con pocos cambios es mas facil ya que rapidamente hicsiste cambios y hiciste el merge.
---

# Parte 2 — Experimento A: rama corta sin conflicto

El objetivo es observar una integración sencilla.

Desde `main`:

```bash
git switch main
git switch -c feature/add-owner
```

Agrega una nueva línea al final de `config.txt`:

```text
OWNER=team-a
```

Haz commit:

```bash
git add config.txt
git commit -m "feat: add service owner"
```

Regresa a `main`:

```bash
git switch main
```

En `main`, crea un archivo diferente llamado `README.md`:

```text
# Branching Sandbox
Repositorio para observar estrategias de integración.
```

Haz commit:

```bash
git add README.md
git commit -m "docs: add sandbox readme"
```

Ahora integra la rama:

```bash
git merge --no-ff feature/add-owner
```

## Observa

Ejecuta:

```bash
git status
git log --graph --oneline --all
```

### Registra

* ¿Git pudo integrar automáticamente? **Sí / No** Si
* ¿Hubo conflicto? **Sí / No** No
* ¿Cuántos commits tenía la rama antes del merge? 1
* ¿Por qué crees que Git pudo o no pudo resolverlo automáticamente?

**Observación:**
fue automatico ya que eran archivos
distintos y no hubo ningun conlicto y lo pudo integrar facilmente.
---

# Parte 3 — Experimento B: provocar un merge conflict

Ahora vas a crear un conflicto **intencional y reproducible**.

Primero asegúrate de estar en `main`:

```bash
git switch main
```

Crea una nueva rama:

```bash
git switch -c feature/change-timeout
```

En esa rama cambia esta línea de `config.txt`:

```text
TIMEOUT=30
```

por:

```text
TIMEOUT=45
```

Haz commit:

```bash
git add config.txt
git commit -m "feat: increase timeout to 45"
```

Ahora regresa a `main`:

```bash
git switch main
```

En `main`, cambia **la misma línea**:

```text
TIMEOUT=30
```

por:

```text
TIMEOUT=60
```

Haz commit:

```bash
git add config.txt
git commit -m "fix: increase timeout to 60"
```

Ahora intenta integrar:

```bash
git merge feature/change-timeout
```

## Resultado esperado

Git debería detener el merge e indicar un conflicto parecido a:

```text
CONFLICT (content): Merge conflict in config.txt
Automatic merge failed; fix conflicts and then commit the result.
```

> El texto exacto puede variar ligeramente según tu versión de Git. Lo importante es que Git no pueda decidir automáticamente cuál versión conservar.

---

# Parte 4 — Diagnosticar el conflicto antes de resolverlo

**No edites el archivo inmediatamente.**

Primero recopila evidencia.

Ejecuta:

```bash
git status
```

Después abre `config.txt`.

Deberías observar marcadores similares a:

```text
<<<<<<< HEAD
TIMEOUT=60
=======
TIMEOUT=45
>>>>>>> feature/change-timeout
```

## Responde antes de corregir

### 4.1

¿Qué representa `HEAD` en este conflicto?
la rama en la que me encontraba cuando hice el merge que fue el main.

---

### 4.2

¿Cuáles son las dos versiones que Git no pudo reconciliar?
la de main con TIMEOUT-60 como HEAD y la de feature/change-timeout con TIMEOUT=45 como incoming

---

### 4.3

¿Por qué Git no debería decidir automáticamente si `TIMEOUT` debe ser 45 o 60?
por que ambos valores pueden ser validos, no hay error de sintaxis que le pueda hacer elegir por uno
correcto por lo que dependera del contexto y alguien tendra que solucionarlo y elegir manualmente.



---

### Evidencia

Pega aquí la parte relevante de: git status
On branch main
Your branch is ahead of 'origin/main' by 1 commit.
(use "git push" to publish your local commits)

You have unmerged paths.
(fix conflicts and run "git commit")
(use "git merge --abort" to abort the merge)

Unmerged paths:
(use "git add <file>..." to mark resolution)
both modified:   config.txt

no changes added to commit (use "git add" and/or "git commit -a")



---

# Parte 5 — Resolver el conflicto

Aquí no existe una única respuesta técnica correcta.

Debes tomar una **decisión de ingeniería**.

Puedes decidir, por ejemplo:

* conservar `TIMEOUT=45`;
* conservar `TIMEOUT=60`;
* elegir otro valor, si puedes justificarlo.

Edita `config.txt` y elimina completamente:

```text
<<<<<<<
=======
>>>>>>>
```

El archivo debe quedar con una sola decisión.

Ejemplo:

```text
APP\_NAME=OrderFlow
TIMEOUT=60
LOG\_LEVEL=INFO
OWNER=team-a
```

Después:

```bash
git add config.txt
git status
```

Si Git indica que los conflictos están resueltos:

```bash
git commit -m "merge: resolve timeout conflict"
```

Finalmente:

```bash
git log --graph --oneline --all
```

Si tienes remoto:

```bash
git push
```

---

# Parte 6 — Evidencia de resolución

Registra:

* ** —Valor final elegido para `TIMEOUT`:** —60
* **Hash del commit de resolución:**  9dabb3e
* **¿Por qué elegiste ese valor?**
pues por que era el valor que ya tenia el main, fue el cambio mas reciente de la rama principal.
tambien por que un TIMEOUT de 60 da mas margen de tolerancia ante un peticion antes de que falle sin saber
el contexto.

---

Pega la salida final de: git log --graph --oneline --all

* 9dabb3e (HEAD -> main) merge: resolve timeout conflict
|  
| \* 1b3797a (feature/change-timeout) feat: increase timeout to 45
* | 4fd5b4e fix: increase timeout to 60
|/
* f0067ff (origin/main, origin/HEAD) Merge branch 'feature/add-owner'
|  
| \* 83d1752 (origin/feature/add-owner, feature/add-owner) feat: add service owner
* | 3d5255b docs: add sandbox readme
|/
* fe502ea chore: create baseline configuration
* 0749ea9 Initial commit



# Parte 7 — Experimento C: aumentar la divergencia

Ahora observa qué ocurre cuando una rama acumula más historia antes de integrarse.

Crea una nueva rama desde `main`:

```bash
git switch main
git switch -c feature/long-lived
```

Haz **al menos 3 commits diferentes** en esa rama.

Ejemplo:

1. cambiar `LOG\_LEVEL`;
2. agregar una variable `REGION`;
3. agregar una variable `RETRIES`.

Mientras tanto, vuelve a `main` y realiza también **al menos 3 commits**.

Procura que por lo menos uno de los cambios de `main` afecte una zona cercana o la misma línea que modificaste en `feature/long-lived`.

Después intenta integrar:

```bash
git switch main
git merge feature/long-lived
```

> Puede aparecer uno o varios conflictos. Si no aparece ninguno, eso ** —no invalida la práctica** —: Git puede integrar automáticamente muchos cambios. Lo importante es analizar la divergencia y comparar la cantidad de trabajo necesario para verificar la integración.

Resuelve cualquier conflicto siguiendo el mismo proceso:

```text
git status
→ inspeccionar
→ decidir
→ editar
→ git add
→ git status
→ git commit
```

---

# Parte 8 — Comparación

Completa la tabla.

|Aspecto|Rama corta|Rama con mayor divergencia|
|-|-|-|
|Número de commits antes del merge|1 (`vfeat: add service owner`)|3 (`change log level`, `add region`, `add retries`)|
|Cambios concurrentes en `main`|1 commit (`docs: add sandbox readme`, archivo distinto)|3 commits (`set log level to warn`, `add max connections`, `enable cache flag`)|
|¿Hubo conflicto?|No|Si|
|Número de conflictos|0|1 (línea `LOG\_LEVEL`)|
|Dificultad para entender qué conservar|Ninguna, git lo integor automaticamente|Media, hubo que decidir manualmente entre WARN y DEBUG, y reconstruir el archivo porque el blog de notas dañó los saltos de línea|
|Evidencia usada para decidir|`git status`, `git log --graph --oneline --all`|`git status`, marcadores de conflicto en `config.txt`, `git log --graph --oneline --all`|

---

# Parte 9 — Interpretación

Responde con base en **lo que observaste en tu repositorio**.

### 9.1

¿Una rama larga siempre genera conflictos?
pues no necesariamente, genera conflicto si el main y esa rama modificaron las mismas lineas en los mismos archivos. aunque entre mas tiempo dure la rama mas probabilidades es que entre en conflicto de integracion por los distintos cambios.

---

### 9.2

Entonces, ¿por qué una rama de larga vida puede incrementar el costo de integración aunque Git logre hacer el merge automáticamente?
por que aunque se pueda hacer un merge no necesariamente estara del todo bien logicamente, despues de algunos commits puede que alla inconsistencia con duplicados, coherencia, etc. despues lo tendria que revisar una persona manualmente y seria muy tardado.

---

### 9.3

¿Qué relación observaste entre divergencia, cantidad de cambios y dificultad de integración?
una relacion directa ya que en la primer practica no hubo conflictos y la integracion fue automatica, en las segunda como hubo 3 commits de cada lado con choques en las mismas lineas si hubo conflicto y resolverlo tomo mas pasos y mas cuidado. entre mas commits se vallan acumulando en cada rama antes de integrarse habra muchos mas cambios y que ambos crucen en la misma linea.

---

### 9.4

¿Por qué un merge exitoso no demuestra por sí solo que el software sigue siendo correcto?
por que git solo verifica que el codigo o texto de los archivos se pueda integrar aunque no sea logico, no checa si el codigo seguira compilando despues de ese merge.

---

# Parte 10 — Decisión de ingeniería

Supón que tu equipo integra y despliega cambios diariamente.

### Decide

¿Preferirías trabajar con ramas de vida corta o ramas de larga duración?
Tu respuesta debe incluir:

1. **decisión**;
con ramas de vida corta.
En el experimento A, una rama con 1 commit se integro al main sin ningun conflicto, en el C una rama con 3 commits que vivio mas tiempo mientras main tambien hacia cambios genero un conflicto que se tuvo que analizar y resolver manualmente, además de un problema adicional al reconstruir el archivo por el blog de notas que dañó los saltos de línea , friccion que no habría existido con una integracion más frecuente y de menor alcance.
2. **beneficio esperado**;
si se integra y despliegan cambios diariamente o muy constantemente, las ramas de vida corta significa menos cambio acumulado para integrar, lo que reduce la probabilidad de conflictos y la dificultad de tener que estar revisandolos manualmente cuando ocurren, y pues si ocurren problemas se detectan mucho antes envez de descubrir muchos acumulados de golpe.
3. **trade-off o costo aceptado**.
pues estar integrando con frecuencia implica mas interrupciones en el trabajo individual, tener que estar haciendo mas merge, y cada rama enfocada en un cambio pequeño en vez de agregar varias funcionalidades en una rama antes de integrarla.

---

# Parte 11 — Uso de IA

Si utilizaste una herramienta de IA durante la práctica, registra:

* **Herramienta:** Claude
* **Prompt relevante:**  casi todo fue de problemas en la terminal o algunas dudas.
1. pero me sale asi despues de hacer el push desde el deskopt, igual confirmo el log final con el git log -- online?
2. le pedi ayuda con esta pregunta por que me confundi -> Si una rama toca un archivo distinto al que toca main, ¿esperas que Git tenga problema para integrarlos?
3. al intentar integrar la rama con el comando de las instrucciones y me salio esto
* **Qué parte de la respuesta verificaste:**
1. Sí, puedes confirmar el git log --oneline ahora mismo — el commit sí se guardó correctamente en tu máquina (\[main fe502ea] chore: create baseline configuration), eso ya quedó bien. El único problema es el push, que es aparte: guardar el commit localmente y subirlo a GitHub son dos pasos distintos.
2. Imagina el archivo config.txt con la línea TIMEOUT=30.
En la rama feature-x, cambias esa línea a TIMEOUT=45 y haces commit. Esa rama guarda su propia versión, sin problema — dentro de su propia historia todo está bien.
Mientras tanto, en main, alguien más (o tú mismo) cambia esa misma línea a TIMEOUT=60 y también hace commit. También sin problema, dentro de main.
3. Todo va bien — no es ningún error. Lo que pasó es que, al usar --no-ff, Git necesita un mensaje de commit para el merge (porque va a crear un commit nuevo explícito), así que abrió el editor de texto por defecto (esto es Vim, se reconoce por esas líneas con \~) con un mensaje ya preescrito: Merge branch 'feature/add-owner'.

No tienes que escribir nada — ese mensaje ya está bien tal como está. Solo necesitas guardar y salir

* **Qué cambiaste o descartaste:**
1. pues si lo segui como me dijo, solo tenia duda de hacer el push en el online
2. pues se guardarian normalmente en su rama por separado pero al intentar hacer merge como hay dos valores distintos en la misma linea, tendrias que elegir manualmente que se queda.
Si no utilizaste IA, escribe:
3. pues segui las instrucciones ya que tenia duda y me funciono correctamente

`N/A`

> Una respuesta generada por IA no sustituye la evidencia del repositorio ni la explicación individual.

---

# Entrega

Entrega **individual** en iVirtual:

1. este archivo `.md` completo;
2. URL de tu repositorio sandbox;
3. el repositorio debe conservar la historia de commits utilizada en los experimentos;
4. identifica en el documento el hash:

   * del baseline;
   * del merge/resolución principal.

No es necesario entregar capturas si la evidencia puede reconstruirse desde Git.



## Si te equivocas durante el experimento

No borres el repositorio inmediatamente.

Primero intenta identificar el estado con:

```bash
git status
git log --graph --oneline --all
```

Si iniciaste un merge y quieres cancelarlo **antes de completar la resolución**:

```bash
git merge --abort
```

Después analiza qué ocurrió y vuelve a ejecutar el experimento.

El objetivo no es obtener un historial “bonito”; es demostrar que puedes **leer el estado del repositorio, diagnosticar un conflicto y tomar una decisión consciente para resolverlo**.

