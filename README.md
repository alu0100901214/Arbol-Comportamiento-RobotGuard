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

Luego, en el árbol de comportamiento añado "SaveLastPlayerPosition" dentro de "Flee" en el momento en el que decide huir del jugador:

```
tree("Flee")
	sequence
		while IsHealthLessThan(40.0)
			while InDanger(50.0)
				sequence
					SaveLastPlayerPosition
					TakeCover
					MoveToDestination
```

Y en "LookAround" añado "SetLastPlayerDestination" y "MoveToDestination" para que busque primero al jugador en la posición en la que lo vió por última vez.

```
tree("LookAround")
	while not IsHealthLessThan(40.0)
		while not SeePlayer
			sequence
				SetLastPlayerDestination
				MoveToDestination
				random
					Turn(90.0)
					Turn(-90.0)
					Turn(145.0)
					Turn(-27.0)
				LookAtTarget
				WaitRandom(2.0,5.0)
				random
					Succeed
					Fail
```


GIF con un ejemplo de la ejecución:

![gif](./GIF/arbolComportamiento.gif)
