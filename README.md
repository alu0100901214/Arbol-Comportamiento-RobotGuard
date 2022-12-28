# Arbol Comportamiento RobotGuard
## Hacer el que el NPC vuelva a la última posición donde vio al jugador antes de huir. Debe volver después de que su salud se haya recuperado por encima de 40.

Para realizar esta tarea, primero he creado 2 Tasks dentro de AI.cs, primero "SaveLastPlayerPosition" guarda la posición del jugador en el momento en el que hace el NPC hace "Flee":

```
    [Task]
    public void SaveLastPlayerPosition() {
        lastPlayerPos = player.position;
        Task.current.Succeed();
    }
```

y la segunda Task "SetLastPlayerDestination" selecciona como destino la posición guardada:

```
    [Task]
    public void SetLastPlayerDestination() {
        if(lastPlayerPos != null)
            agent.SetDestination(lastPlayerPos);
        Task.current.Succeed();
    }
```

Luego, en el árbol de comportamiento 





GIF con un ejemplo de la ejecución:

![gif](./GIF/obstaculos.gif)
