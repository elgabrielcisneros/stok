---
description: Make commit
agent: build
---

# Commits - Como deben ser

Segun los cambios hechos, haz commit de ellos. El formato del primer mensaje es en ingles, inicia con un titulo corto sin un tag del tipo fea, fix, docs, etc y empezando en mayusculas (ej: "Fix + lo que se arreglo", "Add + lo que se agrego") y un segundo mensaje usando el -m una descripcion breve de menos de 50 caracteres iniciando con una sangria(ej: "- el procedimiento que se hizo y porque"). Despues de hacer commit, haz push, pero antes de hacer push 1) dile al usuario que ya hiciste commit de los cambios,  2) preguntale si puedes hacer push a la rama (sea ```git push``` o ```git push --set-upstream origin nombre-de-rama``` si es una nueva) y 3) dale dos opciones para seleccionar antes de continuar: **Si** para confirmar que si puedes hacer push y **No** para confirmar que no puedes hacer push a la rama. Si responde que **Si** haz push, si responde que **No**, termina de responder.

## Ramas - Como deben ser

Los nombres de las ramas deben ser asi:

``tipo-de-rama/nombre```

Ejemplos

- feature: ```feature/nombre-de-feature```.
- bugfix: ```bugfix/nombre-bugfix```.
- docs: ```docs/nombre-docs```.
